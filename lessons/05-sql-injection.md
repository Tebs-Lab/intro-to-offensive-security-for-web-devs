# SQL Injection

SQL injection is an attack that occurs when user input is used to construct a SQL query, which is then executed. SQL injection can result in data exfiltration, local file inclusion, and more. SQLi is almost always a high severity vulnerability. Obviously, if an attacker can execute queries and retrieve data, then logins and other confidential information is at risk. Additionally, SQLi can provide opportunities for file inclusion which can lead to command execution, privilege escalation, and the ability to read files from the filesystem.

note only because of the value and risk present the data that is actually stored in the database, but also because it

## Objectives

* Describe SQLi and it's varieties.
  * Union based.
  * Error based.
  * Boolean based.
  * Time based.
* Directly execute union based SQLi with Burp, in the application, and via script.
* Use SQLMap to automate SQLi attacks.
* Describe tactics for defending and mitigating SQLi.

## Lesson Plan

### What is SQLi (10 minutes)

* SQLi happens when an attacker includes a SQL statement in some input to a server, which is then executed in the database.
  * The results might be sent directly to the attacker, but they don't have to be.
* Example of vulnerable code:

```python
# Assume this is in some login route on the webserver...
username = request.form.username
password_hash = hash(request.form.password)

sql_statement = f"select email, full_name from users where username='{username}' and password='{password_hash}';"

# sql_statement gets sent to the database to be executed...
  # Maybe the result is sent back as JSON...
  # Maybe the result is rendered into some HTML...
  # Also consider what happens if the SQL statement results in an error!
```

* **So, what's wrong with this code?**
  * We have no assurance that the inbound input is what we think it is.
  * The password_hash will definitely be a hashcode at this point, so that's not much of a risk.
  * But username is vulnerable.
* **Can you think of an example of a malicious value to send as username?**
  * Something that will cause an error: `'`
  * Bypassing the password requirement: `"other_user'; --"`
    * SQL becomes: `select email, full_name from users where username='other_user'; --' and password='doesnt matter';`
      * Note the use of the single quote to close the quote in the python code.
      * Note the use of semicolon and SQL comment `--` to ignore the rest of the statement.
  * Select (enumerate) tables from information schema: `"unlikelyusernamexxx' union select table, schema from information_schema.tables limit 1 offest 1; --"`
    * results in:
    ```
    select
      email,
      full_name
    from users where username='unlikelyusernamexxx'
    union select
      table,
      schema
    from information_schema.tables limit 1 offest 1; --' and password='doesnt matter';
    ```
    * With patience and automation we can dump the whole database this way.


* In some cases, stacked queries will work, allowing updates, deletions, or inserts!
  * For example: `"username'; delete * from users cascade; --"`
    * Ouch.

### Indirect Access: Boolean, Time, and Error Based SQLi (15 minutes)

* Sometimes, a SQL query won't directly render or return the information that results from the SQL query.
  * For example, most login routes just check if the login attempt succeeds, then redirect the user to some other route. Like the dashboard page.
  * However, we can still use the "failure" or "success" notification to extract information.
  * Additionally, if we can tell the difference between a "no un/pass match" and an injection that causes an error we may also be able to extract information.
* For "boolean based" consider an injection that results in this SQL:
  ```
  select email, full_name from users where username='target_username' and email like 'a%' and password='doesnt matter';

  -- then next, if we got a "false" response e.g. "no such user" we try to find out if email starts with a b:
  select email, full_name from users where username='target_username' and email like 'b%' and password='doesnt matter';

  -- or, if we got a "true" response e.g. "password doesn't match" we know the email starts with an a and we look at the next letter
  select email, full_name from users where username='target_username' and email like 'aa%' and password='doesnt matter';
  ```
  * We can repeatedly make a request like this to slowly figure out the users email address from their username.
    * Or dump password hashes, or combine this with sub-selects to slowly but surely dump the whole database!
    * This is one thing tools like SQLMap do.


* Error based is very much like boolean based, for example:
  * Say an error is thrown when a query results in 0 records being returned for some query, and the user gets a 500.
  * Now, we can use the same tactic as above but when we get a 500 we know it's "false"


* Time based is also very similar to the above.
  * When an error occurs, or a "false" occurs it often takes longer to execute than for a "true" result or a non-erroring result.
  * This fact remains true even when the webserver doesn't deliver any explicit information to the user,
    * For example, just "failed login" rather than "no such user" / "password doesn't match"
    * For example, server 500's silently and just returns the same result as a standard failed login.
  * You might THINK you're safe because your attacker doesn't get any explicit information, but clever automated programs can measure the difference in latency and *infer* that a particular injection was a "success" or a "failure".

### Execute Simple SQLi (5 minutes)

* Step 1 is to probe for vulnerability, a simple test is to include input that is very likely to cause an error on the server.
  * Examples: `'`, `"`, `#`, `--`
  * SQLMap and others can automate this process.
  * *Demo this error causing behavior on DVWA*
* Step 2: So we know it's vulnerable, lets see if it will deliver MORE information (no limit on sever side...)
  * payload: `' or '0'='0`
  * Tuh-duh, we get lots of results.
* Step 3: Well, we know we get LOTS of results... so lets union all!
  * We have observed that 2 pieces of data are returned in the original query, so we have to match.
  * payload: `' UNION SELECT TABLE_NAME, TABLE_SCHEMA from information_schema.tables; -- `
    * Note, the final space is significant, some DBs do not recognize comments unless they have a space on both sides!
  * Now, we can use what we just learned (all the tables) to dump the rest of the database if we want!

### Execute Blind SQLi (10 minutes)

* We can test for vulnerability in similar ways...
  * First, try bogus input... looks like errors result in "user not there"
  * So, lets try something we KNOW should work and inject a falsehood, consider these two payloads:
    * `1 and 1=0` and `1 and 1=1`
      * if there were no injection, both would return FALSE -> neither of these two are valid "ids"
      * But if injection IS an option, they'll be executed as SQL. 1 isn't 0 so the first says "false" and 1=1 so the second is true!


* Vulnerability is established... we could start to dump some information.
  * Say we want to try to get usernames from ids:
  * `1' and user like 'a%' -- `
  * `1' and user like 'ab%' -- `
  * ... so on until ad when we get another true!


* We could go on doing this manually, but it'd be quite tedious.
* Lets us a tool! SQLMap is a tool for testing SQLi.
  * Things to note:
    * we need to provide a url `-u` option
    * we need to be authenticated to get to this page — SQLMap needs our cookie. `--cookie=` option.
    * We already know which param to inject, so we can specify it `-p` option.
    * We know we're looking at a Boolean type injection so we can tell SQLMap to start with that `--technique` option
    * You can just let SQLMap do it's think though with default options it will try lots of tactics.
  * Basic: `sqlmap -u "http://127.0.0.1/DVWA/vulnerabilities/sqli_blind/?id=1&submit=Submit" --cookie=`  (get the correct cookie value from Burp)
  * More Specific: add `-p id --technique B`

* Answer SQLMaps Y/N questions...
  * Looks like ID is vulnerable... Now we can see SQLMaps real power...
  * Add `--dbs` to get the list of databases
    * SQLMap remembers the vulnerability type and which param, so it doesn't search again.
    * Note that we got 2 DB's back (information_schema and dvwa)
  * Cool, what if we want the tables in the db?
    * add: `-D dvwa --tables`
  * Okay, what about the values in one of the tables?
    * add `-D dvwa -T users --dump`
  * Note that SQLMap even offers to crack the password hashes with a dictionary attack!


* SQLMap is a powerful tool, it can do a lot, so we're not going to demo much more but there are a lot of tutorials online.

### Mitigating SQLi (10 minutes)

* Use prepared statements with parameterized queries.
  * SQLi originates because the web server sends strings representing the complete query to the database.
  * Every programming language -> SQL library worth using provides a mechanism to provide the query and the parameters for that query separately.
    * This way the user input is always treated just as data, rather than a possible addendum to the string.
    * For example, our malicious payload `1' and 1=1'` would be rendered into a properly escaped string:
      * `"1' and 1=1'"`
      * Then that value is compared to the values in the database, rather than the second half being treated as a separate comparison.
  * **This is the best mitigation, as it never allows user input to be rendered into queries directly!**
  * You can use store procedures to similar effect as prepared statements.
  * Most ORMs use stored procedures and are not vulnerable to SQLi.


* Validate and escape user input.
  * Essentially, filter out and disallow important SQL values such as ', ", --, etc.
  * *This is only a mitigation, and should not be a replacement for prepared statements!*


* Follow the principle of least privilege.
  * The DB user for the web-app should not have access to anything more than it needs.
  * This way, if you have a SQLi vulnerability, you'll reduce the access the attacker has to critical tables and systems.
  * Some things to consider:
    * Definitely disallow the DB from reading/writing files to the filesystem.
    * Limit access to information_schema and other sensitive tables.
    * Use views to create limited subsets of data where relevant.
    * Don't run the web app or database as root!!!!

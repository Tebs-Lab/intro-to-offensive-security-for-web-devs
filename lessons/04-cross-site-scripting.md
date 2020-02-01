# Cross Site Scripting

Cross site scripting occurs when a website does not properly escape input that contains JavaScript. This attack vector is dangerous when a user can cause their payload to be executed by other users. XSS exposes users to attacks like cookie and session jacking, key-logging (within the compromised website), as well cryptojacking or other forms of processor theft.

The JS still runs in the browser of an end user, rather than on the server or directly on the end users machine. XSS can be considered critical severity depending on what information might be compromised. In the context of banking software, session jacking is quite severe indeed! In the context of a web forum, the risk is probably more moderate.

## Objectives

* Define Cross Site Scripting
* Describe the difference between reflected and stored XSS.
* Execute both types of XSS attack.
* Describe how to mitigate XSS.

## Lesson Plan

### What is XSS (5 minutes)

* **What is Cross Site Scripting?**
  * An attack that occurs when an attacker can somehow cause their own JS to execute on a website.
  * 2 main kinds "reflected" and "persistent"
    * Reflected means the request itself contains something malicious (like a poisoned query param).
    * Persistent means the server stored something poisonous.
  * Another "type" question is if this is DOM based XSS or not.
    * The difference is in when the malicious script is executed.
    * in DOM based, the script is inserted at runtime somewhere into the DOM then executed.
    * in non-DOM the script is run at page-load time, like the other "real" scripts for the page.
    * DOM based attacks may be invisible to the server, and may happen as the result of a hijacked AJAX request.

### Executing XSS in DVWA (3 minutes)

* Demonstrate reflected and persistent XSS against DVWA.

### Mitigating XSS (5-10 minutes)

* **How would you mitigate this attack?**
  * Always HTML & JS escape any user input that will be rendered ANYWHERE ON THE PAGE.
  * HTML elements and attributes are common attack vectors.
  * URL Query Parameters are attack vectors.
  * CSS values can be attack vectors.
  * And of course, anything rendered into a JS file is an attack vector as well.
    * (such as `eval`)


* Don't allow user input to be rendered unless it's absolutely necessary.
* Perform URL, HTML, and JS encoding before inserting data into a page.
  * escaping is context sensitive, so handling this at output time rather than input time is usually better.
* use `innerText` rather than `innerHTML`.
* use `encodeURIComponent` when adding user input to query params, and `decodeURIComponent` when reading them.
* use the DOM API when making changes to the DOM, rather than replacing the text and letting the browser re-parse it.
* validate user input when they send it (e.g. remove "<script>" and "javascript:" from user input).
* Use a strict Content Security Policy — never execute inline JS or JS from any websites aside from your own!

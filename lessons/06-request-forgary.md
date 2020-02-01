# Server Side Request Forgery and Cross Site Request Forgery

CSRF and SSRF both happens when some user is tricked into loading a URL provided by the attacker. In regular CSRF the URL might be loaded by another user's browser, and in SSRF that URL is loaded by the webserver itself. With CSRF the attacker essentially makes a web request with the privileges of some other user, with SSRF the impact is further exacerbated by allowing access to services running on localhost or inside the firewall. These aren't normally accessible from the public web. Plus such requests may also be made greater privileges.

## Objectives

* Describe server side request forgery.
* Execute an example of SSRF against DVWA.
* Describe mitigations for SSRF.

## Lesson Plan

### What Is Request Forgery? (5 minutes)

* Anytime an attacker can coerce another user to make a web request, we can consider it a "forged request"
* There are a number of ways this could occur, the simplest is simply tricking people to click on a bogus link:

```html
<a href="http://www.website.com/change_password?pw=hacked&pw2=hacked">www.website.com/home</a>
```

* This is especially dangerous when a user already has a cookie, and can take authenticated action.
* Vectors can include
  * fake images,
  * spam emails with trick links,
  * and abusing servers that act as proxies (accept a URL from an attacker, and make a request to that URL)

### Example CSRF in DVWA (10 minutes)

* Simplest example first:
  * Go to the CSRF page and change your password.
  * copy the HTML into a file
  * Modify the "action" to be our DVWA instance on localhost.
  * load it in the browser, change our password!

```html
<form action="http://localhost/DVWA/vulnerabilities/csrf/" method="GET">
New password:<br>
	<input type="text" autocomplete="off" name="password_new"><br>
	Confirm new password:<br>
	<input type="text" autocomplete="off" name="password_conf"><br>
	<br>
	<input type="submit" value="Change" name="Change">
</form>
```

* Second example, copy the full URL with the get params, and save it as the src to an image.

```html
<img src="http://localhost/DVWA/vulnerabilities/csrf/?password_new=admin&password_conf=admin&Change=Change">
```

* Third, we can use the website itself if it has some vulnerabilities...
  * Navigate to the stored XSS page and use the same malicious img but without http://localhost
  * Remember to disable the client side filtering (maxlength=50 in HTML)
    * NEVER TRUST CLIENT SIDE VALIDATION!

```html
<img src="/DVWA/vulnerabilities/csrf/?password_new=xss&password_conf=xss&Change=Change">
```

* SSRF is very much the same, but the server rather than another instance of the client loads the URL.
  * Could be a proxy server.
  * Could be a cron-job or bot that processes requests.
  * Maybe an auto-mod that checks image content for reposts (has to load the image)

### Mitigating CSRF and SSRF (5-10 minutes)

* Eliminate XSS vulnerabilities from your website.
  * XSS is a major vector to achieve CSRF.
  * Furthermore, CSRF via XSS is virtually impossible to defend against :(
    * If your user's browser, cookie in hand, can be compelled to make a request there is no way for your server to distinguish it from your user intentionally making that request!


* Use CSRF tokens.
  * The idea is to check that your application is indeed the origin of the request.
  * Server generates a token, and puts it in the HTML of any page that submits data.
    * Commonly as a hidden form input, or in the HTTP header.
  * When the client submits, the server checks the token against what it generated most recently for that user.
  * The token should be changed anytime a user makes a web request, or at LEAST for every session.
  * Tokens can be defeated if the attacker can intercept them or read them, which is why XSS can be used to defeat CSRF tokens.
    * Therefore HTTPS helps improve this mitigation by making the tokens impossible to intercept.

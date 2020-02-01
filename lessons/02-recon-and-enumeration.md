# Reconnaissance and Enumeration

The first step in any hacking attempt is to identify the "attack surface." What parts of the system are exposed to us from the outside world? Which parts accept input from the application?

## Objectives

* Explore a website with the hackers mindset.
  * Identity patterns in URLs.
  * Use Burp and Gobuster to automatically explore those patterns
  * Use Burp and Gobuster to look for common "hidden" pages and endpoints.

## Lesson Plan

### Basic Recon With Burp (10 minutes)

* Open Burp.
* Open the Damn Vulnerable Web App
* Describe scope, proxy, and target panes.
* Describe the proxy and target panes.
* "Send to intruder"
  * Automate a path with numbers 0-100 (which isn't a solution to this CTF...)
  * Automate a password attack with Kali word lists
  * Okay, this takes a little while.. (esp since we're using free Burp)
  * Let it go for awhile.

### Path Enumeration with GoBuster (10 minutes)

* We can do this with Burp too, but GoBuster is faster (unless you pay for Burp)
  * Useful starting point / documentation: https://tools.kali.org/web-applications/gobuster
* Open the Kali terminal.
* We're going to use a basic command:

```
gobuster dir -e -u http://{TheWebsite}/ -w /usr/share/wordlists/dirb/common.txt
```

* (If not installed apt-get update and apt-get install gobuster)
* Describe the Kali wordlists (very useful)
* This command is going to try to try a bunch of common directory/URL paths against our host.
  * Stuff like apache/django/flask/other webserver defaults
  * Stuff like /admin
  * Etc.
* Let it go...
  * Show the results.

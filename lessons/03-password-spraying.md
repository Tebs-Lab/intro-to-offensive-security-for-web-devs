# Password Spraying

Lots of people use bad passwords. Hackers have compiled lists of common passwords and common user names, Kali has tools to weaponize these lists by automatically trying login combinations using brute force.  

## Objectives

* Define password spraying.
* Execute a password spraying attack in Burp.
* Describe tactics to defend against password spraying.

## Lesson Plan

## Password Spraying Basics & Burp (5-10 minutes)

* **What is password spraying?**
* Burp can be readily used to password spray, and Kali ships with some useful word lists.
* `/usr/share/wordlists`
  * rockyou.txt notablly (passwords sorted by popularity)
  * Many such wordlists exist online in addition to the ones that Kali ships with.


* Demonstrate intruder on Damn Vulnerable Web App
  * Note that Burp throttles you significantly on the community edition.
  * Note additionally that paid Burp maintains other useful wordlists in addition to what Kali provides.
  * Additionally and developer could fairly quickly write a script yourself to automate this in python or JS or whatever.
    * Multithreading helps with speed.


* It is also worth noting that there are a number of more complex tools, designed to spray against specific systems (SSH, SMB, etc...)
  * For web application pen-testing, Burp is an industry standard go-to.
  * On the network side you might look at something like BruteSpray which works witn `nmap`

## Defending Against Password Spraying (5-10 minutes)

* **So, what can be done to defend against this attack?**
  * There are a few mitigations that can be combined.
  * Require multi-factor authentication.
  * Have strong password requirements (this can backfire though...)
  * Monitor login attempts and consider:
    * Banning IP's after so many attempts per timeframe
    * Throttle login attempts (consider exponential backoff — slow down more for each failed attempt above threshold)
  * Have employees (and maybe even users) check Troy Hunt's "Have I Been Pwnd" to see if their un/password is common or known.

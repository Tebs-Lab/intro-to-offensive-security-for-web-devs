# The Breaker Mindset — What Happens If I Push This Button?

When hackers set out to take advantage of a system their quest revolves around this question: "What can I do on this system that the owner doesn't want me to do?" Critically, attackers only need to find *one exploitable flaw* whereas defenders need to consider every possible attack vector. This is similar to a mathematical proof: the author of a proof must exhaustively consider every relevant aspect of the problem, but a contrarian only needs to find one counter example.

This dichotomy is an essential advantage that attackers always have over defenders. Defenders have other advantages — such as access to the source code and complete knowledge of the infrastructure involved — but as the "attack surface" grows, defenders will always be at a disadvantage in that they must defend their entire territory.

This first lesson is a chance to explore the mindset of successful hackers, and contrast it with the mindset that most engineers have when approaching their work.

## Objectives

* Contrast the "exhaustive" mindset of a defender with the "path based" mindset of a hacker.
* Identify common goals for hackers.
* Describe the stages of a cyber attack.
  * Sometimes called "the kill chain."

## Lesson Plan

### Engineering Vs Hacking (10-15 minutes)

* Open with a discussion, use follow up questions as necessary:


* **So, when you approach a problem for the first time, what is your process?**
  * **Do you consider malicious input?**
  * **Do you consider edge cases?**
  * **Do you write tests?**
  * **When do you transition from planning to coding?**
  * **Do you consider how your changes might impact the rest of the system?**
  * The purpose of this discussion is to highlight a common mindset of engineers, specifically that we tend towards exhaustive and systematic thinking.
  * We want to cover all of our bases.
  * We have to consider the whole system.
  * Most engineers have at least been exposed to a test driven mindset as a Good Idea:
    * Define many inputs that exhaustively cover all possible use cases (e.g. inputs to a function), including
      * Malicious data,
      * Ill formed data,
      * Data of the wrong type ("1" vs 1)
      * Obscure corner cases,
      * etc.
    * Now, having defined a nice list of tests, an engineer will make a plan, and start to execute it.
  * Engineers have to build something that always works.
  * Our tendency is to to be meticulous, systematic, and exhaustive.
  * Engineers think ahead to roadblocks before taking action because "hours of planning can save you days of programming."


* **What about hackers?**
  * **What are the goals of a hacker?**
  * **How do we approach problems on attack?**
  * **What aspects of the underlying system matter to us?**
  * We only need to break one thing.
  * We have a small set of goals already in mind.
  * Breaking deeper into a system is more valuable better than finding every entry point.
  * We don't yet know how the whole system works, but we also don't need to consider the whole thing. Only breakable parts, and how they connect to our targets.
  * Hackers are more likely to start down a path and continue until we hit a dead end.
  * Our tendency is towards exploration and search, not exhaustive mapping.
  * Hackers abandon a path once they've found a roadblock, rather than concerning themselves with what roadblocks *might* occur further down this path.
  * Instead of worrying about what *might* happen when we push a button, hackers just push it and find out. If something breaks... GOOD! We might have found an entry point.


### A Hacker's Goals (5-10 minutes)

* **What are the goals of breaking into a computer or network?**
  * *Keep probing the students: "what else?"*
    * Steal corporate secrets or other data like passwords
    * Execute something like ransomware
    * Install persistent malware
      * Crypto mining
      * Key loggers
      * "Botnets" that can be activated later to other ends.
    * Denial of service.
    * Destruction of infrastructure.
      * Stuxnet is a great example here.


* One of the main points here is that not every bug or flaw is on a path to a hackers goal.
  * Systems that aren't valuable to a hacker won't become targets.
  * "valuable" obviously depends on the hackers goals, but we aren't just completely in the dark about this.
  * Lots of security companies compile data about what hackers are doing, who they target, and what kinds of attacks are popular!
    * We can use this information to prioritize our defenses.


### Stages of an Hack (10 minutes)

* Remember: we are trying to be "path based thinkers" so don't think of these phases as something to be "completed" before advancing to the next stage.
  * Instead, treat it like a depth first search: advance to the next stage as quickly as possible and return to a previous phase when you encounter a roadblock.
  * There are *some* exceptions to this, for example you may want to do some significant recon to identify juicy targets before trying to break anything... But it mostly holds true.
* These steps are also not necessarily linear. We may have to move laterally before we escalate privileges and vice versa.


* Step 1: Reconnaissance and exploration
  * How can I send input to this system?
  * Which inputs are likely connected to one of my targets?
  * Logins, forms, API endpoints, ADMIN PANELS!!
  * Humans are the weak-point, can I find or guess an email list to phish against?
* Step 2: Intrusion
  * Probe some of the inputs you've identified, can you cause any errors?
  * Send some phishing emails.
  * Passwords spraying.
  * You're just trying to get in the door — any door.
* Step 3: Exploitation
  * Now that you're in the door, is there something valuable in the room?
  * Can we run scripts or install software?
  * Can we upload something?
  * Can we change anything?
  * Remember: Go ahead and push that button!
* Step 4: Privilege Escalation
  * If we got in through a web-app, we're probably stuck as that user, or otherwise limited in what systems we can access.
  * We'd like to be a root user!
  * We'd like to access more systems!
  * Can we lift some passwords? Create users? Identify vulnerable software and run a known exploit?
* Step 5: Lateral Movement
  * Having opened one door are there new doors to open?
  * Get direct DB access.
  * Access another service besides the webserver.
  * Expose an admin service.
  * Get to the email server...
* Step 6: Obfuscate our Efforts
  * Black hat hackers do not want you to know they are on the system.
  * They will try to hide, remove, and otherwise obfuscate evidence of their intrusion.
* Step 7: Exfiltration
  * Getting into the safe is only half the battle.
  * If you want to profit from the data you'll have to transfer it off the system!

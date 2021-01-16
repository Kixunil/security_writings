# Umbrel is very insecure and you probably don't know it

If you're a user of Umbrel - the Bitcoin full node tool,
then there's a good chance you don't know how insecure it is.
This is not a vulnerability disclosure as the authors are already aware of the problem.
[They even put it on their GitHub.](https://github.com/getumbrel/umbrel/blob/master/SECURITY.md<Paste>)

It may seem honest but there are several problems with this:

* **They did NOT put it on their website** - most people do **not** read GitHub!
  They even [refused to put it there when asked](https://github.com/getumbrel/umbrel/issues/381)
* They put it **at the end of the installation process** - users are reluctant to start over/buy another hardware
* The "remember Umbrel is not secure" is quite hand-wavy - nobody knows what it means

I hope the former two points are clear enough.
I'm going to assume they didn't realize about the psychological factors and the problem is unintentional.
It's just their unwillingness to admit problems that's troubling.

I'm going to attempt to describe what kind of known serious vulnerability Umbrel has today:

**If for any reason an attacker gains access to your NETWORK, then FULL CONTROL of your Umbrel is possible**

This is possible because Umbrel uses a default password that doesn't change.

In other words, the attacker might do these things:

* Steal all of your satoshis
* Spy on your transactions
* Throw you out of consensus
* Mine shitcoins with your electricity
* Store child porn on the disk of your node and report you
* Send spam from your device
* Participate in DDoS attacks using your device
* I bet I could come up with more if I wanted

Now you may be asking how likely is it?
Nobody knows exactly.
I experienced this kind of attack once but anecdotal evidence is not worth much.
So I'm going to list all ways I can think of that this could happen:

* **Any** device in the same network compromised (old vulnerable phone, computer, IoT stuff...)
* Old/vulnerable network router compromised - vulnerable routers are common
* Various NAT-traversal attacks, e.g. [one discovered recently by Samy Kamkar](https://samy.pl/slipstream/)
  ~~Yes, it means that if **any** device within your network accesses a malicious website, you can be attacked.
  It could be your security-illiterate grandmother or a child.~~
  Update: not entirely, the attacker would have to attack that device first and then continue attacking Umbrel.
  While possible, it's harder.
* Misconfigured network settings
* Network shared with not-trusted people
* Unprotected network
* Wi-Fi with weak password
* Public network
* Network socket/cable accessible from outside of your house

Whether this is serious enough, I'll leave for you to decide.
You have your own brain, use it.

I'll just close this with one idea: *Perhaps losing your money is **not** user-friendly*

## Disclosure

I work on a project that has some similarities and could be by some people considered competition.
This is not personal, this is just a reaction to the fact that non-zero number of people were confused by Umbrel marketing.
I'm intentionally not including any links or marketing about my project besides this statement.
Not disclosing this fact seemed unethical.

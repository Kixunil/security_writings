# Disclosure of security vulnerability in LNURLPoS and LNURLVend

## Summary

[LNURLPoS](https://github.com/arcbtc/LNURLPoS) is an offline Lightning-enabled point of sale software which was previously vulnerable to request modification attack
enabling the attacker to pay one cent or one satoshi (depending on settings) for products of higher value.
This was responsibly disclosed by me and fixed by LNURLPoS developers.
The same vulnerable code was also used in [LNURLVend](https://github.com/arcbtc/LNURLVend) that got fixed as well.
Thus this disclosure refers to both of them.

The latest versions are no longer vulnerable and I don't see any other vulnerability.
Users using an old version must update if they didn't do so yet.

The vulnerability was discovered and immediately reported on 14 December 2021 13:10 UTC.
The fixes were committed betwenn 14 and 21 December 2021.
The vulnerability disclosure was published 7 March 2022.

## Detailed description of the vulnerability

LNURLPoS can work "offline" because it uses the connection of the payer to transfer the proof of payment via LNURL.
When the merchant enters an amount a secret PIN is generated, encrypted, and added to LNURL which is displayed to the user.
After the user pays, the server decrypts the PIN and sends it to the user who can then tell it to the merchant to prove the payment was made.

The vulnerability was caused by the amount being *just* encrypted, *not* authenticated.
Even worse, a stream cipher was used to encrypt the amount which made it super-trivial to modify.
The attacker would only have to replace encrypted amount with `encrypted_amount xor amount xor 1`.

This works because the xor operation is commutative and applying it twice with the same data is no-op.
The equation for encrypting is `amount xor secret` therefore the full "attack equation" is `amount xor secret xor amount xor 1`.
After removing `amount` which is there twice we get `secret xor 1` - same as encrypted `1`.
This was accepted by the server without any verification.
(Note: LNURLPoS, as any other sensible financial software, doesn't use floats but integers representing the smallest unit - cent or satoshi, that's why `1` means 1 cent or 1 satoshi.)

The attacker would need to use a modified wallet that does the modification based on amount entered.
This would be trivial to make reusing any existing LN wallet with LNURL support.
Even if this was (later) detectable by staff using LNURLPoS it'd be hard to identify the attacker in the case of LNURLVend variant.

## Personal notes

I'd like to take this opportunity to say a few things about security in general.
First and the most important is **I'm not a cryptographer**, yet I was able to find this bug (took less than 5 min) as well as some other bugs in other software.
(I think LND verification script is noteworthy.)
How?
Just by knowing some simple principles of cryptography and security and matching the code against them.
These principles are quite well-known but scattered around the Internet, so here are some that come to my mind:

* Always verify signatures before doing *anything* else.
* Encryption is not authentication.
* If a cryptographic algorithm requires a *nonce* make really, really, really sure it's only used once.
* It's better to show an error dialog than make users lose money by bad cryptography (e.g. randomness failure).
* Use HMAC for keyed hashes instead of bare hash.
* Use tagged hashes.
* Think about replay attacks.
* Don't mix code and data.
* Don't trust outside inputs until proven safe/correct.
* Don't trust math operations until you proved they can't overflow.
* Honor principle of least privilege - software should only be allowed to do what it requires to do.
* Use sandboxing - related to the above, software should be split into components each having its own permissions.

Surely, this list could be extended with more, I just want to make a point.
I believe any programmer is capable of checking the code against a list of principles.
The main problem seems to be programmers not knowing or forgetting about them.
So my suggestion is: make a checklist of security principles and check the relevant ones when creating a project and on each pull request.
I believe that if all developers used such a list we'd see far fewer vulnerabilities and less lost money.

I will try to think of some platform to host and collaborate on the list with more principles added.
Feel free to suggest one!

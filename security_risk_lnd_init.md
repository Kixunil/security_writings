# Somewhat small security risk when initializing LND

In this article I'm documenting a slight security risk that occurs during LND (Lightning Network Daemon) initialization.

## Am I vulnerable?

If you've seen the seed then you are **not** vulnerable.
You also are **not** vulnerable if you run [Cryptoanarchy Debian Repository](https://deb.ln-ask.me) on a **clean** machine with auto unlocker (on by default)
You also are **not** vulnerable if you run BTCPayServer-docker - this means *their* docker setup, not your own custom setup nor third party setup.

You probably are exploited if you initialized LND, didn't see any seed and then deposited sats.
You **may** be vulnerable if you're planning to install LND on a multi-user server or server with one of the (system) users compromised.
You **may** be vulnerable if you're planning to install LND on clearnet-accessible server and you announced your plan publicly.

However in the last two cases, you can easily protect yourself just by ensuring you have the seed before depositing any money.
In such case the worst possible outcome for you is annoyance and having to repeat the setup.

## How does the attack work?

In order for the attack to work, several different conditions must be satisfied:

* The user must be unfamiliar with the need to have the seed before depositing
* The machine the user is installing LND on must have an unprivileged malicious/compromised (system) user or must be accessible from the Internet.
* The time between starting LND for the first time and user attempting to initialize the wallet must be sufficiently long

When LND starts for the first time, it uses TLS to authenticate itself to the clients however it doesn't authenticate the clients trying to create a wallet.
An attacker could connect to it (using different user account on the same machine or from the Internet) and initialize the wallet and make sure it's unlocked.
It'd look like LND magically self-initialized and if the user could unknowingly deposit money.
If the user knows that LND is not supposed to mgically self-initialize (unless [a special unlocking script](https://github.com/Kixunil/cryptoanarchy-deb-repo-builder/blob/master/lnd/auto_unlock.sh) is used) further inspection will reveal the problem.

## How did this bug occur in the first place

If you know something about programming you might be asking "Why isn't there authentication code checking all RPC calls?"
This situation is a natural consequence of these design decision:

* LND uses symmetric authentication for macaroons instead of asymetric
* The authors of LND assume an attacker could gain access to wallet file and macaroon database but for some unknown reason can't access `admin.macaroon` nor `tls.key`
* The authors of LND assume the users of raw LND will be capable and willing to use command line and at the same time incapable of turning on the "encrypt disk" checkbox during OS installation
  or incapable of running `gpg` to encrypt their backups.
  (All non-command line users use wallets with a GUI which could take responsibility for protecting secret information.)

As a result LND has to encrypt the wallet and the macaroon database using a mandatory user-supplied password.
But since macaroon database is encrypted using the password, it can't be used to authenticate the clients before the password is supplied.
So LND authors decided to not verify the authenticity of the clients initializing the wallet.

## Disclosure timeline

I've responsibly disclosed this issue to Lightninglabs on 16 April 2020.
I got a reply the next day asking for GPG key (I forgot to publish it to the keyservers).
I replied in an hour and received a reply from Olaoluwa Osuntokun who said that based on how many conditions have to be satisfied he isn't sure it's a vulnerability.
He also suggested some improvements that could be made to improve the security.
I replied six hours from receiving the e-mail clarifying my previous e-mail.
To this day, I didn't get a reply.

Because of long waiting I forgot about it.
I remembered the issue yesterday evening (almost six months from disclosing).
Since the issue is not that huge and there was no progress on LND side I decided to publish this report.

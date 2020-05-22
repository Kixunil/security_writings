# Why you should use cookie files instead of passwords

Note 0: Not to be confused with browser cookies!
Note 1: This topic is much more technical than previous one, so unless you happen to be a bitcoiner running a full node, feel free to skip this one, the next one will be much less technical
Note 2: This is written with Bitcoin as an example, but generally applies to other services. If you're a sysadmin, continue reading even if you don't use Bitcoin.

## Different services and their authentication methods

When you have multiple separate programs, called services, that run on the background they can use various methods to communicate between each other. These methods may have various advantages and disadvantages. Examples include:

* Ordinary TCP connection - the big advantage is they are portable and
  can work between multiple machines,
  they are harder to properly authenticate - the point of this post.
* Unix sockets - easy to authenticate but,
  as the name suggests,
  work only on Unix (no Windows).
* Platform-specific IPC mechanisms - non-portable,
  may or may not be hard to authenticate.

### When authentication actually matters?

#### Isolation methods

If you run a single-purpose server that only runs bitcoind which you use with SSH to manually check your transaction,
then this article doesn't matter.
However, as soon as you start to run more things on such server,
it is smart to isolate them.

There exist various popular isolation mechanisms:

* Running each service under a different user - whatever you may have heard about it,
  this is actually a pretty solid way to isolate applications.
  Android uses it as the isolation mechanism (a new user per app)
  and I didn't hear about too many serious problems with it.
  If you don't update the software, then it becomes dangerous,
  but such a principle applies to everything,
  not just Android.
* Docker - currently over-hyped technology that's mistakenly considered as a security/isolation mechanism.
  To my knowledge, it actually increases the attack surface
  and it had multiple problems in the past.
  I mostly mention it for completeness
  but to be completely honest,
  I'm not that deep in the topic to rule it out completely.
  If someone can convince me that
  it's better than just using separate users
  and a few systemd hardening options,
  please do so.
* Virtual machines - the ultimate isolation that is near impossible to breach.
  The base of QubesOS and technology,
  but not without its runtime cost.

This article mainly applies to the separation of users.
I'm not sure how applicable it is to the other technologies.
I could imagine it still being useful if there's some magic trick to circumvent other security mechanisms (NAT poisoning, maybe?)

#### Why we need isolation and authentication

So what's the point of isolation?
To limit the damage if a service gets compromised.
The main idea is that in such case the attacker can only affect the data of a single service
but not other services.
E.g. if your services are properly isolated,
then someone compromising your electrum server can't steal the money from Lightning Network daemon.

But the services still need to communicate between each-other and in cases of more powerful commands, they need to prevent malware compromising an unrelated service from damaging other services. Now the most common, simple authentication method is just a password.

## The problem with passwords

Passwords seem to be fine at first glance.
If it has high enough entropy and is stored securely,
the communication can't be spied on on the local machine,
right? Wrong.

If the service uses TCP and password authentication,
then there's a less-known edge case how the password can leak.

Suppose one of the "users" got compromised.
It may look "fine" since it can't access bitcoind directly.
But what it can do is wait in an infinite loop for bitcoind to stop for any reason.
It could be admin restarting it,
right before shutdown of the machine,
OOM killer killing it if there's not enough RAM,
crash...
At that moment, the underlying TCP port is freed up and can be reused by other applications.
Including the malicious application.
So the malicious software can then bind this port (usually 8332)
and listen for incoming connections.
Now supposed that another application,
e.g. electrs,
connected to that port,
perhaps to attempt to reconnect.
The RPC protocol mandates that the application sends the password right away,
inside the RPC request.
At this point,
the malicious application obtained the password!

The malicious application can then close the socket and wait for the real bitcoind to bind it again,
perhaps as it's restarted by systemd.
(I could easily imagine this happening in quick enough succession.)
It can also save it,
just in case.

The malicious application can then empty the wallet,
if there is one,
or knock bitcoind out of consensus by sending `invalidateblock`.
Either way,
it should be considered a compromise of bitcoind.

## What is cookie file and how it works

Cookie files are an alternative authentication method of bitcoind
and possibly other software.
It works exactly the same on the protocol level.
However,
instead of using a fixed password,
it generates a completely random secret at each start
and stores it into a file.
The authorized applications can then read that file
(based on Unix permissions or ACL)
and send the secret over RPC
to prove that it's authorized.

Apart from solving a bunch of issues around keeping the config file secret
and having to manually generate the password, it also solves the security issue described above.

## How cookie file solves the race condition

Let's analyze the above situation again.
The malicious application waits for bitcoind to die,
when bitcoind dies,
the malicious application binds the port
and receives the cookie.
But remember what I said.
Bitcoind generates a completely new cookie [**at each start**](https://github.com/bitcoin/bitcoin/blob/25ad2c623af30056ffb36dcd203a52edda2b170f/src/httprpc.cpp#L252).
So when the malicious application receives the cookie, it's already expired.
The next time bitcoind launches, the cookie will be different.

But hey, what if bitcoind binds the port after generating the cookie file?
There could still be a very small opportunity for a race condition!
Well, thankfully, [it does **not** bind after regenerating the secret](https://github.com/bitcoin/bitcoin/blob/25ad2c623af30056ffb36dcd203a52edda2b170f/src/init.cpp#L791),
so such race condition is impossible.
(`InitHTTPServer` performs binding, `StartHTTPRPC` transitively calls `GenerateAuthCookie`)

As you can see,
I checked the validity of these statements myself.
(But go ahead and check it too!)
So the next time,
you will be setting up [`electrs`](https://github.com/romanz/electrs/blob/master/doc/usage.md#bitcoind-configuration)
or a similar service,
keep this in mind.
[I even made sure](https://github.com/romanz/electrs/commit/5549287ac6389d02c6410714d5864935b6d2c92e) you can use custom cookie files in electrs. :)

If you're still concerned about the security you may take a look at [btc-rpc-proxy](https://github.com/Kixunil/btc-rpc-proxy),
which can filter-out dangerous commands.
It supports *reading* cookie files, of course!
It doesn't *write its own cookie* yet because the most common case of creating a "public" user is already covered.
[Let me know](https://github.com/Kixunil/btc-rpc-proxy/issues/new) if you need that feature!

If you found this article and my work useful I will be happy to receive "Thank you for helping with security" messages at [ln-ask.me](https://ln-ask.me). :)

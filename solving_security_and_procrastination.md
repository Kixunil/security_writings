# Solving information security and procrastination at the same time

This blog post explains how I drastically improved the security of my computer AND killed procrastination using the same tool: QubesOS

## What the hell?! What procrastination has to do with information security?

I believe that the title of this section summarizes the reaction of 99% of readers. :D Yes, sounds super-crazy, please read at least this section to understand the point and *then* decide whether it makes sense, OK?

### What is QubesOS and how it solves security

QubesOS is a special operating system that focuses on "security via compartmentalization". It means that it divides your digital life into several user-defined areas and makes sure to keep data from different areas to be isolated from data in other areas. It does so by creating a new virtual computer (the technical term is "virtual machines") for each category (domain). An analogy would be buying an entirely new computer for each task. Imagine that, having a separate computer for:

* Communication with people (e-mails etc)
* Social networks
* Work
* Banking
* Fun & relax
* Random browsing
* Testing new stuff
* An organization you work for
* Each of your clients
* ... many others

You'd have to spend a lot of money and since productive people don't do everything at once, the currently unused computers would just sit and collect dust. Qubes works around this by creating virtual computers, but not just that. Virtual machines are a very old concept. Qubes also provides the whole infrastructure for managing them, allowing them to communicate securely, perform backups, and a huge pile of other security features. Another special feature worth mentioning is disposable virtual machines (called dispvm). It's like buying a computer for one task (e.g. viewing a suspicious PDF) and then throwing it away!

So what's the point? The point is that if one of the machines is compromised (which should be quite hard already thanks to other features), no other machine can be attacked from within it. In other words, if someone attacks you using Fun & relax VM, one which people don't usually take much care of, then all your other data is still secure. If you use disposable virtual machines for various dangerous tasks, you often don't even need to care if that dispvm was compromised or not!

OK, this is all cool, but it comes with its own limitations: not all applications can run on it, especially 3D games or other 3D apps, Windows-only apps can work sometimes, but you will probably find some that don't (at least not without spending quite some time messing with creating Windows VM). Also, Qubes eats quite more resources: RAM, disk space (there's some nice optimization, so it's not *completely* crazy), CPU time, battery. But unless you're a 3D artist or a 3D game developer, these limitations can be turned into productivity features!

### So how the hell can this help with battling procrastination?

First, we need to understand why procrastination happens. It's usually when your brain faces a particularly difficult task and it has an easy way to get satisfaction from some other, easy "task". For example, if you need to write a long article that you don't enjoy *that* much and there's Facebook tab open in another window, it's natural for your brain to switch window and start browsing. After some time, the stimulation creates a high level of satisfaction, that the original task (writing article) can not.

There are various tricks to help with it. Probably the most effective and simple is to remove the possibility of such distraction or make it highly unlikely. A non-Qubes user could possibly log out from Facebook on the computer and keep it on the phone only (assuming the password is hard to remember), then move the phone to another room. Yes, the phone being in another room (possibly switched off, in a drawer) is often enough to discourage your brain to go and use it. A Qubes user can use a completely separate VM for social networks or other distracting activities like games. (Interestingly, despite all the limitations, Qubes is capable of running one particularly addictive game, that I won't tell you the name of to save you. Although, that game enhances thinking about efficiency quite a lot as well...)

But how that helps? Well, running a new VM is not as easy as opening a new tab in the browser. First, it takes several seconds to start, second, it eats quite more RAM. If you don't have too much RAM, the VM may refuse to run and you will be forced to optimize it. One could theoretically shutdown work VM and launch fun VM, but procrastination is not *that* intentional. At least not in my case. I don't remember ever intentionally stopping working and starting doing something else. Procrastination always starts with "let me check Twitter really quick". And even if I do have enough RAM to start `twitter` VM (yes, that's the real name), it feels intentional, heavy and wrong.

The title of the article doesn't seem that weird anymore, right? But it can get even more intense!

## Tuning QubesOS setup to the extreme

When I started using Qubes, it was basically a stunt. I didn't need that huge level of security (previously using Linux Mint was enough already), didn't know about anti-procrastination tricks, but I loved information security and playing with OSes. It was kinda like a Lambo of security nerds. However, later when I realized I'm not as productive as I'd love to be, I randomly found a book called "[Atomic habits](https://atomichabits.com/)" (really good one, go get it!), which explained the trick about making bad habits harder. So I immediately moved Twitter from `personal` to `twitter`. At the same time, I realized I'm not satisfied with the security of my Twitter password and generated a new, completely random one. That logged me out of the phone too, and I decided to keep it that way. This simultaneously improved my security and productivity.

I started to use some other tricks, like [Habitica](https://habitica.com) to improve my life. But it still wasn't enough. Something was missing. Then I randomly found some great videos from [Andrew Kirby](https://www.youtube.com/channel/UC8JNIm02zw8h_3NBrP_zsZA), who explained a bunch of other ideas and concepts. He has a [video of how he organizes his computer](https://www.youtube.com/watch?v=RNJAV1EQ-BE) (huge minimalism) and after so-called "[Dopamine Detoxing](https://reddit.com/r/DopamineDetoxing)" (OMG, I did [TT](https://youtu.be/h6jSTr47CcI?t=319)!) [he also recommended](https://youtu.be/eRZGP3mnmwM?t=187) to use time tracker called [Rescue time](https://www.rescuetime.com/signup/solo/lite). The idea of giving someone else my private information scared the shit out of me and was impossible to use on Qubes anyway. So [I coded my own](https://github.com/Kixunil/ttt). If you're guessing I'm measuring the time I spend in particular VM, you guessed right.

Since I got the first batch of data, I got intrigued. I used `personal` for e-mail, Habitica, **GitHub**, and **ton of random internet searches**. Yes, not the ideal security. I mean, it's still 100 times better than most people, but it doesn't make much sense. And it was screwing my measurements. Awesome opportunity to split it even more! But there were some other anti-patterns:

* I was using `untrusted` for music, games and random internet searches work-related or not, music was played from Youtube in a browser, which had ~thousand of tabs open (not kidding). You can imagine the mess. Yeah, personal had about twice as many tabs despite being more security-critical!
* Initially I put tracking into personal, that made sense but it had the severe limitation that I couldn't shut down personal anymore (at least not without stopping my time tracker)
* I had a single VM for securely connecting to remote servers. This wasn't entirely broken, but not ideal either because I used it for my own servers as well as for one belonging to [a non-profit organization I volunteer at](https://paralelnapolis.sk)
* `work` VM had a pile of my own projects mixed with various random Open Source projects which I just cloned, built, and ran. Yes, idiotic idea, but that's what happens when the brain takes shortcuts. It doesn't just kill your productivity, but also security.

So with motivation through the roof, I started cutting stuff to smaller pieces:

* `personal` renamed to `personal-communication` - for e-mail, [`ln-ask.me`](https://ln-ask.me) and related things. I was considering using it for Signal too, but I'm still scared of installing it on a computer.
* `life-mgmt` - for Habitica and time tracking
* `work-web-vcs` - GitHub, and other web-based version control systems
* `work-foss` - a pile of Open Source projects that I want to contribute to
* `work-$important_project` - a new VM for security-important projects
* `work-my-code` - VM for my own, less important coding projects
* `work-writing` - for writing articles, like this one
* `work-research` - browsing work-related stuff (documentation, Stack Overflow...)
* `music` - music only, the reason for not bundling it with `fun` is to not log listening to music during work as wasting time as well as make it harder to procrastinate
* `ssh-client` - as before, used to connect to **my servers**
* `polis-ssh` - dedicated for connecting to the server of the organization I volunteer for (note: I already had a VM for it, but SSH is much more security-critical than the rest of the stuff)
* I kept the remaining Qubes that I used previously and they were fine.

With all that done, I had more peace of mind collecting relevant statistics, making bad habits even harder, and security much stronger. But I didn't stop there!

## Overcoming Qubes limitations

But hey, I don't want to have my computer just for work! I want to play 3D games too!

Actually, you can. However, there's only one way to achieve it without trashing your security (unless you figured out how to set up [TPM](https://en.wikipedia.org/wiki/Trusted_Platform_Module), which I didn't): physically swap the drives with different OSes. Yep, it's quite difficult. Yes, you need to shut down your computer completely, unscrew a few screws, swap drives, put it back, and then start your computer. This operation is both secure and **very** demotivating. So it's pretty much guaranteed, you won't play a game for "a few minutes" between the work. And yes, I *very rarely* play games this way. :)

Difficulty as a feature.

(BTW, you could, in theory, have multiple drives to achieve similar security to what QubesOS does. However, QubesOS is still more secure because of other things and much more convenient in cases where you really need convenience - e.g. transferring files between work VMs.)

## Geeky stuff

### Achieving ultimate minimalism

So Andrew showed some cool minimalistic approaches, what can I mimic or even improve? His main theme was minimalistic look. Well, guess what! Despite Qubes not *requiring* terminal for most stuff I was already using it for years! That's the ultimate instantiation of computer minimalism. Just text. And I decided to push more things to the terminal. Music was a fairly obvious one. I installed `mps-youtube` to play music videos from the terminal. That saved me the whole browser window full of distractions. Seemed better than installing an extension. :D

**What the hell are you talking about? Terminal?! That's ancient technology, it was replaced with GUI thirty years ago!**

Yet another thing that sounds strange, right? Actually, learning terminal (bash) is a huge productivity hack that I learned more than 10 years ago. It boils down to two things: using a keyboard is faster than running around with mouse cursor (especially on a laptop) and it enables some advanced automation. I bet you writing `mkdir` takes me less time than you right-clicking and then selecting "Create a new directory". What do you do when encountering a task like "download these 300 images from this site" (actually happened to me)? Most people will manually click through them and download them manually. If each takes 5 seconds to process (very optimistic guess), it will take you 1500 seconds which is 25 minutes. I just type `for i in $(seq -w 0 300); do wget https://example.com/image_$i.jpg; done` which takes me 20 seconds and then I will get some coffee while the images are being downloaded automatically. There's a ton of other things you can automate this way.

If you're interested in learning, then my recommendation would be to start with basic commands like `man`, `cd`, `ls`, `mkdir` `rm`, `grep`, `echo`... then find something that you'd like to automate and try figure out the steps needed to do it and how to write them in the command line.

So what else I automated? I found other repetitive tasks that I did often. E.g. when programming, I was often rebuilding my project and then testing it manually. It's a pretty common practice to automate the tests, but I neglected it in certain scenarios. On top of that, I did some Qubes-related stuff manually, like copying files into test VMs. Yes, it's not trivial to automate such a thing, but I managed to figure it out. As I write this article, a huge batch of tests is now running on the background without my intervention. :) I will need to do some other improvements, but this is a pretty big achievement already.

### Going extreme

#### Text editor

If you thought there's a limit on how far I can go, you're wrong. I use [Vim](https://www.vim.org/), the text editor, for many years as well. If you never heard about it, then I should start with one ~~joke~~ fact: if a newbie attempts to quit it without looking at the documentation, he won't succeed and produce a random string of characters instead. That isn't to say it's hard to quit in general, it's just that one has to know the correct command. It's `:q`, BTW. :)

So what's such an editor good for? First, it's a completely command-line application, which is great for me, as launching it is very quick. Yeah, it's also very quick thanks to not needing fancy graphical stuff. It takes 1-2 seconds to launch. How much time does MS Word need? Secondly, it's ultimately minimalistic - no buttons, no menus that would clutter your experience. There are some status bars that can be turned off if one wishes so. Lack of buttons means that to perform various actions you have to type special commands (some of which require hitting only a single letter).

Once you learn these commands you can do various text edits with blazing speed. Need to swap two consecutive lines? Hit `ddp`. Need to swap two consecutive letters? Hit `xp` (this kind of typo is extremely common for me). And so on, and so on. It also has powerful macros and tons of various plugins available. At this point, you might be interested to know how it can distinguish between edits and commands. I don't want this to become a vim tutorial, but I'll say that it has several modes, most importantly `normal`, `insert`, `visual` you can switch to `insert`/`visual` using letter commands (depending on what exactly you want to achieve) and go back using `Esc`. It may look quite clunky at first but becomes very fast and natural, once you learn them.

So this section was mostly intended to inspire you, but I improved my Vim experience too! One of the commonly used things in Vim is using number to jump/select/delete/... a certain number of lines/words/... This works really great and speeds up editing a lot, but gets difficult once you want to jump too many lines. The problem is that one can't easily tell how many lines away something is above a certain number. I saw someone using relative line numbers before, but didn't give it much thought until I realized what it's good for. So I searched how to activate it and found [an awesome article](https://jeffkreeftmeijer.com/vim-number/) on how one can also use "hybrid mode" where the current line is absolute and dynamic line numbers that change the numbering mode based on what makes the most sense in the current context. That's really cool, definitely recommend!

#### Favorite file format

If you looked at Vim by this point, you might be wondering how do I create fancy files with various colors of text, animations, and whatnot. The answer is I don't. The era of everyone showing off what he can do with a computer is long gone and minimalism is now the King. Thus, for some time I started using [Markdown](https://en.wikipedia.org/wiki/Markdown) format for all my writing. It's insanely simple, can be trivially written in Vim, and does all the most basic text formatting that is needed in 99.999% of cases: headings, lists, bold, italic, code, and some other stuff.

Yes, people occasionally send me some weird documents and I do use Libre Office in those cases. Obviously, I treat it as potential malware and only open it in disposable VMs. :) Thankfully, I made sure to avoid most communication that would incentivize people to send such documents in the first place. Slovak bureaus are required by law to use file formats that are open standards (PDF, RTF, ODF), so I'm fine here as well.

Again, I was doing this for some time and didn't change anything here. This is just inspiration for you. Now you know in which format I wrote this article and which editor I used. :)

## Conlusion

As you can see, one can go really far in productivity with QubesOS despite (or thanks to) its limitations. When combined with some terminal skills, it's possible to achieve productivity that other people can only dream about. Interestingly, I had zero problems writing this article and did so without interruptions, apart from going to the bathroom and taking a quick glance at the state of the tests, which **succeeded**! Awesome!

I hope my article will be helpful to others, who seek productivity and information security. Make no mistake, starting to use a completely different operating system is not easy. Especially Qubes. It's a digital version of the Time Theorist challenge - no eye candy, a lot of work, a lot of unlearning old habits, and a lot of learning new ones. However, great things await those who can go through it! As this is a quite difficult topic, I'll try to pay extra attention to be helpful if you ask me questions about it on whichever platform is applicable.

If liked this article and you happen to be a Bitcoiner you might be also interested in [an article about Qubes](https://github.com/MaxHillebrand/towardsliberty/blob/master/docs/bitcoin/Qubes.md) written by  Max Hillebrand.

### Special thanks

I'd like to thank a lot these people, without whom I'd never achieved this:

* Many developers of great Open Source software, especially Linux, QubesOS, bash, Vim and Habitica.
* All great people who have no problem with procrastination for inspiration and motivation.
* The author of Atomic Habits, for such a great and useful book.
* Last, but not least Andrew Kirby for huge inspiration, motivation, and very practical tools for managing my life.

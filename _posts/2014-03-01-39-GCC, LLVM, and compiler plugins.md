---
layout: post
title: GCC, LLVM, and compiler plugins
original: https://blog.csdn.net/zedware/article/details/20217975
---
[![LWN.net Logo](http://lwn.net/images/lcorner.png)](http://lwn.net/)

Not logged in
[Log in now](http://lwn.net/login)

[Create an account](http://lwn.net/newaccount)

[Subscribe to LWN](http://lwn.net/subscribe/Info)
Weekly Edition
Return to the [Front page](http://lwn.net/Articles/582067/)Recent Features
[LWN.net Weekly Edition for February 27, 2014](http://lwn.net/Articles/587524/)
[Lawrence Lessig on East-Coast vs West-Coast code](http://lwn.net/Articles/588055/)
[LWN.net Weekly Edition for February 20, 2014](http://lwn.net/Articles/586019/)
[C11 atomic variables and the kernel](http://lwn.net/Articles/586838/)
[LWN.net Weekly Edition for February 13, 2014](http://lwn.net/Articles/584508/)
[Printable page](http://lwn.net/Articles/582697/?format=printable)[Weekly edition](http://lwn.net/current/)[Kernel](http://lwn.net/Kernel/)[Security](http://lwn.net/Security/)[Distributions](http://lwn.net/Distributions/)[Contact Us](http://lwn.net/op/FAQ.lwn#contact)[Search](http://lwn.net/Search/)[Archives](http://lwn.net/Archives/)[Calendar](http://lwn.net/Calendar/)[Subscribe](http://lwn.net/subscribe/Info)[Write for LWN](http://lwn.net/op/AuthorGuide.lwn)[LWN.net FAQ](http://lwn.net/op/FAQ.lwn)[Sponsors](http://lwn.net/op/Sponsors.lwn) ![](http://engine.adzerk.net/i.gif?e=eyJhdiI6MTM1MzEsImF0Ijo0LCJjbSI6MTk5ODYsImNoIjo3MzE5LCJjciI6NTIyNDEsImRpIjoiNWYyM2NlMmUyYTFmNDJmYjgyNzhmNzg2ODQ3MDBlZjciLCJkbSI6MSwiZmMiOjc0NTY0LCJmbCI6MzgxMTksImt3IjoidDpodHRwIiwibnciOjQ2NjksInBjIjowLCJwciI6MTc2MjUsInJ0IjoxLCJzdCI6MjA5NzksInpuIjoxNjAyNiwidHMiOjEzOTM2NTU5NjA4Nzl9&s=iUUmsGQckog1dyMtPaVnSu5pMlE)#GCC, LLVM, and compiler plugins
By **Nathan Willis**
January 29, 2014[GCC](http://gcc.gnu.org/), the GNU Compiler Collection, is a cornerstone of the GNU project and the larger free-software community that has grown up around it. Recently a debate
 sprang up on the GCC mailing list over the question of whether GCC ought to deliberately adopt a development approach more like that of rival compiler [LLVM](http://llvm.org/). Precisely
 which aspects of LLVM's approach were desirable for adoption depends on who one asked, but the main argument was that LLVM seems to be attracting more users. The GCC project, however, contends that LLVM's perceived popularity is due largely to its accommodation
 of proprietary extensions—which is something that GCC supporters consider decidedly contrary to their core objectives.
Few would consider it a stretch to say that GCC has grown to dominate the compiler landscape over the course of its 26-plus years as a project, to the point where its popularity extends beyond free-software circles, and it is even routinely used to compile
 proprietary code—a scenario that is ripe for bringing interests other than software freedom into development discussions. In recent years, though, newer compiler projects have made names for themselves, LLVM being the most famous at present.
LLVM started off as a research project at the University of Illinois at Urbana–Champaign; befitting the needs of researchers, it has been developed in a modular form that allows easy replacement of individual components: front ends, code generators, optimizers,
 and so on. It was also released under the [University of Illinois Open Source License](http://llvm.org/svn/llvm-project/llvm/trunk/LICENSE.TXT), which is a permissive, BSD-style license
 that requires copyright attribution but permits the code to be used in proprietary applications. And indeed there are multiple proprietary compilers and utilities built on top of LLVM—including Apple's Xcode and NVIDIA's CUDA compiler.
####For whom the bell clangs
The present debate was touched off by a [comment](http://lwn.net/Articles/582917/) from Dmitry Gutov on the emacs-devel list on January 19. In the midst of a discussion about Emacs's [company-mode](http://www.emacswiki.org/emacs/CompanyMode)auto-completion
 framework (specifically, a minor mode designed to work with the LLVM front end Clang), Gutov said that he had heard Richard Stallman disliked Clang so much that he would oppose importing any code from it into Emacs. That spawned a lengthy thread about Clang/LLVM
 and GCC's differing approaches to software freedom—in particular, about the merits of copyleft licensing.
The various sides of that debate have all been argued many times in the past, of course, and this incarnation of the topic did not break any real new ground. Stallman's position, in short, is that Emacs and other official GNU applications should not incorporate
 default features designed to work with non-GNU projects (like LLVM) that do not work with the corresponding GNU project (GCC).
The debate was revived on January 21, however, when Eric S. Raymond [contended](http://lwn.net/Articles/582919/) that the very existence of Clang/LLVM was a response to the GCC project's
 resistance to broad interoperability. The Clang developers, he said, "want to build IDEs and other tools that share the compiler's code. GCC policy won't let them do that. Ergo, GCC must be kicked aside." Raymond also
 argued that LLVM bests GCC on several technical measures, a claim that was rapidly met with disagreement and, for the most part, dropped.
But the original point remained the subject of the resulting list thread (and several overlapping threads, some of which were cross-posted to the GCC development list). Essentially, Raymond argued that LLVM's modularity is a strong selling point among users
 that GCC cannot match. Whatever one thinks of the relative quality of LLVM and GCC, LLVM is clearly a capable enough compiler that GCC should be worried about losing market share (so to speak) to it among users. The solution, Raymond [said](http://lwn.net/Articles/582911/),
 was for GCC developers to abandon their monolithic architecture and adopt a modular compiler design like LLVM's.
####The mod squad
Modularity, though, is not a simple design question where compilers are concerned. As several in the discussion pointed out, it is actually LLVM's modularity combined with its non-copyleft license that makes it attractive to many proprietary software companies.
 Apple, NVIDIA, and other downstream LLVM users build their proprietary IDEs on LLVM—at least partly—because they do not want to be bound by the GPL's copyleft requirements. As David Edelsohn [said](http://lwn.net/Articles/582920/),
 "The issue for companies is control: which compiler allows them better access / control over the toolchain control point of the ecosystem and which allows them to create a walled garden to improve their margins and their business
 model."
In essence, asking GCC to be "more like LLVM" is not a matter of modularity alone, but of making it possible to use GCC with proprietary add-ons or extensions. And although (as Joseph Myers [pointed
 out](http://lwn.net/Articles/582921/)) GCC is more modular than it used to be (with [more such refactoring work](http://gcc.gnu.org/wiki/ModularGCC) on the to-do list), GCC's developers have actively resisted allowing
 it to be modularized in a way that would allow GCC's internal representation of a program to be read and used by proprietary compilation tools or optimizers. Stallman even [reiterated](http://lwn.net/Articles/583140/) this
 point in the current thread. GCC's present [plugin system](http://gcc.gnu.org/wiki/plugins) debuted with GCC 4.5.0 in 2010, but the project was keen to ensure that it does not offer
 a "black box" API to its internal representation.
Still, it has never been possible to fully prevent others from hacking together their own plugins, which in turn has led to some unusual licensing moves by the project. Early on, the GCC team made the decision that GCC *should* be able to compile
 proprietary code (a choice which was certainly not a given, since that could be construed as "promoting" proprietary software). The question came up due to the different nature of compilers when compared to "regular" programs. Certainly nothing in the GPL
 itself prevents using GPL-licensed software to work on proprietary software.
But GCC adds its own runtime library to the object code that it produces, which creates the first dilemma. Strictly speaking, this linking of the GCC runtime library would make any program compiled by GCC a derivative *of* GCC, which in turn means
 it would inherit GCC's GPL license. The solution to that problem was the [GCC Runtime Library Exception](http://www.gnu.org/licenses/gcc-exception-3.1.html), which explicitly exempts
 this situation and allows GCC-compiled programs to be released under whatever license the program authors would otherwise choose.
In 2009, the GCC project [released](http://lwn.net/Articles/301959/) an updated version of the exception designed to specifically address the second problem—what to do about proprietary
 GCC plugins. The exception discourages them by explicitly allowing user to distribute a GCC-generated binary under a proprietary license *only if* no proprietary plugins were used to compile the binary.
Thus, the GCC Runtime Library Exception leverages the GPL to discourage the use of GCC in conjunction with proprietary add-ons. But there are not that many GCC plugins to begin with, since (as Ian Lance Taylor [explained](http://lwn.net/Articles/583145/)),
 the plugin interface "simply exposes GCC internals, and as such is not stable across releases." Others have argued that the unstable interface acts like an effective deterrent to those attempting to maintain out-of-tree
 patch sets as well, though it has the unfortunate side effect of making it difficult to write free GCC plugins, too.
####Fight club
Whatever one thinks about the peculiar requirements that the runtime library exception sets up, it is undeniable that the GCC team is not interested in supporting the permissive approach taken by LLVM. Stallman made this point when he [replied](http://lwn.net/Articles/582241/) that
 allowing non-free plugins "would be throwing in the towel. If that enables GCC to 'win', the victory would be hollow, because it would not be a victory for what really matters: users' freedom."
Then again, there is considerable disagreement over what "winning" really means for GCC, and even over whether LLVM and other compilers are in a competition with GCC. Raymond and others pushing for a more LLVM-like approach argue that LLVM will attract more
 users than GCC. That is certainly a simple metric, but to many it is not a relevant one. Jonathan Wakely [commented](http://lwn.net/Articles/582922/) that Raymond thinks he is "helping
 us win a war with Clang. But there is no war. There's room for two high-quality open source compilers. They will distinguish themselves in different ways, one of which is licensing."
Perhaps the more extreme viewpoint would be that GCC faces an existential threat from LLVM. Along those lines was Daniel Colascione's [comment](http://lwn.net/Articles/582924/) that
 "Free software is great, but if nobody uses it, the entire enterprise is futile, sad, and ultimately irrelevant." To that, Stallman [responded](http://lwn.net/Articles/582925/),
 "Free software is equally futile, sad, and irrelevant if everyone uses it...as a base for nonfree software."
Ultimately, all parties to the debate said that they are interested in seeing the number of GCC users increase. The root of the "competition" disagreement tended to come down to whether the GCC team's plugin policy amounts a barrier to attracting new users.
 Helmut Eller [called it](http://lwn.net/Articles/582926/) a "policy of introducing artificial technical hurdles to prevent some nonfree programs"
 and caused collateral damage. David Kastrup [replied](http://lwn.net/Articles/582927/) that "the whole point of the GPL is to introduce an 'artificial
 hurdle' preventing turning code into proprietary programs," and, significantly, it is a hurdle that works, as the absence on proprietary compilers built on top of GCC demonstrates.
There will likely always be people for whom the proprietary derivatives of a permissively-licensed program like LLVM are a problem, and there will be others who do not care about them, so long as free software exists, too. Similarly, to some people, if LLVM
 attracts more users than GCC, it must reveal that GCC is flawed, while to others the relative numbers are meaningless.
On the more concrete questions, however, the answers are considerably less nebulous. Stallman and the GCC team have made their position clear about adopting a lax attitude toward proprietary plugins in GCC: it is a non-starter. Whether that stance amounts
 to a fundamental flaw in GCC itself depends on the individual—but do not expect GCC to change its tune simply because LLVM has attracted its own crowd of users.
***
([Log in](https://lwn.net/login?target=/Articles/582697/) to post comments)
[]()[]()
GCC, LLVM, and compiler plugins
Posted Jan 29, 2014 20:21 UTC (Wed) by **emk** (subscriber, #1128) [[Link](http://lwn.net/Articles/583203/)]
I haven't touched the LLVM code base in years, but way back when, I tried to use it as embedded JIT. (And I wound up writing the first version of llvm-config, so it could be easily linked as a JIT.)
Along the way, I tripped over a compiler bug. I can't remember the details now, but I wound up diving into the LLVM C++ code to fix it. And the code was *nice*—there once major intermediate representation, based on a strongly-typed assembly language with
 a very small number of operations. I was able to find my way around the code base and make my patch without unnecessary pain. Even better, I could dump the intermediate representation as an LLVM "assembly" file at any step of the compilation process, which
 made debugging very civilized.

So I'm not surprised that LLVM has since gone on to be quite successful. The architecture was always very approachable and very pleasant to hack on. At least at the time, that was not really the case with GCC.


[]()GCC, LLVM, and compiler plugins
Posted Jan 29, 2014 23:07 UTC (Wed) by **nix** (subscriber, #2304) [[Link](http://lwn.net/Articles/583241/)]
Every part of ESR's statement was many years out of date.The Clang developers, he said, "want to build IDEs and other tools that share the compiler's code. GCC policy won't let them do that. Ergo, GCC must be kicked aside."It has not been true for many years (since the GCC plugin exception was added) that this sort of thing was against any sort of policy. Indeed, there are debugging options to dump the full AST now, and if that wasn't enough -flto dumps the entire GIMPLE intermediate
 representation into a section in the object file. It is certainly not impossible to read this in and reconstruct the entire state of the compiler: after all, GCC does just that.It is true that introducing these features required substantial negotiation with RMS and was both politically and technically painful, but the technical pain wasn't a consequence of the political pain: it was just that GCC needed a lot of changes before
 it could do this sort of state serialization. It had never been designed for that, after all, and even in a post-tree-ssa world had a lot of internal cruft and implicit dependencies that made it hard to do. (I am in awe of the whole SSA serialization work
 in particular: an amazing example of spinning gold out of rather damp straw, second only to the tree-ssa project itself.)

GCC's developers have actively resisted allowing it to be modularized in a way that would allow GCC's internal representation of a program to be read and used by proprietary compilation tools or optimizersAgain, this was true up until something like six *years* ago. This horse has left the barn, strapped on rocket boosters and flown into low orbit by this point, and everyone involved knows it (except possibly RMS! never underestimate how out of touch
 he can become).
the plugin interface "simply exposes GCC internals, and as such is not stable across releases."The same, of course, is true of LLVM: its internals may be less crufty and easier to get along with simply by reason of LLVM's relative youth, but the churn rate is still very high.
[]()GCC, LLVM, and compiler plugins
Posted Jan 30, 2014 2:04 UTC (Thu) by **JoeBuck** (subscriber, #2330) [[Link](http://lwn.net/Articles/583282/)]
Exactly. Furthermore, RMS really doesn't know much about the current state of GCC, so when ESR started screaming about GCC being non-modular and not supporting extensions, RMS started going on about this being a good thing instead of pointing out that it wasn't
 true. RMS has not been active in GCC development for years. He does not read the gcc list. He reads the Emacs list, so he saw this discussion because it was crossposted. RMS only interacts with the GCC steering committee, and that way the gcc developers can
 develop code in peace without being constantly drawn in to free software purity discussions. GCC contributors are all over the map as far as the free software vs open source, purity vs "pragmatism" debates are concerned, but they still work together effectively.Despite his past contributions, in this interaction ESR has basically been acting as a troll (hopefully inadvertently), cross-posting to three development lists (Emacs, gcc, llvm) with highly contentious and uninformed opinions, seemingly designed to get
 a lot of people yelling at each other. And why? To get attention? To get GCC to make changes that it has already made long ago and Eric didn't know about?
The GCC plugin API isn't stable. But neither is LLVM's, and neither is the Linux kernel module API. There are good reasons for this.

[]()GCC, LLVM, and compiler plugins
Posted Jan 30, 2014 13:46 UTC (Thu) by **nix** (subscriber, #2304) [[Link](http://lwn.net/Articles/583389/)]
RMS has not been active in GCC development for years.Tempus has fugitted: by this point, the correct term would be 'decades'!
[]()GCC, LLVM, and compiler plugins
Posted Jan 30, 2014 16:38 UTC (Thu) by **marcH** (subscriber, #57642) [[Link](http://lwn.net/Articles/583457/)]
> It has not been true for many years (since the GCC plugin exception was added) that this sort of thing was against any sort of policy. Indeed, there are debugging options
 to dump the full AST now, 
Thanks A LOT. This was IMHO the most important point that the article missed: how can this modularity discussion still happen even after the GCC plugin exception was added? Does not make sense.

Apparently just because everyone pays too much attention to famous people, even when they are poorly informed.




[]()GCC, LLVM, and compiler plugins
Posted Jan 30, 2014 1:27 UTC (Thu) by **silvas** (subscriber, #87887) [[Link](http://lwn.net/Articles/583271/)]
> But there are not that many GCC plugins to begin with, since (as Ian Lance Taylor explained), the plugin interface "simply exposes GCC internals, and as such is not stable
 across releases."
Many people would rather put their code under GPL than have to interface to an unstable API. If the new "modular" GCC can provide useful, *stable* API's that are developed to meet a broad class of use cases, it could gain significant mindshare.


Full disclosure: I'm an LLVM developer. Hopefully this will help clarify the sense in which LLVM is useful as a library:

LLVM does have stable C API's, but doing anything nontrivial with them usually will require exposing something else from C++-land, and so you end up having to get involved with upstream.

One thing to keep in mind is that LLVM's C++ API's "simply exposes LLVM's internals". LLVM's is "library-based", if you define "library" as "a well-factored body of code that does something". However, if you include any notion of compatibility or attention
 to external use cases in your notion of library, then LLVM doesn't really qualify.

LLVM is like a bunch of libraries that evolve in lock-step. I sometimes describe it as "LLVM is loosely coupled from a software architecture standpoint, but very tightly coupled from a development standpoint".

Also, LLVM is developed with very little attention to external use cases. Most software libraries exist solely to serve external use cases. What is exposed as stable in LLVM's C API is basically "the minimal amount of stuff we can expose right now to meet
 the use cases of the users that have come barking at us". The C++ API's are literally the internal include/ dir. LLVM has no "public" include/ dir with its "public headers": all the headers in include/ (except for the C API) are evolved in lock-step with the
 goal of cleaner, better code, and no attention to backwards compatibility.

I'm not saying that this is a good or bad thing (actually, as I mentioned in another thread, this is a very strong disincentive for keeping private branches, which is a really good thing IMO). Personally, as an LLVM developer, I really like the freedom of
 not having to worry about backwards compatibility 99.9% of the time, even though I'm modifying ostensibly public interfaces. However, it's a headache for users.

Personally, I would not want GCC to go that same route. Or at least, I would like to see GCC learn from this aspect of LLVM and not end up in exactly the same situation. I have no clue what to do differently though.


[]()GCC, LLVM, and compiler plugins
Posted Jan 30, 2014 2:13 UTC (Thu) by **silvas** (subscriber, #87887) [[Link](http://lwn.net/Articles/583283/)]
I should also mention that LLVM does have a few important points of compatibility besides just the C API:
- The textual and bitcode format of LLVM IR, and its semantics.

- The syntax and semantics of the code that Clang translates (as you would expect from any production-quality compiler)

- The commandline options accepted by Clang (as you would expect from any production-quality compiler)


[]()GCC, LLVM, and compiler plugins
Posted Jan 30, 2014 13:44 UTC (Thu) by **nix** (subscriber, #2304) [[Link](http://lwn.net/Articles/583387/)]
Of these, the first of these is the only thing I can see that LLVM guarantees, but GCC does not. (The closest GCC equivalent would be the sections written out when -flto is used, which are definitely not stable across releases.)

[]()GCC, LLVM, and compiler plugins
Posted Jan 30, 2014 2:52 UTC (Thu) by **PaXTeam** (subscriber, #24616) [[Link](http://lwn.net/Articles/583289/)]
> Personally, I would not want GCC to go that same route.
gcc doesn't and won't go down that route as it got the plugin feature rather late in its lifetime therefore the churn in the exposed API is really much less than either that of llvm or linux. the only real 'problem' for plugin writers is the ongoing c++
 conversion that will eventually make it impossible to support different gcc versions from the same source code, but so far it's been quite managable. if anything, i wish more access was exposed to plugins (say, the preprocessor, the language frontends, etc).


[]()Emacs and clang?
Posted Jan 30, 2014 4:10 UTC (Thu) by **rsidd** (subscriber, #2582) [[Link](http://lwn.net/Articles/583299/)]
I seem to have missed RMS's attitude towards supporting clang in emacs the first time you posted about this. I find stuff like [this](http://thread.gmane.org/gmane.emacs.devel/168747)
**> GNU Emacs does not exist in isolation. It is a part of the GNU Project. "Success" for GNU Emacs means success for GNU.
> Our policy is not to install features in GNU package that would work with Clang and not with GCC.
and [this](http://thread.gmane.org/gmane.emacs.devel/168747) (of packages that exploit clang interfaces that have no GCC analog)*They nonetheless encourage people to switch from GCC to Clang. The features may be useful but this way of providing them hurts our cause.*pretty terrible and likely to hurt emacs more than LLVM. Besides, LLVM is anyway a free software project and RMS used to be supportive of all free software (he even co-opted X as part of the GNU operating system!) What changed?
[]()Emacs and clang?
Posted Jan 30, 2014 4:59 UTC (Thu) by **da4089** (subscriber, #1195) [[Link](http://lwn.net/Articles/583310/)]
LLVM is not GPLed. When there's a GPL alternative, the GNU project will encourage its use. In the case of X, there was no GPLed alternative.

[]()Emacs and clang?
Posted Jan 30, 2014 5:46 UTC (Thu) by **rsidd** (subscriber, #2582) [[Link](http://lwn.net/Articles/583314/)]
There are features that are in LLVM that are not in the GPL'd alternative. Stallman's policy is to not support those features in emacs. To not support proprietary software is one thing, to not support free software LLVM features because it is a competitor to
 a GNU program that doesn't have those features is another thing.
By the way, RMS did [support](https://lwn.net/2001/0301/a/rms-ov-license.php3) the switch of Ogg Vorbis to a BSD licence on "pragmatic" grounds. Pragmatically, aren't we better off
 that Apple, Nvidia, Intel, Adobe and others are using and contributing to LLVM rather than a proprietary solution (and they either would not have, or could not have, used GCC for the use cases they had in mind)? Isn't there an argument to be made that, without
 LLVM, we wouldn't have free OpenCL compilers?

[]()Emacs and clang?
Posted Jan 30, 2014 7:30 UTC (Thu) by **pbonzini** (subscriber, #60935) [[Link](http://lwn.net/Articles/583325/)]
Reading the message you linked, the balance there was between keeping copyleft and facilitating the use of a patent-free codec. There is no such tradeoff in the case of GCC.

[]()Emacs and clang?
Posted Jan 30, 2014 7:58 UTC (Thu) by **rsidd** (subscriber, #2582) [[Link](http://lwn.net/Articles/583326/)]
Again, there are things LLVM can do that GCC can't and probably never will. The choice is not between LLVM and GCC (unless you're just compiling a program, in which case GCC is probably better), it is between LLVM and something
 proprietary that would probably have been invented. And the commercial companies that use LLVM contribute back to it. If RMS's goal is to increase the availability of free software, he should support LLVM. If his goal is to shut down proprietary software at
 the cost of hurting free software, well, LLVM runs against that goal, but then he should not be running the "Free Software Foundation". Shut the FSF down, make clear that the GNU project exists in competition to and not in collaboration with other free software
 projects, and everyone will be happy. Or at least cease to be under misapprehensions. 

[]()Emacs and clang?
Posted Jan 30, 2014 8:42 UTC (Thu) by **oldtomas** (subscriber, #72579) [[Link](http://lwn.net/Articles/583334/)]
> Shut the FSF down, make clear that the GNU project exists in competition to and not in collaboration with other free software projects [...]
Calm down and stop screaming "bloody murder".

The FSF and RMS are free to not support whatever feature in Emacs. You are free to implement it (remember? It's free software) and even to distribute and promote it (as long as you abide by the license). The FSF and RMS explicitly allow you to do that.

In won't be under the FSF umbrella, i.e. not part of the official Emacs distro.

Where's the problem?


[]()Emacs and clang?
Posted Jan 30, 2014 9:07 UTC (Thu) by **rsidd** (subscriber, #2582) [[Link](http://lwn.net/Articles/583337/)]
The problem is that the FSF's avowed mission is promotion of free software, and he is doing the opposite here because he thinks it will hurt a GNU project. If someone else ran FSF and he ran GNU there wouldn't be a problem. 

[]()Emacs and clang?
Posted Jan 31, 2014 7:50 UTC (Fri) by **oldtomas** (subscriber, #72579) [[Link](http://lwn.net/Articles/583594/)]
> The problem is that the FSF's avowed mission is promotion of free software [...]
which it has done in an impressive way, looking back. Could it have been done better? Possibly. Would *I* have done better than RMS? I very much doubt it. Could *you*? Sorry. I doubt it too.

And I repeat myself, because the point doesn't seem to be clear: thanks to the GPL you're free to add *and distribute* the LLVM support in Emacs you so much wish for. Go ahead!


[]()Emacs and clang?
Posted Jan 30, 2014 14:25 UTC (Thu) by **emk** (subscriber, #1128) [[Link](http://lwn.net/Articles/583390/)]
I was thinking about this, and as an Emacs user, I suspect you're correct:
1. I really wish Emacs had modern syntax-highlighting and code-completion support.
2. GCC is not remotely ready to provide those features right now, and it will not be ready within the next couple of years.
3. There's already an excellent, GPL-compatible compiler which could be used to make Emacs suck less compared to the proprietary alternatives.
4. Stallman is absolutely opposed to providing any help to users to who want to take advantage of (3), even though the entire resulting stack would be GPL-compatible.
5. Stallman is now actively sabotaging the quality of GPLed software to punish non-GPLed, GPL-compatible software.

Here are two [key](http://thread.gmane.org/gmane.emacs.devel/168747) [quotes](http://thread.gmane.org/gmane.emacs.devel/168747) from
 the thread:
Our policy is not to install features in GNU package that would work with Clang and not with GCC. Emacs will not encourage people to switch away from GCC. Indeed, when we have the chance, we should encourage GCC over Clang.And:
There exist now various packages integrating emacs will elements of clang. These packages are not supporting clang as alternative compiler. Rather by exploiting clang interfaces that have no gcc analog they offer exciting IDE-like features.They nonetheless encourage people to switch from GCC to Clang. The features may be useful but this way of providing them hurts our cause.My conclusion: After many years of respect for Stallman, I'm afraid he's now actively sabotaging his own ideological goals to punish people who choose non-GPLed, GPL-compatible licenses. And this includes punishing users, specifically users of free software,
 who would like to have modern C++ features in Emacs. I cannot support this attitude.
I've always tried to evaluate Stallman's actions based on his stated goals, and I've generally found that he is ethical, straightforward and clear in pursuing those goals. Here, however, it appears he is choosing to punish Emacs users because of his personal
 distaste for LLVM.
Because of this, I no longer trust the Free Software Foundation enough to include the "future version of this License" language in GPLed software that I write. And because I consider GPLv2-only software to be an unpleasant licensing obstacle, this means
 that I will be releasing more software into the public domain when I would have otherwise strongly considered the GPL. I'm sick of stupid licensing wars; I just want the freedom to hack on cool code, and to allow my users to do the same.

[]()Emacs and clang?
Posted Feb 5, 2014 11:02 UTC (Wed) by **javispedro** (subscriber, #83660) [[Link](http://lwn.net/Articles/584217/)]
What I don't understand is why you assume this would require decades to be done with gcc. This is a coupling of two GNU projects so there are no licensing barriers at all, and no technical reasons I can't think of either.
Besides, C/C++ parsers aren't _that_ hard and there have been loads of alternatives for years. No idea why suddenly every other "syntax highlighter on steroids" seems to require LLVM.


[]()Emacs and clang?
Posted Feb 5, 2014 13:25 UTC (Wed) by **foom** (subscriber, #14868) [[Link](http://lwn.net/Articles/584218/)]
Um...There aren't loads of alternatives. c++ is an stupidly hard language to parse. 
There's a bunch of half baked projects out there, but I'm not aware of even *one* alternative free software c++ parser (besides clang and gcc) that actually works.



[]()Emacs and clang?
Posted Feb 5, 2014 22:39 UTC (Wed) by **javispedro** (subscriber, #83660) [[Link](http://lwn.net/Articles/584360/)]
Virtually every other serious compiler generator out there comes at least with a C++ grammar as a model of a non CF grammar just to show how one would implement such things. 
As for more examples of such parsers on the wild, Qt Creator has one (and no, it's not clang), Eclipse's CDT has one, etc. 

Parsing C++ is hard, but definitely not impossible.


[]()Emacs and clang?
Posted Feb 5, 2014 23:36 UTC (Wed) by **Cyberax** (**✭ supporter ✭**, #52523) [[Link](http://lwn.net/Articles/584368/)]
>Virtually every other serious compiler generator out there comes at least with a C++ grammar as a model of a non CF grammar just to show how one would implement such things. 
And none of them has a complete C++ parser. 
> As for more examples of such parsers on the wild, Qt Creator has one (and no, it's not clang), Eclipse's CDT has one, etc.
And all of them are incomplete.


[]()Emacs and clang?
Posted Feb 6, 2014 2:15 UTC (Thu) by **mathstuf** (subscriber, #69389) [[Link](http://lwn.net/Articles/584388/)]
The KDevelop parser stuff pretty complete to me when I used it last. IIRC, it even figures out macro madness. What is missing, specifically?

[]()Emacs and clang?
Posted Feb 6, 2014 2:22 UTC (Thu) by **Cyberax** (**✭ supporter ✭**, #52523) [[Link](http://lwn.net/Articles/584389/)]
A lot of template stuff. So much of it, that they'd junked their parser in favor of clang: [http://milianw.de/blog/katekdevelop-sprint-2014-let-there...](http://milianw.de/blog/katekdevelop-sprint-2014-let-there-be-clang)

[]()Emacs and clang?
Posted Feb 6, 2014 2:29 UTC (Thu) by **mathstuf** (subscriber, #69389) [[Link](http://lwn.net/Articles/584390/)]
Ah. I figured C++11 would be of some issue, but I guess vararg templates are really hairy. Also, when I used KDevelop, it was using Qt containers, so templates weren't that much of an issue. Now I do lots more with boost (qi is
 certainly a beast), but I'm also in Vim.

[]()Emacs and clang?
Posted Feb 6, 2014 10:38 UTC (Thu) by **javispedro** (subscriber, #83660) [[Link](http://lwn.net/Articles/584455/)]
They are complaining it's not C++11 complete, but it seems C++03 complete to me. 
I don't understand your point regarding not any of the parsers I mentioned being complete. Obviously if you want to be pedantic then no C++ parser is complete (not even clang or gcc) as neither of them can solve the halting problem. But all of the ones I
 mentioned are quite complete enough to perform code indexing+autocomplete+folding (and will recover from broken syntax much more nicely than clang or gcc).

They will not handle corner cases but I'm not sure if you're able to solve these using only the clang public C API. And I'm not sure if the corner cases are really worth it in the first place. Not to mention most parsers are also faster than gcc or (gasp)
 clang. Compare QtC's one.

Virtually the only benefit I can find to clang is that, because Mesa depends on it, it's kind of guaranteed to be present on every other GNU/Linux system. 

TL;DR GNU forbidding a LLVM dep is not going to kill emacs. There's plenty of alternatives. Just Google around.


[]()Emacs and clang?
Posted Feb 6, 2014 18:44 UTC (Thu) by **nix** (subscriber, #2304) [[Link](http://lwn.net/Articles/584597/)]
but it seems C++03 complete to meBet it doesn't support export (but mind you neither does anyone else bar EDG, and it's nearly useless even when it works, and it's gone from C++11).I wonder if it gets the horrible Koenig lookup rules for dependent types in multiple namespaces right. That's a typical hell zone for C++ parsers, and assuming that KDevelop wants to know what types things are (or even if they *are* types rather than,
 say, variables) it needs to get that right...

[]()Emacs and clang?
Posted Feb 6, 2014 5:55 UTC (Thu) by **raven667** (subscriber, #5198) [[Link](http://lwn.net/Articles/584409/)]
If I understand correctly the complexity of parsing the source and returning some sort or normalized representation of the code is going to be the same for a compiler as for a full-featured editor, so by definition the "simple"
 parser used by the editor, to be complete, will have to be the same complexity as the parser used by the compiler, so why not ditch the duplicate implementation (which won't be bug-for-bug compatible anyway) and use the compiler as the parser.

[]()Emacs and clang?
Posted Feb 7, 2014 11:38 UTC (Fri) by **dakas** (guest, #88146) [[Link](http://lwn.net/Articles/584682/)]
I've always tried to evaluate Stallman's actions based on his stated goals, and I've generally found that he is ethical, straightforward and clear in pursuing those goals. Here, however, it appears he is choosing to punish Emacs users because of
 his personal distaste for LLVM.Uh no. The current situation basically is a dangling carrot in favor of supporting approaches and communities that in turn are rendering the GPL useless.The approach taken by the GPL is to not rely on goodwill. It is a tool based on legal restrictions on reuse of software and its modifications as a whole.
Restrictions as a tool for ensuring freedom are *always* balancing a limited unfreedom against a long-term freedom.
Just how the balancing part turns out depends on what happens in the real world.
The overall situation around LLVM has been engineered to favor convenience. That is largely by design, and some of that design plays into the hands of people on the lookout for proprietary solutions. A significant part of the LLVM company support is related
 with that, and it leads to improvements and benefits that make GCC look less compelling on technical viewpoints.
"So let's abandon whatever we managed to achieve in our copyleft-dominated universe and convert to interoperable software on the terms of their respective communities." is certainly a natural reaction.
But that some developer goals have, due to the availability of LLVM, shifted from the core position of RMS does not mean that *he* has become unreasonable. He is trying to bring those communities caring about similar goals back in synch with what
 the GNU project stands for, and in this case this requires jumping off a convenient one-way wagon that is taking off in a direction that would be out of control of the FSF and the GNU project.
Just because the world chose not listening to RMS in one case does not mean it makes sense for him to run after it. Yes, this makes him less popular with people like you, but then the GNU project was never about popularity.
As to not trusting where the GPL may go: the copyright assignments from the FSF are two-sided contracts and spell out quite narrow restraints for that. They have been designed with the intent to make it next to useless for big companies to take over assets
 of the FSF or try getting increased influence over it.

[]()Emacs and clang?
Posted Feb 9, 2014 10:22 UTC (Sun) by **Del-** (guest, #72641) [[Link](http://lwn.net/Articles/584916/)]
> Uh no. The current situation basically is a dangling carrot in favor of supporting approaches and communities that in turn are rendering the GPL useless.
No, LLVM/Clang was/is about Apple wanting all the benefits of open development without committing to anything. That is some twisted view of freedom and has little to do with communities so far. LLVM/Clang is effectively an open core model for Apple, for
 them it is a part of xcode (they have no interest in contributing xcode to anybody afaik). There is a lot of astroturfing making wide claims about how fantastic LLVM is. Recent events tend to show that LLVM is not all it is cranked up to be. How long will
 contributors outside Apple wait until they just fork it for their own purposes, or just give up and do their own thing? Whether this model succeeds in the long run is an open question. Personally, I put my money on GCC. The FSF has shown a flexibility around
 GCC (allowing transition from C to C++, allowing official plugin solution, moving over to a modular approach) that you cannot expect from Apple unless is suites their specific needs. Apple has proven exceptionally bad at building communities, but quite good
 at damaging communities. I wouldn't want Apple near any open projects I care about.

> The approach taken by the GPL is to not rely on goodwill. It is a tool based on legal restrictions on reuse of software and its modifications as a whole.

I do not find this line of reasoning very useful. GPL is designed to make people contribute back, while BSD is designed to enable forking. They both rely on goodwill, you cannot really force anybody to contribute to an open project, but you can create incentives
 for it. That is what GPL does.

> "So let's abandon whatever we managed to achieve in our copyleft-dominated universe and convert to interoperable software on the terms of their respective communities." is certainly a natural reaction.

Some large software houses want to take advantage of an open development model without committing to what made the model successful in the first place. Moreover, they are quite deliberate on what bits to do it on (typically low level bits, run along to Apple
 and ask them for an open source gui to anything). This is nothing new, Apple and others have used BSD software for a long time as an alternative. The new thing today is that some of the larger software houses are willing to collaborate on BSD code. That is
 an interesting model, and I am curious to see what comes out of it the next decade. I do think many will learn the hard way that BSD is in most cases not about community at all, so I think your usage of the word community in your post is deceiving.




[]()Emacs and clang?
Posted Feb 9, 2014 12:47 UTC (Sun) by **khim** (subscriber, #9252) [[Link](http://lwn.net/Articles/584923/)]
The new thing today is that some of the larger software houses are willing to collaborate on BSD code.That's also not new: X.Org (the X Consortium before it) operated in such a fashion for decades. This model works. Yes, some tangential development is lost and if you compare result of copyleft project and non-copyleft project with similar numbar of contributors
 then copyleft project is usually more advanced but then non-copyleft projects tend to attract larger number of developers (and, eventually, contributors) which means that in practice results are quite similar.

[]()Emacs and clang?
Posted Feb 9, 2014 13:25 UTC (Sun) by **paulj** (subscriber, #341) [[Link](http://lwn.net/Articles/584927/)]
Hardly tangential, useful extensions like Display Postscript were locked away in proprietary forks of X.

[]()Emacs and clang?
Posted Feb 9, 2014 16:32 UTC (Sun) by **Del-** (guest, #72641) [[Link](http://lwn.net/Articles/584939/)]
> X.Org (the X Consortium before it) operated in such a fashion for decades.
Indeed, thanks for reminding me.

> This model works.

Please define what you mean by "this model works". From my end of the universe it hasn't exactly been the unifying experience. Xorg itself was a fork of course, but even ignoring that for a while. How has this played out?

The graphics stack on linux has been a major pain the whole way, and I fail to see how Apple embracing xfree86 helped that even a tiny bit. Today it seems Apple want to go at it alone with its Xquartz. The BSDs of this world seems stuck with xorg, while
 Android made its own surface flinger, and linux-world is currently being torn apart by wayland/weston and mir.

On the low level bits linux finally had a go at it alone with KMS, leaving BSDs out in the cold. Seems it was just the right cure, and BSD is finally catching up by piggy-backing on the linux graphics stack. Who knows, maybe you will even be able to use
 something open other than intel graphics on BSD soon.

Is this your idea of successful collaboration? While you are at it, please enlighten us all about the terrific audio stack provided to linux through open sound system. I prefer GPL any day of the week.


[]()Emacs and clang?
Posted Feb 11, 2014 18:55 UTC (Tue) by **jsdyson** (guest, #71944) [[Link](http://lwn.net/Articles/585418/)]
"That is an interesting model, and I am curious to see what comes out of it the next decade. I do think many will learn the hard way that BSD is in most cases not about community at all, so I think your usage of the word community
 in your post is deceiving."
Actually, at least the original GPL is not really about the 'community', but rather a (non-prejudicial) set of rules which requires an offer of source code TO THOSE WHO ARE GIVEN A COPY OF THE BINARIES. If there is a company which has purchased a service
 contract for GPLed software (often $10's of k -- outside the interest of most individual developers), that company receiving the GPLed works has no real motivation to pass on the changed source code to anyone else -- possibly given the intellectually property
 freely to a competitor. The GPL seems mostly to be a helpful tool for those receiving the binaries so that they can maintain the software themselves.

The idea of redistribution of new changes to the 'net' at large (to a larger development team), can be quite helpful for either BSDed or GPLed sources to be maintained by the network (the larger internet based development projects, e.g. Linux(whatever version),
 FreeBSD, X or whatever.) It can be quite onerous to maintain the deltas from a dynamic project, so it can be very beneficial to give back at least a significant part of add-on intellectual property so that the net at large might support it. GPLed works and
 BSDed works are both mostly agnostic on that point.

The GPL doesn't really force redistribution to the net at large, nor does BSD. AFIAR, there have been some licenses which require sending changes back to the owner, but such terms seem (to me) to violate personal freedom to choose to embargo or redistribution
 one's own invention or ideas.

John Dyson


[]()GCC, LLVM, and compiler plugins
Posted Jan 30, 2014 9:28 UTC (Thu) by **kugel** (subscriber, #70540) [[Link](http://lwn.net/Articles/583340/)]
> But GCC adds its own runtime library to the object code that it produces, which creates the first dilemma. Strictly speaking, this linking of the GCC runtime library would
 make any program compiled by GCC a derivative of GCC, which in turn means it would inherit GCC's GPL license.
I disagree with that. When I write a proper C program that does not depend on any of GCC extensions and I can compile it fine with a different compiler, then compilation with GCC of that program doesn't make that program derivate work. Whether GCC builds
 a runtime library into the program is the choice of GCC and an implementation detail, and doesn't affect my program or it's license. The same is true for programs which have been developed under a different compiler and now compile under GCC.

Similarly, the license of my text editor/IDE also does not affect the license of the programs I write using them and nobody would consider it derivative work.

"Derivative work" is a more complex matter than just "the program includes bits of X even if just 1 LOC" or something like that. GCC's exceptions are just FUD and not necessary.


[]()GCC, LLVM, and compiler plugins
Posted Jan 30, 2014 13:52 UTC (Thu) by **nix** (subscriber, #2304) [[Link](http://lwn.net/Articles/583392/)]
I disagree with that. When I write a proper C program that does not depend on any of GCC extensions and I can compile it fine with a different compiler, then compilation with GCC of that program doesn't make that program derivate work. Whether GCC
 builds a runtime library into the program is the choice of GCC and an implementation detail,Copyright law does not talk about 'implementation details'. What matters is the direct textual inclusion of work licensed under two different licenses into a single aggregate, which has definitely happened when libgcc is linked into a binary under another license:
 that sort of inclusion is what linkers*do*.Fortunately, the runtime library exception makes this sort of use not a problem: libgcc's use only imposes extra requirements on proprietary software distributors if they hack libgcc (in which case they must distribute the source to the hacked copy, and
 its build system).

[]()GCC, LLVM, and compiler plugins
Posted Jan 30, 2014 14:57 UTC (Thu) by **madscientist** (subscriber, #16861) [[Link](http://lwn.net/Articles/583411/)]
Put another way: yes, absolutely compiling with GCC doesn't cause _your source code_ to come under the GPL. However, _the generated binary_ output from the GCC linker contains copyrighted code from the FSF licensed under the GPL,
 and so if you redistribute that you have to obey the GPL license, and that license puts requirements you to distribute the source code, which is perfectly legal (insofar as anyone knows).
If you take your code and compile it with a different compiler then you can do whatever you want with THAT binary.

But as nix points out, the entire discussion is moot since the GCC license contains exceptions exactly for this situation.


[]()GCC, LLVM, and compiler plugins
Posted Jan 30, 2014 16:30 UTC (Thu) by **marcH** (subscriber, #57642) [[Link](http://lwn.net/Articles/583449/)]
> What matters is the direct textual inclusion of work licensed under two different licenses into a single aggregate,
The GPL is quite clear that it does not really care about aggregation.

The real question is about what makes a "Derivative Work" and - like Kugel and many others wrote - it is a quite complex and still debated question.



[]()GCC, LLVM, and compiler plugins
Posted Jan 30, 2014 17:22 UTC (Thu) by **vonbrand** (subscriber, #4458) [[Link](http://lwn.net/Articles/583466/)]
GPL could say it cares about mere aggregation until it was blue in the face, that wouldn't change anything: The law says mere aggregation doesn't matter. Likewise, what RMS would like to mean by "derivative" is totality irrelevant. It would have to be decided
 by a court.

[]()GCC, LLVM, and compiler plugins
Posted Jan 30, 2014 18:00 UTC (Thu) by **marcH** (subscriber, #57642) [[Link](http://lwn.net/Articles/583483/)]
A license is not a law

[]()GCC, LLVM, and compiler plugins
Posted Jan 31, 2014 15:05 UTC (Fri) by **nix** (subscriber, #2304) [[Link](http://lwn.net/Articles/583624/)]
The license only has any force because copyright law says it does.

[]()GCC, LLVM, and compiler plugins
Posted Jan 30, 2014 19:35 UTC (Thu) by **DOT** (subscriber, #58786) [[Link](http://lwn.net/Articles/583497/)]
A license can say that you may only use or distribute the program if you don't wear purple pajamas. It can't forbid your wearing of purple pajamas at all, but it can forbid using or distributing the program in that case. In the
 same way, it can put whatever restrictions on your use or distribution of the program in case you do it together with another, proprietary, program. This is not dependent on any standard definition of derivative work or any law whatsoever: it can attach any
 restrictions to the use of the program.

[]()GCC, LLVM, and compiler plugins
Posted Jan 30, 2014 20:34 UTC (Thu) by **vonbrand** (subscriber, #4458) [[Link](http://lwn.net/Articles/583506/)]
Licenses like GPL operate in the range regulated by copyright law. If the PPL says the software can't be used unless wearing purple pajamas, that restriction is void as copyright doesn't cover use, only copying and distribution. Likewise, coyright covers
 copying of a particular work, not if it is shipped together with other stuff (unless it becomes a part of a greater whole, in which case you are talking about coyright on the collection as such). And I believe there are some "reasonableness" conditions in
 what the license might demand.
In what is wrongly called "software licenses" (which are really contracts) you can agree to more stringent conditions.

[]()GCC, LLVM, and compiler plugins
Posted Jan 30, 2014 20:44 UTC (Thu) by **DOT** (subscriber, #58786) [[Link](http://lwn.net/Articles/583511/)]
Right, my mistake. Still, this only changes my argument slightly: a copyright license can still demand anything in return for giving you permission to copy or distribute. It may demand that you don't link it with proprietary software
 just like it may demand that you wear purple pajamas while doing the distributing.
This does restrict the power of the GPL: it cannot say anything about linking the program with a proprietary one on you own system. But it can say just fine that it considers such linking a BadThing, and it may not be distributed while it is a BadThing.


[]()GCC, LLVM, and compiler plugins
Posted Jan 30, 2014 22:39 UTC (Thu) by **madscientist** (subscriber, #16861) [[Link](http://lwn.net/Articles/583532/)]
I'm not sure what you mean regarding the GPL: the GPL doesn't try to enforce itself in a "mere aggregation" situation:[http://www.gnu.org/licenses/old-licenses/gpl-2.0-faq.html...](http://www.gnu.org/licenses/old-licenses/gpl-2.0-faq.html#MereAggregation)
> Likewise, what RMS would like to mean by "derivative" is totality irrelevant.

Well, it is VERY relevant in at least one way: it lets people using the software understand what the license holder considers the license to be, and THAT lets them know what behaviors are likely to cause them to face some sort of legal action from the FSF.

And, believe me, that's no small thing.

Sure, maybe the courts would decide the FSF is full of it. But so far we haven't seen anyone (in the US) willing to step up and take that risk, for whatever reason. One reason might be that their legal teams tell them it's a bad bet.


[]()GCC, LLVM, and compiler plugins
Posted Jan 30, 2014 22:33 UTC (Thu) by **madscientist** (subscriber, #16861) [[Link](http://lwn.net/Articles/583530/)]
nix's use of the term "aggregate" was unfortunate; I don't think he meant it in the legal sense of "mere aggregation". He meant it in the English language sense of "things put together".
It's pretty clear (I think) that a binary linked with libgcc is NOT a "mere aggregation" since if you remove libgcc the entire thing is useless and inoperable.


[]()GCC, LLVM, and compiler plugins
Posted Jan 30, 2014 23:51 UTC (Thu) by **marcH** (subscriber, #57642) [[Link](http://lwn.net/Articles/583541/)]
> It's pretty clear (I think) that a binary linked with libgcc is NOT a "mere aggregation" since if you remove libgcc the entire thing is useless and inoperable.
I don't think this is even relevant. If you push this logic further you eventually come to the conclusion that statically linking has different licensing implications from dynamically linking - even to the exact same library. Reading the GPL from some distance
 it does not look like its initial authors intended something that silly.

Licenses/contracts have one thing in common with laws: since they are written in natural languages they need to be interpreted; in the worst cases in a court. Such interpretations always take into account the intentions of the parties involved, including
 the intentions of the license authors.

The "spirit" of the GPL is clearly about the high-level, business question: "Is this software reusing GPL work / does it have a hard dependency on GPL work in some way"? as opposed to low-level engineering details like different linking techniques or network
 protocols between modules. Arguing otherwise is basically pretending that the authors of the GPL were idiots completely missing their own point. I don't think they were.

All this being said, with what you can see happening with patents there is of course still a good chance that any BS interpretation of the GPL out there could be validated in some court (and then invalidated in another).

Going full circle: yes I think the libgcc exception looks pointless, missing the philosophy of the GPL. Useless and harmless.




[]()GCC, LLVM, and compiler plugins
Posted Jan 31, 2014 1:46 UTC (Fri) by **artem** (subscriber, #51262) [[Link](http://lwn.net/Articles/583556/)]
Static linking obviously has different implications when you are distributing the binary. If linked statically, you are distributing bits from the library. If linked dynamically, you can distribute only your own bits, and tell
 your recipients to get library bits themselves. Look at the difference between GPL and LGPL - does it seem silly to you too?

[]()GCC, LLVM, and compiler plugins
Posted Jan 31, 2014 7:59 UTC (Fri) by **marcH** (subscriber, #57642) [[Link](http://lwn.net/Articles/583595/)]
> Static linking obviously has different implications when you are distributing the binary. 
No, static versus dynamic linking does not make any "obvious" difference with respect to the GPL. In fact the FSF argues the exact opposite.

"- Hi, is your program/work combined with any GPL code?
- No, it's not, I used dynamic linking as a workaround. Now don't forget to make sure you have libGPLx.so and libGPLy.so installed; without these there is not a chance it can work"

Do I find this made up example completely ridiculous and nonsensical? Yes. Could some red-neck court validate this anyway? Sure, why not? The sky is the limit.

This question has been debated a million times already, let's not do it once again. Just Google "GPL+dynamic+linking"

[https://www.gnu.org/licenses/lgpl-java.html](https://www.gnu.org/licenses/lgpl-java.html)


> Look at the difference between GPL and LGPL - does it seem silly to you too?

The LGPL is not defined in terms of static versus dynamic linking.



[]()GCC, LLVM, and compiler plugins
Posted Jan 31, 2014 13:59 UTC (Fri) by **mathstuf** (subscriber, #69389) [[Link](http://lwn.net/Articles/583619/)]
Without the text here, IIRC, the LGPL states something along the lines of that the LGPL portion must be replaceable by the end user. In practice, this is easy for shared but hard for static builds (as you'd have to ship you intermediate
 .a files per platform to avoid source).

[]()GCC, LLVM, and compiler plugins
Posted Jan 31, 2014 14:44 UTC (Fri) by **oshepherd** (subscriber, #90163) [[Link](http://lwn.net/Articles/583622/)]
Lets just say, for the purposes of example, that said GPLed library was libgcc.so.0 (We will ignore the existence of the runtime library exception for the purposes of this example)
Is my program bound by the terms of the GPL just because it was compiled with GCC, which linked it against libgcc.so.0?

What about if on <my|one of my user's> systems, libgcc.so.0 is a symlink to libcompiler-rt.so.0? We will note that the latter is under the MIT/X11 license.

One would not argue that Compiler-RT is derivative of libgcc, in spite of implementing exactly the same interface. (In fact to do so would be to claim that an API is copyrightable, which is both preposterous and has been proven false in at least one jurisdiction
 (the US))

What about if my application was linked against libgcc.so.0 in the year 2001, long before Compiler-RT was created as a project?

I find it hard to reconcile this collection of situations into a consistent rule set which doesn't imply that the claimed restrictions of the GPL on dynamic linking may be an exaggeration of the truth.

YMMV. IANAL, and all that.


[]()GCC, LLVM, and compiler plugins
Posted Jan 31, 2014 21:17 UTC (Fri) by **marcH** (subscriber, #57642) [[Link](http://lwn.net/Articles/583698/)]
When confusion reigns, it can be useful to take a giant step back. AFAIK the founding principle of the GPL is: if you take (something GPL) then you must give back (to the GPL community). I think it's quite clear that this is the
 rationale behind the "Derived Work" / "Combined Work" wording.
Joe is maintaining a very portable and closed source C application that runs on AIX and Solaris. One day his boss asks him to compile it on Linux for some new customers. Thanks to the good people at POSIX everything just works. Without Joe realizing it,
 GCC has sneaked some statically linked libgcc binary into his program (most developers have never heard about things like libgcc). Then he ships his closed source application as a binary to his Linux customers.

Joe never touched GCC or any GPL software before. Even though he did not change one line in his application he now has to give his entire program "back" to the GPL community because of some (irrelevant) implementation detail he did not even need to be aware
 of? Sorry but that does not make any kind of sense. Joe did not take anything from the GPL community. You could rather argue he made the effort to support GNU/Linux.

Of course not every case is that simple. Yet this is one is perfectly realistic and makes the libgcc exception ridiculously pointless.



[]()GCC, LLVM, and compiler plugins
Posted Jan 31, 2014 21:43 UTC (Fri) by **anselm** (subscriber, #2796) [[Link](http://lwn.net/Articles/583704/)]
It is a very widespread belief that distributing a proprietary program that includes GPLed parts means that the whole program must be put under the GPL. That belief is, however, incorrect.
You never »have to give your entire program to the GPL community« if you inadvertently distributed it with some GPL code linked into it. Putting your own code under the GPL is just one of the options; you can also remove the GPL code in question and replace
 it with something under a more suitable license, or negotiate a separate license deal with the copyright holder(s).
In the GCC case, the libgcc license contains explicit language allowing you to link your stuff with libgcc and propagate the result, so as far as GCC is concerned the issue is moot, anyway.

[]()GCC, LLVM, and compiler plugins
Posted Feb 1, 2014 11:22 UTC (Sat) by **hummassa** (subscriber, #307) [[Link](http://lwn.net/Articles/583780/)]
Anyway, the example is weird, and it's easily shown:
Joe is maintaining a very portable and closed source C application that runs on AIX and Solaris. One day his boss asks him to compile it on Windows for some new customers. Thanks to the good people at POSIX everything just works. Without Joe realizing it,
 MSVC has sneaked some statically linked msvcrt binary into his program (most developers have never heard about things like libgcc). Then he ships his closed source application as a binary to his Windows customers.

Joe never touched Microsoft software before. Even though he did not change one line in his application he now has to obey arbitrary Microsoft licensing terms? Sorry but that does not make any kind of sense. Joe did not take anything from Microsoft. You could
 rather argue he made the effort to support Windows.

Now, even if libgcc was 100%, just-GPL, don't let proprietary software near me, it's Joe's boss responsibility to follow the license for some software his company uses. Because it would be a liability to do otherwise... It is quite simple.


[]()GCC, LLVM, and compiler plugins
Posted Feb 1, 2014 12:39 UTC (Sat) by **khim** (subscriber, #9252) [[Link](http://lwn.net/Articles/583785/)]
Joe did not take anything from Microsoft.Really? Where exactly msvcrt comes from?
You could rather argue he made the effort to support Windows.Does not matter. He shipped code made by Microsoft, he must play by Microsoft rules.
It's not a new development. Think dBase II (thirty years old by now): if you compile you program using dBase II compiler and want to ship the resulting .exe you need to pay royalty! Even if you didn't knew anything about Ashton-Tate and wrote you program
 without prior knowleadge of dBase (pretty hard to do in 1981, but we are talking hyphothetical possibilities here, right?)—you still need to pay royalty because said .exe includes bits and pieces owned by Ashton-Tate!
License fee for runtime package may not be common today (software is cheaper today as a matter of course, some is even free), but it was the norm half-century ago. And if law can force you to pay actual money for the privilege (the ability to actually ship
 compiled binary is a privilege not right as law is concerned!) then why it can not force you to do other things, too?

[]()GCC, LLVM, and compiler plugins
Posted Feb 6, 2014 9:51 UTC (Thu) by **hummassa** (subscriber, #307) [[Link](http://lwn.net/Articles/584441/)]
whooosh? The sound of the parody I made passing over?
My point exactly. GPL's terms + copyright law = if you want to distribute it, follow the terms of the license.


[]()GCC, LLVM, and compiler plugins
Posted Feb 6, 2014 18:59 UTC (Thu) by **Wol** (guest, #4433) [[Link](http://lwn.net/Articles/584602/)]
This dBase II example is hilarious, given that there is a pretty nice sting in the tale :-)
You do know that the original dBase was Public Domain, don't you? And there was a lawsuit over copying, which Ashton Tate pretty resoundingly lost ...

Cheers,
Wol


[]()GCC, LLVM, and compiler plugins
Posted Feb 6, 2014 19:54 UTC (Thu) by **khim** (subscriber, #9252) [[Link](http://lwn.net/Articles/584613/)]
Said lawsuid had nothing to do with dBase runtime and everything to do with [dBase language and file format](http://en.wikipedia.org/wiki/Ashton-Tate#Lawsuits). Basically Ashton-Tate
 wanted to outlaw [Clipper](http://en.wikipedia.org/wiki/Clipper_(programming_language)) and [FoxPro](http://en.wikipedia.org/wiki/FoxPro) and
 failed. This defeat meant you could use **Clipper** runtime for free but if you wanted to use orginal **dBase II runtime** for whatever reason you **still** needed to pay even after that “resounding defeat”. And of course
 all that happened much, **much** later in an era of dBase IV, not dBase II.
[]()GCC, LLVM, and compiler plugins
Posted Feb 1, 2014 13:25 UTC (Sat) by **marcH** (subscriber, #57642) [[Link](http://lwn.net/Articles/583791/)]
Since when Microsoft licences have anything in common with the GPL??

[]()GCC, LLVM, and compiler plugins
Posted Feb 2, 2014 0:49 UTC (Sun) by **mathstuf** (subscriber, #69389) [[Link](http://lwn.net/Articles/583847/)]
That you must conform to the terms and conditions before redistributing?

![](http://engine.adzerk.net/i.gif?e=eyJhdiI6MTM1MzEsImF0Ijo2LCJjbSI6MTk5ODYsImNoIjo3MzE5LCJjciI6NTIyNDIsImRpIjoiNzkwYzgyNGE4NmM1NDYwY2EwYzYzODgzZTA0NzRjYzQiLCJkbSI6MSwiZmMiOjc0NTY1LCJmbCI6MzgxMTksImt3IjoidDpodHRwIiwibnciOjQ2NjksInBjIjowLCJwciI6MTc2MjUsInJ0IjoxLCJzdCI6MjA5NzksInpuIjoxNjAyNywidHMiOjEzOTM2NTU5NjA4ODB9&s=fFuLldrP3QZYhuv3tdlRB6bojyQ)Copyright © 2014, Eklektix, Inc.
Comments and public postings are copyrighted by their creators.
Linux is a registered trademark of Linus Torvalds

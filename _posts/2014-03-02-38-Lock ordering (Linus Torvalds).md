---
layout: post
title: Lock ordering (Linus Torvalds)
original: https://blog.csdn.net/zedware/article/details/20303913
---
撞到这么一个文章，Copy and paste：
[
Index](http://yarchive.net/comp/index.html) [Home](http://yarchive.net/home.html) [About](http://yarchive.net/about.html) [Blog](http://yarchive.net/blog)
***
[]()[]()	From: Linus Torvalds <torvalds@linux-foundation.org>
	Newsgroups: fa.linux.kernel
	Subject: Re: [RFC][PATCH 7/7] lockdep: spin_lock_nest_lock()
	Date: Wed, 08 Oct 2008 15:45:34 UTC
	Message-ID: <[fa.D2pIV1iJBQnDZoW5vft6Be6fEt8@ifi.uio.no](http://groups.google.com/groups/search?as_umsgid=fa.D2pIV1iJBQnDZoW5vft6Be6fEt8%40ifi.uio.no)>
	
	On Wed, 8 Oct 2008, Steven Rostedt wrote:
	> >
	> > And yes, if there is an outer lock, even the order of getting locks is
	> > irrelevant, as long as anybody who gets more than one inner lock always
	> > holds the outer one.
	>
	> But I need to disagree on a programming practice style.  Unlocking locks
	> in a non nested order is just bad programming practice.
	
	No it is not.
	
	Unlocking locks in non-nested order can sometimes be very much the right
	thing to do, and thinking otherwise is (a) naive and (b) can generate
	totally unnecessary and pointless bugs.
	
	The thing is, sometimes you have to do it, and imposing totally made-up
	rules ("unlocks have to nest") just confuses everybody.
	
	The FACT is, that unlocks do not have to nest cleanly. That's a rock solid
	*FACT*. The locking order matters, and the unlocking order does not.
	
	And if you cannot accept that as a fact, and you then say "unlock order
	should matter just to keep things nice and clean", then you end up being
	screwed and/or confused when you can't hold to the unlock order.
	
	There are many perfectly valid reasons not to unlock in reverse order.
	Don't create make-believe rules that break those reasons for no gain.
	
	For example:
	 - let's say that you have a singly-linked list of objects.
	 - you need to lock all objects, do something, and then unlock all
	   objects.
	 - the *only* sane way to do that is to just traverse the list twice.
	 - that means that you will unlock the objects in the same order you
	   locked them, _not_ in reverse ("nested") order.
	 - if you force a rule of "unlocks must be nested", then
	
	        YOU ARE A F*CKING MORON.
	
	It's that simple. Don't do made-up rules that have no technical reason for
	them.
	
	Lock ordering matters. Unlock ordering does not. It really is that simple.
	Don't confuse the issue by claiming anything else.
	
	                Linus
	
	
	
***
[]()	From: Linus Torvalds <torvalds@linux-foundation.org>
	Newsgroups: fa.linux.kernel
	Subject: Re: [RFC][PATCH 7/7] lockdep: spin_lock_nest_lock()
	Date: Wed, 08 Oct 2008 16:20:52 UTC
	Message-ID: <[fa.Hxf2nbygEuDKujp2k5dIpZ4WMNg@ifi.uio.no](http://groups.google.com/groups/search?as_umsgid=fa.Hxf2nbygEuDKujp2k5dIpZ4WMNg%40ifi.uio.no)>
	
	On Wed, 8 Oct 2008, Steven Rostedt wrote:
	>
	> Unfortunately, you cut out my comment that I stated "unless there is a good
	> reason not to", which the below example is a good reason ;-)
	
	Well, the thing is, as long as you think nesting is good, you end up being
	very confused when it isn't.
	
	> I just find that seeing something like:
	>
	>    lock(A);
	>    lock(B);
	>
	>    [do something]
	>
	>    unlock(A);
	>    unlock(B);
	>
	> just seems to be sloppy.
	
	Of course you'll often get nesting almost by mistake.
	
	For example, any normal locking that is hierarchical tends to nest
	naturally, especially if you end up using lots of small functions (which
	you should). Or simply due to error handling.
	
	So in that sense, nesting may be a "natural" thing, but partly exactly
	_because_ it is fairly natural, we should not try to make it even more
	than that, because then when things don't nest (and it does happen), if
	the "things should nest" camp gets too strong, bugs happen.
	
	They happen because somebody looks at the non-nesting case, and thinks
	it's sloppy, and tries to fix it. And in the process just complicates
	matters - and very likely introduces bugs.
	
	And THAT is why people shouldn't think that locks "should" nest. Because
	they shouldn't. They often do, but we're better off making people very
	aware of the fact that 'often' isn't 'good design', it's just a matter of
	'it happens in practice' rather than 'it should be that way'.
	
	I know for a fact that some people thought unlocking in non-nested order
	was a bug. And I believe that belief is a dangerous one.
	
	                                Linus
	
	
***
  [Index](http://yarchive.net/comp/index.html) [Home](http://yarchive.net/home.html) [About](http://yarchive.net/about.html) [Blog](http://yarchive.net/blog)



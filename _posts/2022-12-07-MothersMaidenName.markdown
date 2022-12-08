---
layout: post
title:  "Why Your Mothers Maiden Name Doesn't Work Anymore"
date:   2022-12-06 12:44:14 -0400
categories: TechnicalBlog
---

Long time, no blogs. I've been crazy busy with a new consulting gig and Q4. Glad to be pushing something out that I've been passionate about the last few weeks.

By the name of this blog post, you can probably guess what I'm going to rant about this time. That's right, those damn security recovery questions you can't ever remember.

In a few recent penetration tests i've been on, I've seen an increase in self service portals (ManageEngine in particular) and for me, i'm always drooling over these. In particular, the account perspective, there is always a way to identify valid user enumeration. I want to share some light on how easy some of these are and make some recommendations to help secure these more as I seem them poorly implemented.

#### Proof Of Concept

I labeled this a "TechnicalBlog" so I've got to show SOMETHING. I'm going to redact a ton of stuff from the following but hopefully you will be able to understand.

Step 1 : Identify Valid Usernames

I normally use [statistically-likely-usernames](https://github.com/insidetrust/statistically-likely-usernames) as it's a list of the most common names in the US in your chosen masked format. I normally use some form of error verbose response or traffic direction to identify valid accounts. For Example, the reset password functionality on this site was the culprit for account enumeration. Valid accounts were 302 redirected to 2FA whereas invalid accounts were 302 to authFailed.


![Invalid.jpg](/assets/images/Mothers/invalidaccount.jpg)
*Invalid Account*


![Valid.jpg](/assets/images/Mothers/ValidAccount.jpg)
*Valid Account*


Once I ran 50k usernames through here, I was able to identify a few hundred. I do want to mention that this functionality had some mitigations and would lock my IP out after 25ish requests but that is an easy win and I just proxied my traffic through some proxies rotating my src IP every request.

So from here, you'll have to manually vet some of the questions or recovery methods, I've seen phone 2FA, Google auth, and email confirmations that suffice my attack path but I found that in lots of pentests, it will be a mix of all of them. Once I identify questions that are weak, I begin my OSINT search. To save my methodology, typing words, and you having to read a bunch of nonsense, I was able to identify a users that had the following three questions:

- What is your Mothers Maiden name?
- What is you Date of Birth?
- What is your Favorite Color?

From here, I just did my OSINT stuff on particular users finding the more internet open users, I found a user that their facebook was public alongside their family connections. I'm really going to redact this as I want to protect the user.

![Facebook](/assets/images/Mothers/Facebook.jpg)
*Users Facebook Relationship Page*

BOOM! That's 1/3... Next is Date of Birth (DOB), using public voter records, I found the DOB! I was also able to find this on their wedding info and cellular information.


![DOB](/assets/images/Mothers/Voter.jpg)


BOOM! That's 2/3... Well I wish I could say I found their favorite color but ROYGBIV baby! The account questions didn't lockout or rotate so I brute forced the color on my second guess.

![Reset](/assets/images/Mothers/Reset.jpg)


I was able to reset this users password and breach the perimeter this way.

#### Recommendations

This is something the industry is moving away from but it's still frequent. Using some of the following will prevent a lot of these attack paths:

- Don't use trivial or guessable questions. Favorite color (unless it's something crazy) has ROYGBIV which is a whopping seven potential answers. DOB and mother's maiden name is weak in todays internet surface. Questions are being outdated but questions like favorite childhood friend, favorite teacher name, etc are a lot stronger and less guessable.
- Rotate the questions. Have users give five and only leverage three to reset the password with rotating them every failure. Lock the account after 3 attempts so some can't brute force answers.
- Move away from questions to 2FA or Token based. This is the most secure way for end users.

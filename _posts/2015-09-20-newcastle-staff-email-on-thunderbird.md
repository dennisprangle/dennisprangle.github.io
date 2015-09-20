---
layout: post
title: "Newcastle staff email on thunderbird"
description: ""
category: Software
tags: [Newcastle university, Email, Thunderbird, Exchange]
---
{% include JB/setup %}

I've recently started worked at Newcastle University and found it a bit tricky to read my staff email with thunderbird.
In case it saves anyone else some time, here's the settings I used.
This worked for me as of September 2015.

* I used the thunderbird add-on [ExQuilla](https://exquilla.zendesk.com/home).
This accesses Microsoft Exchange email accounts.
It requires a $10/year license, but has a 60 day free period where you can decide if you like.
* Create a new account email account with ExQuilla
* Put in your email address and password
* Log in with userid (`youruserid@newcastle.ac.uk`) and leave domain blank.
* Specify manual EWS URL: `https://outlook.office365.com/EWS/Exchange.asmx` and complete other details as you wish.
---
layout: post
section-type: post
title: How to install Sonartype Nexus Server
category: tech
tags: [ 'tutorial', 'nexus', 'dependencies' ]
---


In my current job position, I have to improve an very old architecture to a brand new one.

The first thing what caught my attention was that there is not any CI environment, no jenkins, no sonar, even no a version control system like maven, gradle...., they manage their libraries manually!!!.

To improve step a step this chaos, I will ask to my CTO a new server in amazon, to fill it with some utilities like a nexus repo manager, jenkins and sonar for the CI, etc.

I don't have still the server but i'm reading on internet and thanks to the blog of <a href="http://chrisjenx.com/sonatype-nexus-aws-ec2/" target="_blank">Christopher Jenkins</a>, I've been able to do it easily.

I want to thank him its help. With their blog entry and a only few advices from my i think you will can install your own sonartype nexus server to manage propertly all your development libraries. Also you can use to manage the libraries of your own projects to mantain versioned.

I don't want go on too long about this because if you follow Christopher's blog you can install your sonartype server. Only one thing that happens to me, ensure that in your EC2 instance have the 8081 port open (or whatever port you want to use in your server).

With these simple steps you can easily install your dependencies repository.

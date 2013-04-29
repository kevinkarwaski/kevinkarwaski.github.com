---
layout: post
title: "ChefConf 2013 - Roundup"
date: 2013-04-28 16:29
comments: true
categories: [chef, chefconf, berkshelf, vagrant]
---

Hot off the heals of ChefConf 2013 I wanted to take a moment to talk about some of the relevent takeaways. Many of the ideas my team and I have been kicking around were loudly validated throughout the sessions and here are few worth mentioning...

<!-- more -->


### 1. Use [Berkshelf](http://berkshelf.com/) and [Vagrant](http://www.vagrantup.com/). 

- See [Jamie Windsor's](https://twitter.com/resetexistence) keynote at ChefConf 2013 on ["The Berkshelf Way"](http://www.youtube.com/watch?v=hYt0E84kYUI)
- See [Mitchell Hashimoto's](https://twitter.com/mitchellh) ChefConf 2013 session on Vagrant.

For the last few months I've been trying to develop better Cookbook Authoring habits and cross team cookbook collaboration with the developer teams in my org. Using advanced tools like Berkshelf / Librarian / Vagrant to simplifiy cookbook dependency management and decrease the iteration testing cycle seemed like a natural fit.

While I had kicked the tires of Librarian, I had not gone down the Berkshelf path which is unfortunate because Berkshelf was mentioned time and time again throughout many of the sessions. Indeed, it appears to be quickly becoming the defacto standard for cookbook dependency managment and development and for good reason.

Its rich integration with Vagrant really makes it shine as a super efficient cookbook development tool. But in the end a tool is just a tool and without a planned development process or framework it remains just that; a tool.

Enter "The Berkshelf Way" of cookbook development. It is fundamentally a [Readme Driven Development](http://tom.preston-werner.com/2010/08/23/readme-driven-development.html) process. There are four main components:

- **Work in verticals from the outside in:** Start with the application or end result and work your way back to the services and dependencies that actually drive the thing you are trying to develop. Create a cookbook for every single service of application you have. Apply common design patterns to each of the components in the cookbook.

Cookbook Patterns: Application, Library and Wrapper

- **Favor Data Driven Cookbooks:** Encourages the reuse of public cookbooks by changing the cookbooks behavior at runtime. Favor configuring with attributes rather than data bags -or- encrypted data bags. Only use data bags for org wide (base role) info: users, groups, apt/yum repos, 

- **Write well-encapsulated cookbooks:** Work on cookbooks in isolation so they can be as portable as possible. How do roles fit in? They don't. Use a recipe instead. Roles aren't versioned and typically contain org level data. What about data bags. Use them where they make sense. Always validate the databags.

- **Have short interation loops:** Using the vagrant-berkshelf plugin you can provision and test the cookbooks extremely quickly.

This is a fantastic tool and development process we're hoping to adopt at my org.

### 2. Avoid the Chef Code Antipatterns.

See [Julian Dunn's](https://twitter.com/julian_dunn) session at ChefConf 2013 on Antipatterns.


### 3. Package everything.

More to come...

### 4. Treat your Infrastructure like Garbage... because it is.

More to come...

### 5. Don't be a jackass; always use AWS AutoScale groups.

More to come...

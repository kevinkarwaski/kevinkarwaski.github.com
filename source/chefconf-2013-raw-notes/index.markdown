---
layout: page
title: "chefconf-2013-raw-notes"
date: 2013-04-28 17:06
comments: true
sharing: true
footer: true
---
-----------------------------------------
Day 1 keynotes with Facebook and Forester Research


Facebook:

Scaling config management

The Goal:
 - 4 people
 - 10k+ hetero systems
 - service owners make changes (devs)

 1. Building blocks:
  - Distributed - everything on the client
  - Deterministic - The system you
  - Idempotent - only nessesary
  - Extensible - tied to internal systems
  - Flexible - no dictated workflow

 2. Configuration as data

 3. Flexibility
  - Adapt to our workflow
  - fast proto
  - internal assumptions can be changed - easily
  - extend in new ways - easily

Why Chef?

Easier to see from a problem
node.save
 - scale issues with 15k-node cluster
 - standard solution sucks (disable ohai plugins)

 solution: whitelist_node_attrs:
 - https://github.com/opscode/whitelist-node-attrs

 Desired workflow:

  - provide api for service owners to extent configs
  - engs don't need to know what they're building on
  - engs can change their systems without changing anything else
  - testing should be easy

  - Moving idempotency up
   - stale idepotentent records
   - the removal of the resource should remove it from the node

 Chef Infrastructure:

  - OSC and OPC
   - bought private chef for support...

  Required:
   - stateless chef servers
    - no search
    - no data persistence
    - no databags
   - separate failure domains
   - tiered model
  
  all stats and errors shipped to internal monitoring solution

  Chef client is crond at 15 minutes (15 min splay)

  cpu dropped from 80% to 67% to register 15k nodes in OSC... yeah.

  Lessosns:
   - idempotent sys > idempotent records
   - Full lang > DSL
   - scale is more that just num of clients
   - easy abstraction is critcal
   - testing against real systems is usefull and nessessary

 Summary:
  - how many homogen sys can you maintain >> 17k
  - 4 people of FB ops


Forester Research:


 - Patterns in IT mirror the industrial revolution.
 - Big changes on the horizon
 - Economic and complexity driving disruption; can't continue
 - poorly managed disruption = volatility (business hates this)

 How's it changing?

 - more industrialized because it's predictable
 - cost effective (less waste)
 - productivity
 - managed risk

 Currently too much risk in IT (traditional)

 We are living through the next wave of the idustrial revolution

  - put discipline in the process
  - if it's not strrategic, let someone else do it.
  - Automate everything
  - Adapt the people

  Process: Use ITIL as a guide
   - start with "low hanging fruit"
   - apply to whole service lifecycle
   - break tradition / use process
   - don't automate unless you have discipline in process!!

  Strategic:
   - is it innovative? what is innovative?
   - differentiate business?
   - is it intellectual grunt work?
   - Focus on business needs

"Automation is evolution of trust."

Will it replace my job? maybe, but resistance is futile. :)

Systems Administrators are dead.


-----------------------------------------
Antipatterns


Number 1 rule:

 - Nothing replaces good / advanced planning.

 Adv planning:
  - what cookbooks should you have
  - what recipes
  - roles and their names
  - how many envs
  - clusters within env
  - DB heirarchy

 Top 10 anitpatterns:

 10. Giant git repo for chef repo:
  - mixing rtemoral data with versioned data

 
 9. One giant company cookbook
  - company is not a top level service
  - Huge blast radius on changes

  better: more cookbooks are better


 8. Using envs for more than just logical env
  - don't be tempted to 


 7. Forking community cookbooks

  better: Rather use app/library cookbook pattern (smpostgresql)


 6. Run list in Role
  - Roles aren't versioned

  better: create a roles cookbook each recipe contains run list.


 5. Disorganized Data bags
  - Only two levels
  - Avoid making enormous


 4. Not knowing about or using the chef-shell
  - IRB + chef primitives
  - cookbook devel
  - prod debugging
  - See intro to chef-shell (slideshare)


 3. Who's afraid of the Big Bad LWRP
  - Myth: they're hard to write


 2. "Not invented here" Syndrome
  - Bias against using other peoples code
  - temptation to write your own

  Better: do research / look for what others have done!


 1. The Lone Wolf Chef
  - Chef configs apps; get devs involved in writing and maintaining cookbooks.
  - Everyone becomes responsible for prod ready.


-----------------------------------------
TestKitchen

 - Born out of LDAP testing
 - Heavily influcened by TravisCI
 - started as internal tool

 - Best way to test in on a "real" system... converge a real node.
 - Gathered requirements for test tool:
  - cross plateform
  - regressions
  - lint / unit
  - functional / integration
  - Jenkins / CI
 - Dev continued
 - Limitations:
  - Adding new providers
  - Override local customizations (vagrantfile)

 - JamieCI: the accidental project
  - created RVM cookbook (converges +40mins)
  - Came out of Friday Night Hacks
  - Make a Vagrantfile to test all platforms (insane!!)
 
 - Jamie codebase + Test Kitchen name = 1.0+

Concepts:

 - based on .kitchen.yml file
  - description of testing problem
  - declarative and static
  - platforms = OS or target system, networking, virtualized, Chef version, etc.
 - Contains: name, driver_config, run_list
 - Suite: the chef configuration
  - name, run_list, attributes
  - instance = suite + platform

 - Lifecycle activities:
  - Create
  - Converge
  - Setup
  - Verify
  - Destroy
 
 - Instance Actions:
  - Test
 - Driver: implementation of instance actions
  - Ship as rubygems
  - EC2, vagrant, etc.

 Dry run:
  - Install gem
  - kitchen init
  - kitchen test

 The future:
  - 3rd party integrations
  - Documentation

-----------------------------------------
Chef in the Cloud

mashion.net

 - Make buckets private
 - USE IAM ROLES

 - Formerly used Librarian
 - ran single instances
 - AMI MUST SUPPORT USERDATA
 - Use AS groups always. Always. Even for singletons!!

 Userdata used to:
  - Install s3cmd
  - Install chef
  - Download cookbooks from s3
  - run chef-solo
  - The good: no spf, everything mostly automated
  - The bad: no envs, config changes were painful, multi-apps

The move to chef-server:
 - wanted env support
 - and chef search
 - moved to VPC
 - ELB in VPC
 - NAT box in private VPC
 - USE BIG NAT BOXES
 - Routing tables are hard / easy to fuck up.
 - Switched over to Berkshelf / Jenkins integration.

Cloned Asgard in Ruby / CLI / not open-sourced

 - ELBs + ASGs make rolling deploys easy
 - Using Blue / Green deploys
 
 - convergence failures due to hostname collisions
 - Env issues bases on lack of versioning, retreated attributes to cookbooks and not in envs.
 - used combo of node/client delete in runlevel 6 and ran a node GC collector

 Using RPMs and AMIs!!

  Why? 
  - Reduce time to Boot
  - Build RPMs / cookbooks quickly (using fpm and internal YUM repo)
  - ensure convergence
  - Build AMI for app-version
  - Promote AMIs via environments
  - Back to chef-solo
  - one cookbook, installs rpms

  Using Netflix Cloudstack:
  - Archaius
  - Eureka
  - Asgard (use groovy to update userdata... not fun.)
 
  Run chef to create and bundle AMI.
   - Super fast boot time (15 seconds to be operational)
   - Rolling deploys are easy
   - No restarts on service discovery

-----------------------------------------
Cookbook Refactoring

Case Study:
Host file cookbook

USE Chefspec... it's really fucking fast.

Every cookbook should fully manage itself.


-----------------------------------------
The Berkshelf Way

Jamie Windsor

What is it?
 - CL tool
 - scm tool
 - pack manager
 - partial knife replacement

berks init to convert current cookbooks


metadata.rb
 - version
 - set supported platforms
 - set dependencies

Berksfile
 - Like Gemfile
 - Like rubygems, cookbooks get installed (by default) ~/.berkshelf

Deved under readme based development
 - Works in verticals from the outside in
 - Create it base on Service or Application
 - Apply design patterns to all components

 Application Cookbook Pattern

 MyFace: LoadBalancer, app server, etc.
  - They are all recipes.
  - There are private / public recipes

 Favor Data-driven cookbooks:
  - Change cookbooks behavior at runtime
  - 3 ways to config: attributes, data bags, encrypted
   - favors attributes
   - data bags for org level : users, groups, yum/apt, things done by base cookbook

 Encapsulation:
  - Build portable cookbooks.
  - Stop using roles, not ver, cant be packed, org level data, not namespaced
  - use Databags where they make sense and validate them
  - default recipe is special; simply list includes.

 Library Cookbook Pattern:
  - might have recipes
  - contains lwrp, definitions, libraries

 Wrapper Cookbook Pattern:
  - Super lightweight
  - few recipes and attribute overrides

 Iteration Loops
  - Make them as short as possible

Coming soon... motherbrain!


-----------------------------------------

Vagrant

A tool for creating, managing, and distributing a portable dev env.

Perfect for iterative cookbook dev.

dev / test / prod use the same cookbooks on the same OS.

Vagrant Book coming out in July.

0.1 released in 2010
1.0 released in 2012
1.1 released in 2013 (removes VirtualBox restriction)
2.0 will be awesome

Multi-provider
 - A provider manages compute resources.
 - in 1.0 only provider was vb
 - in 1.1 many more: VB, Vmware, AWS, Rackspace, LXC

Why?
 - VB is not great for every situation (sucks)
 - enables new and awesome things
 - work in envs that work best for you

 CI:
  - AWS, LXC

 Develop Locally, Test Remotely

 Deploys (not recommended)

How?
 - vagrant up --provider=aws

Boxes are now tied to providers: AMI/metadata for AWS

Provider specifc configs

-----------------------------------------

Lightning Rounds
Application Automation Deployments

Deployment Patterns:
 - Starting / Stopping / Shipping of code

 - Pull based deploys... deploy when ready, chef deploy resource
  - fast / auto
  - but limited in scope

 - push based deploy... deploy when told
  - complex workflows
  - internode triggering
  - leverage chef api
  - retrofitable
  - but another tool to manage

 Config patterns:
  - storing config data
  - deploying config data
  - storage: roles, library / wrapper cookbooks

Kanban for Operations
Jeffery Hulten - Automated Labs @jhulten

Keys: Pull based flow, Limit WIP
Make unplanned work visable
keep flow in one direction

https://github.com/adamhjk/metarepo

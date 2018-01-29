---
title: Outline Of Continuous Delivery With-windows-and-net
date: 2018-01-29 17:40:00
tags: [CI, CD, dotnet, windows]
---
[Original PDF Download](http://www.oreilly.com/webops-perf/free/files/continuous-delivery-with-windows-and-net.pdf)

## Foreword
1. Continuous Delivery is grounded in a pragmatic, empirical approach to software development.

1. There are at least two factions: the world of Windows and .NET, and the Unix-based tribes of the “Open Stack.”

## CHAPTER 1 Introduction to Continuous Delivery
1. Continuous Delivery aims to enable regular, rapid, reliable software releases through a set of sound practi‐ ces, giving the people who “own” the software product the power to decide when to release changes. P.1
1. Continuous Deployment is a push-based approach: when software developers commit a new fea‐ ture to version control, it is pushed toward the Live systems auto‐ matically after successfully passing through a series of automated tests and checks.   P.2
1. The Importance of Automation for Continuous Delivery. P.3
    * Software compilation and unit testing (“builds”)
    * Software testing (component, service, integration, and UI)
    * Deployment of software and infrastructure through all environ‐ ments, including Production
    * Configuration of applications and infrastructure (including net‐ works, DNS, virtual machines [VMs], and load balancers)
    * Database changes (reference data, schema changes, and data migrations)
    * Approval of everyday IT changes (“standard changes”)
    * The tracking and tracing of change history and authorizations
    * Testing of Production systems
1. Why Is Continuous Delivery Needed?
    * Developing and operating modern software successfully requires a combination of good tools, well-trained and well-socialized people, good team discipline, a clear and shared purpose, and well-aligned financial and organizational goals.
1. Why Windows Needs Special Treatment    P.4
    * Plain text  les for con guration of applications and services (rather than the Windows Registry or databases)
    * Controlling application behavior from the command line (rather than a GUI)
    * A multivendor, open source–friendly approach to software in the Windows ecosystem
    * Easily scriptable package management with declarative depen‐ dencies
1. Terminology    P.5
    * Artifact
    * Continuous Delivery (CD)
    * Continuous Integration (CI)
    * Cycle time
    * DBA
    * DSC
    * DSL
    * IIS
    * Infrastructure
    * Infrastructure con guration management (or just con g manage‐ ment)
    * Infrastructure as code
    * LDAP
    * On-premise
    * Package management
    * Production
    * SCCM
    * Software-as-a-Service (SaaS)
    * SysAdmin
    * Test-driven development (TDD)
    * User interface (UI)
    * Version control system (VCS)

## Chapter 2 Version Control
2. Key Version Control Practices  P.7
    * Commits to version control should be cohesive and meaningful, helping people to see why a change was made.
    * Commits should happen many times per day—if your version control system or practices work against multiple daily com‐ mits, you’re likely using the wrong approach.
    * Branch as little as possible (see “Branching Options” on page 11)—use techniques such as feature toggles to manage partially completed work. Focus on completing a small number of changes rather than tackling many things in parallel.
    * Any non-trunk branches should be short-lived—especially fea‐ ture branches.
    Version Control Technologies  P.8
    * Git
    * Mercurial
    * Subversion
    * TFS

2. Branching Options  P.11
    A developer has a problem to fix, so they create a feature branch. Now they have two problems to fix. --Anon.
    * Pull Requests
    * Feature Toggles
    * Use NuGet for Dependencies
    * Do Not Store Packages in Version Control
    * Use NuGet to Manage Internal Dependencies

2. Do not be tempted to rush into branching just because your version control tool has good branching support. Git in particular supports branching well, but Git was designed for collaboration on the Linux kernel code by geographically separated individuals. Your software teams (should) have much better communication than the Linux kernel team, so give preference to communi‐ cation instead of branching!    P.12

2. Originally, the NuGet package format was for build- time dependencies only, although several tools now use the NuGet format for deployable artifacts too. For a clearer distinction between build-time and run-time dependencies, use Chocolatey, currently the de facto apt-get tool for Windows and .NET. In the future, other package managers may appear, facilitated by the emerging OneGet packager manager management framework.    P.14

## Chapter 3 Continuous Integration
3. Many organizations assume that they are “doing CI” if they run a CI Server (see below), but good CI is a well-defined set of approaches and good practices, not a tool.     P.15
3. Remote build agents require a physical or virtual machine, which can be expensive. A possible route to smaller and cheaper build agents is Windows Nano, a lightweight version of Windows announced in April 2015.   P.16
3. CI Servers for Windows and .NET  P.15
    * AppVeyor
    * Bamboo
    * BuildMaster
    * GoCD
    * Jenkins
    * TeamCity
    * TFS Build / VSO
3. Automation P.22
    * Build
    * Test
    * Package
    * Deploy
3. Tools P.23
    * MSBuild
    * PSake
    * Ruby/Rake
    * NAnt
    * Batch Script
3. Architecture Changes for Better CI P.26
    * Use smaller, decoupled components
    * Use exactly one .sln file per component or service
    * Ensure that a solution produces many assemblies/DLLs but only one component or service
    * Ensure that each .csproj exists in only one .sln file, not shared between many
    * Use NuGet to package internal libraries

# Chapter 4 Deployment Pipelines
4. Reason   P.27
    * To automate the different build, test, and deployment activities
    * To visualize the progress of software toward Production
    * To find and reduce bottlenecks or wait times in the deployment process
4. Tools    P.28
    * GoCD
    * Octopus
    * TeamCity
    * VSO
    * Inedo BuildMaster
    * IBM UrbanCode Deploy
    * XebiaLabs XL Deploy
4. Deployment Techniques     P.32
    * Use Blue-Green Deployment for Seamless Deploys
    * Canary Deployments
    * Postdeployment Checks
    * Smoke Tests
4. Decouple File Delivery from Code Availability    P.35
    * Delivery of files onto a server
    * Making the new software active
4. The most effective rollback is simply to redeploy the previous soft‐ ware version using the deployment pipeline. P.35
4. Database P.35

TO BE Continue...
    
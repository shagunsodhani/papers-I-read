---
layout: post
title: Searching for Build Debt - Experiences Managing Technical Debt at Google
comments: True
excerpt: 
tags: ['2012', 'Build System', 'Software Engineering', 'Technical Debt', Engineering, IEEE, Software, Systems]


---

## Introduction

* The paper describes the efforts to control and repay the technical debt in the build system at Google (called the Build Debt).

* Guiding Principles:

    * Automate techniques to analyze and fix issues that contribute to technical debt.

    * Make it easier to do the right thing as developers can incur technical debt unknowingly.

    * Make it hard to do the wrong thing, e.g., by building stricter checks into the build process.

* Note that some of the metrics and design decisions may be outdated now (the paper was written in 2012). However, the core message is still relevant.

* [Link to the paper](https://static.googleusercontent.com/media/research.google.com/en//pubs/archive/37755.pdf)

## Google's Build System Debt

*  BUILD files encapsulate the specifications for building software.

* Generally, these files are maintained manually, and the dependencies may not be up-to-date over time.

* In extreme cases, some of the build targets are not built for months. Such targets are called zombie targets.

* Originally, any project could depend on any other project's internal details, thus creating (sometimes unwanted) couplings. 

* If the lower-level project did not intend to expose some internal details, the unwanted couplings introduce technical debt and make it harder to modify the lower-level project.

* One form of technical debt is the visibility debt or the cost of back-fitting visibility rules onto the existing build specifications to re-establish the appropriate encapsulations.

* Another example of technical debt is dead code that can confuse the developers looking for useful APIs.

## Dependency Debt

* *Over-declared* or *underutilized* dependencies can slow the build and testing of systems.

* *Under-declared* dependencies can make the build process brittle and make it difficult to remove *over-declared* dependencies.

* Potential solutions for *over-declared* dependencies include:
    
    * Setting aside some dedicated time for fixing build rules. But this approach is not automated, and potential breakages make it harder for developers to do the right thing.

    * Automatically add all the *under-declared* dependencies to the BUILD files. The system can raise an error if a direct dependency is missing, making it harder to do the wrong thing.

    * Automation can be applied for finding/reporting the over-declared dependencies as well.

*  Potential solutions for *underutilized* dependencies include:

    * While it is challenging to automate fixing *underutilized* dependencies, automating the discovery of such dependencies is still useful.

    * Highlighting dependencies with high cost and low removal effort could incentivize developers to clean up their projects.

## Zombie Targets

* Zombie targets can be identified by query the results of build and test runs. 

* A target is marked as "dead" if the attempts to build it have failed for at least 90 days. Until then, build errors are considered to be transient.

* A zombie target can be eliminated by deleting its definition from the BUILD and deleting the source files, which are reachable only via the zombie target.

## Visibility Debt

* Originally, the default visibility of all the targets was public, leading to unintended dependencies.

* The visibility of all the existing builds was set to *legacy_public*, and the default visibility was changed to private.

* This encouraged developers to explicitly consider if they wanted other projects to depend on their project.

## Dead Flags

* Google developed its command-line parsing utilities and defined a set of recognized command-line flags for libraries and binaries. 

* Overtime, the number of flags grew to half a million, and many of these flags are not useful anymore (i.e., dead).

* These dead flags can it hard to understand and refactor code. 

* Existing flags are analyzed to check which ones have always been set to the same value and replaced by those contents, clearing about 150 thousand flags.

* Removing dead flags also helps to clean up dead/unreachable code.
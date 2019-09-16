This is the summary of How to do a code review from Google Engineer Practice

Reference: https://google.github.io/eng-practices/review/reviewer/

# The Standard of Code Review
The primary purpose of code review is to make sure that the overall code health of Google’s code base is improving over time.

In order to accomplish this, a series of trade-offs have to be balanced.

First, developers must be able to make progress on their tasks. If you never submit an improvement to the codebase, then the codebase never improves. Also, if a reviewer makes it very difficult for any change to go in, then developers are disincentivized to make improvements in the future.

On the other hand, it is the duty of the reviewer to make sure that each CL is of such a quality that the overall code health of their codebase is not decreasing as time goes on.

Thus, we get the following rule as the standard we expect in code reviews:

**In general, reviewers should favor approving a CL once it is in a state where it definitely improves the overall code health of the system being worked on, even if the CL isn’t perfect.**

A key point here is that there is no such thing as “perfect” code—there is only better code. A CL that, as a whole, improves the maintainability, readability, and understandability of the system shouldn’t be delayed for days or weeks because it isn’t “perfect.”

## Mentoring
Code review can have an important function of teaching developers something new about a language, a framework, or general software design principles. It’s always fine to leave comments that help a developer learn something new.

## Principles
* Technical facts and data overrule opinions and personal preferences. 技术性的数据和标准比个人意见和偏好更重要。
* The style should be consistent with what is there. If there is no previous style, accept the author’s. See style guide for more detail.
* Aspects of software design are almost never a pure style issue or just a personal preference. They are based on underlying principles and should be weighed on those principles, not simply by personal opinion. **What are the principles?**
* If no other rule applies, then the reviewer may ask the author to be consistent with what is in the current codebase, as long as that doesn’t worsen the overall code health of the system.

## Resolving Conflicts
In any conflict on a code review, the first step should always be for the developer and reviewer to try to come to consensus, based on the contents of this document and the other documents in The CL Author’s Guide and this Reviewer Guide.

When coming to consensus becomes especially difficult, it can help to have a face-to-face meeting.

If that doesn’t resolve the situation, the most common way to resolve it would be to escalate.

Don’t let a CL sit around because the author and the reviewer can’t come to an agreement.

# What to look for in a code review

## Design
Does it integrate well with the rest of your system? Is now a good time to add this functionality?

## Functionality
Does this CL do what the developer intended? Mostly, we expect developers to test CLs well-enough that they work correctly by the time they get to code review. However, as the reviewer you should still be thinking about edge cases, looking for concurrency problems, trying to think like a user, and making sure that there are no bugs that you see just by reading the code. you can have the developer give you a demo of the functionality if it’s too inconvenient to patch in the CL and try it yourself.

Another time when it’s particularly important to think about functionality during a code review is if there is some sort of parallel programming going on in the CL that could theoretically cause deadlocks or race conditions. These sorts of issues are very hard to detect by just running the code and usually need somebody to think through them carefully to be sure that problems aren’t being introduced.

## Complexity
Is the CL more complex than it should be? “Too complex” usually means “can’t be understood quickly by code readers.” It can also mean “developers are likely to introduce bugs when they try to call or modify this code.”

A particular type of complexity is over-engineering, where developers have made the code more generic than it needs to be, or added functionality that isn’t presently needed by the system.

Encourage developers to solve the problem they know needs to be solved now, not the problem that the developer speculates might need to be solved in the future.

## Tests
Ask for unit, integration, or end-to-end tests as appropriate for the change. Make sure that the tests in the CL are correct, sensible, and useful.

## Naming

## Comments
Usually comments are useful when they explain why some code exists, and should not be explaining what some code is doing. mostly comments are for information that the code itself can’t possibly contain, like the reasoning behind a decision.

## Style
Make sure the CL follows the appropriate style guides.

## Every Line
Look at every line of code that you have been assigned to review.

If it’s too hard for you to read the code and this is slowing down the review, then you should let the developer know that and wait for them to clarify it before you try to review it.

## Good Things
If you see something nice in the CL, tell the developer, especially when they addressed one of your comments in a great way. Code reviews often just focus on mistakes, but they should offer encouragement and appreciation for good practices, as well. It’s sometimes even more valuable, in terms of mentoring, to tell a developer what they did right than to tell them what they did wrong.

## Summary
In doing a code review, you should make sure that:

* The code is well-designed.
* The functionality is good for the users of the code.
* Any UI changes are sensible and look good.
* Any parallel programming is done safely.
* The code isn’t more complex than it needs to be.
* The developer isn’t implementing things they might need in the future but don’t know they need now.
* Code has appropriate unit tests.
* Tests are well-designed.
* The developer used clear names for everything.
* Comments are clear and useful, and mostly explain why instead of what.
* Code is appropriately documented (generally in g3doc).
* The code conforms to our style guides.

Make sure to review every line of code you’ve been asked to review, look at the context, make sure you’re improving code health, and compliment developers on good things that they do.

---
layout: post
title: Rebase-based workflow
---

Today everyone works with repository and usually it is git. Now there are two main approaches on managing the repository:

* branch-based like [git flow](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow/)
* rebase-based

Git flow is mostly well known, but let's take a quick look at it anyway.

### Branch-based strategy

*__TL;DR__ Branch-based workflow is problematic, because of merge conflicts participants has to resolve, when they're merging feature branches. Project history is not that clear as it could be.*

Imagine the following scenerio:

You work in a team of 5 on an iOS app. Your leader configured the basic project and dependencies commonly used in the app. Each of you start feature, that you were assigned to, from the same commit. After a week, when the deadline for first iteration is getting closer, you are asked to merge your changes to a certain branch - let say `master`. The procedure looks as follows:

    $ git checkout master
    $ git pull origin master
    $ git merge new-feature     # resolve eventual conflicts
    $ git push origin master

Assume that each developer added some new files, libraries or assets to the project. Each one edited [storyboard file](https://developer.apple.com/library/prerelease/ios/referencelibrary/GettingStarted/RoadMapiOS/SecondTutorial.html) as well. Now, the first merge is a piece of cake - no additional work. Every succeeding merge creates a conflict on `CoolProject.xcodeproj/project.pbxproj` and `Main.storyboard` files. These conflicts has to be resolved before developer publish (`git push`) her/his feature. Do you see my point? It's time-consuming, frustrating and problematic. What if someone will make a mistake? The project will fall apart and it's not a difficult state to achieve.

Situation can get a bit better when there's a person, maintainer, that is responsible for merging feature branches to the `master` branch. It can be done, for instance, with pull request mechanism, but won't make the job any prettier.

The other drawback is project history. It becomes a bit messy, unclear and, you might say, incomplete, because of merge commits.

This is an example result of merging:

![]()

### Rebasing

In the rebase strategy you also can use so called *feature branches* (*topic branches*). There is no change on that field comparing to git flow. The only difference is in joining the topic branch with a main branch - `master`. The difference between rebasing and merging is that you're making sure that your commits come on top of the commits in the main branch. This means consistency, linear history and less conflicts what is explained below.

#### Rebase procedure

Assuming we checked out our topic branch `new-feature`, here is how the rebase should be done:

    $ git fetch origin
    $ git rebase master    # resolve eventual conflicts and then
                           # continue rebase with git rebase --continue
    $ git checkout master
    $ git rebase new-feature
    $ git push origin master

#### Explanation

Now lets assume that we made 3 commits in our `new-feature`. We will fetch the origin `git fetch origin` so the repository will look like this:

![]()

Pretend that I'm not the author of `Added Welcome View Controller` commit. It was added by co-worker. To join our `new-feature` we need rebase it to the `master` branch. There can be some conflicts that we have to resolve, and here is the difference. The conflicts are more likely optional and they're smaller, easier to resolve. Your co-workers also have smaller conflicts and you will fetched already updated files, so your conflicts refers to the current state of the repository, not the initial. I think this is a huge difference, and profits are bigger, the bigger development team is.

After `git rebase master` we're in the following situation:

![]()

As you can see we rebased `master` below the commits on the `new-feature` branch. Now we need to move all that commits to the `master` branch, so we need to switch to the `master` and rebase `new-feature` to it. There's the result:

![]()

We resolved a tiny conflict and moved all our commits from *topic* to *main* branch. Push your work and call it a success!

#### Extra

So you worked on some feature on a *topic* branch for some time. You have regurarly commited your changes, so you have a full history in case of any trouble. When you'll be ready, you may ask yourself a question: do we really need all that history in our repository? It may be hard to go through it for other devs. How about turning all these commits into a single one?

You can achieve that by doing interactive rebase in the *topic* branch like this

    $ git rebase -i master

Then you will see a list of your commits and you can choose what to do with them. In this case you want to squash them, but this is too broad topic for this article. More information about it can be found on [git ready blog post](http://gitready.com/advanced/2009/02/10/squashing-commits-with-rebase.html). In our case squashed commits would look like this:

![]()

Now we have a nice `master` branch with a new feature on it.

You can reach me on Twitter [@rad3ks](https://twitter.com/rad3ks).

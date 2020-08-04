## Document Purpose:

The pupose of this document is to outline Git workflow based projects work flow release designed to use with the git version control system. This acts as a guide to how to make changes source with Git and releases to the QA/production.

## Git Workflow

## Overview
The  Simple Git Workflow is a project development and release work flow which provides a development and release cycle based on below key phases:
    • Development: All active development is driven by project deliverables, and contains the code base to push for QA testing.
    • QA: Quality assurance testing figures directly as part of the development cycle, assessing both requirements compliance and acceptance criteria.
    • Release: Releases are generated after both a QA and review process.

## Organize the workflow

These phases are represented by "permanent" and "temporary" branches in the git repository, which facilitate the development process through to release. The main reason for the existence of persistent branches is to provision multifaceted deployment, allowing anyone to view the status of an application at different stages of it's development. These 2 types of branches contain the following git branches, which directly form part for the work flow.
## 4.1. Permanent branches:
•	develop: This branch contains the completed features for the current deliverables. This is an alpha code base, and considered unstable for release. Raise a Merge Request in GitLab to merge dev branch to qa branch, and it will be assigned to master user.
•	qa: All active development for a deliverable is eventually tested on this branch. Only master users can push the develop to qa branch.  
•	master: After approval has been given by the reviewer the qa code base is merged to master, which holds the current version of the project deliverables in the production environment. Only master users can push the qa to master branch.

## 4.2. Temporary branches:
•	feature: These branches are created from the develop branch for the isolated development of a particular task or deliverable. 
•	issue: These branches are created from the qa branch to fix an issue reported by QA while testing a completed deliverable.
•	hot-fix: These branches are created to attend to serious and urgent problems detected in the production environment. At best, these branches should never need to be created if the QA process is efficient enough to pass a stable code base to stage for review.

For all temp branches, Raise a Merge Request in GitLab to merge a branch, and it will be assigned to master user.

An important difference between "permanent" and "temporary" branches is that no changes can be made directly to a "permanent" branch, they may only be inherited via the merge of a "temporary" branch.

## 5. Develipment & Release cycle
In the following sections the phases of the development and release cycle are described individually, explaining the process for each in detail.

5.1. Development:
During the active development of a deliverables, developers will create feature branches, based on the code base from the develop branch.

These branches are named "feature/", and followed by the identifier of the task. This would typically be the ID or functional area of the deliverable from WBS, for example:

$ git checkout -b feature/proj1 develop
$ git push -u origin feature/proj1
By separating the task off onto a separate branch developers avoid destabilizing the develop branch to the point where they may be interfering with the work done by others. Additionally, feature branches can be updated (rebase) with other features already committed to the develop branch, in case some features are based on or integrate with others.

If you're working on the same feature with another developer you can checkout their branch and work on it in conjunction.

$ git checkout -t origin/feature/proj1

When the development of a feature is complete, it's merged back into the develop branch, and the feature branch is then deleted.

$ git checkout develop
$ git merge --no-ff feature/proj1
$ git branch -d feature/proj1
$ git push origin :feature/proj1

## 5.2. Testing:
When a deliverable is considered complete, the develop branch is merged into the qa branch, and the QA process begins. 

 

It's important to note that, when develop is merged into qa, any new features constitute the next deliverable. This allows for testing of the current deliverable as well as development of the next to run in parallel. Additionally, as the QA process is handled on a dedicated branch, this allows the testing phase to be scheduled without impeding the development to continue.

$ git checkout qa
$ git merge --no-ff develop

During the testing phase it's possible that QA may discover issues with the deliverable development, and therefore fail some features. When this occurs, the modifications required to rectify these bugs are created as issue branches, based on the code base from the qa branch. These branches are named "issue/", and followed by the identifier of the task. 

$ git checkout -b issue/proj1 qa
$ git push -u origin issue/proj1

While active, issue branches can also be updated (rebase) with other issues committed to the qa branch, in case one issue depends upon the completion of another. When an issue is resolved, it's merged back into the qa branch, and the issue branch is then deleted.

$ git checkout qa
$ git merge --no-ff issue/proj1
$ git branch -d issue/proj1
$ git push origin :issue/proj1

The qa branch would be deployed for the QA or developer/member to process in the current dev cluster (DEV).

## 5.3. Release
When the stage branch has been reviewed, after completing one or many deliverables, a release can be created. This is when the code base in the production environment is updated to reflect a new version of the application.

 
To create the release, the stage branch is merged into master, for example:

$ git checkout master
$ git merge --no-ff qa

Additionally, in order to mark the creation of the release, a tag is created from the master branch. These tags are named "release/", and followed by the version number, which vary between projects depending upon the versioning strategy used. The tag may also be signed cryptographically with the -s or -u options.

$ git tag -a release/1.1.0

All code merged from the qa branch into stage after the new release will now constitute the next version of the application.

## 5.4. Hot Fixes:
While the situation should rarely occur when effectively implementing this work flow, there are sometimes urgent bugs detected in the production environment which cannot wait until a pending release is created.

 

In these cases, a hot-fix branch is created, based on the code base from the master branch. These branches are named "hot-fix/", followed by the identifier of the task. 

$ git checkout -b hot-fix/proj1_HF01 master
$ git push -u origin hot-fix/proj1_HF01

Once the patch is successful, the hot-fix branch must also be propagated and merged back into the stage, qa and develop branches.

$ git checkout master
$ git merge --no-ff hot-fix/ proj1_HF01
$ git checkout stage
$ git merge --no-ff hot-fix/ proj1_HF01
$ git checkout qa
$ git merge --no-ff hot-fix/ proj1_HF01
$ git checkout develop
$ git merge --no-ff hot-fix/ proj1_HF01

Finally, once the hot-fix has been applied to all the relevant branches it can now be removed.

$ git branch -d hot-fix/ proj1_HF01

$ git push origin :hot-fix/ proj1_HF01


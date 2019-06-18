# Branching Strategy

Most projects at Bottle Rocket follow the [Gitflow](https://nvie.com/posts/a-successful-git-branching-model/) pattern for branching. More recently, some projects have experimented [trunk-based development](https://trunkbaseddevelopment.com/), but Gitflow is still the standard for all new projects.

It's also worth noting that our open source repos more closely follow [GitHub Flow](https://guides.github.com/introduction/flow/). This is essentially "Gitflow lite" where `master` is used as the primary development branch. We found that trying to follow true Gitflow with a separate `develop` branch added an additional layer of redundant pull requests when preparing a release. [This article](https://hackernoon.com/a-branching-and-releasing-strategy-that-fits-github-flow-be1b6c48eca2#98c4) aligns with our current approach for [our open source projects](https://github.com/BottleRocketStudios).

## Branching Models

### Gitflow

Gitflow is a branching model that organizes a repository into distinct branches, each with a unique purpose. It facilitates the ability to generate quality software via a stable release and development process.

![Gitflow branching strategy diagram](Images/git-flow.png)
*[Diagram created by Vincent Driessen](https://nvie.com/posts/a-successful-git-branching-model/)*

#### Branches

##### master

* Each commit in `master` should reflect a stable release and be tagged with that release's version number.
* The latest commit in `master` reflects the build currently available in the App Store.
* Until the first version of the app is released, `master` will usually reflect the initial state of the repository.

##### develop

* The main development branch, which reflects the most current non-shipped version of the app.
* Serves as the main integration point for new features/fixes as they are developed.
* The CI system points to `develop` by default, with QA performing the bulk of their testing on builds created from this branch.

##### feature/*

* Represents a feature or fix actively in development (e.g. `feature/loginScreen`).
* Initially branched from `develop` to start feature work.
* As the feature is developed, `develop` should be merged into `feature/*` periodically to keep the feature branch up-to-date with the latest changes.
* Once the feature is complete, the `feature/*` branch is merged into `develop` (via pull request) and then removed.

##### release/*

* Represents a release candidate (e.g. `release/2.1.0`).
* Initially branched from `develop` to start a code freeze process.
    * Only bugfixes should be merged into the `release/*` branch (i.e. no merging from `feature/*` branches).
* Merged into `develop` periodically so that fixes get incorporated into new feature development.
* Merged into `master` and tagged using [semantic versioning](https://semver.org/) once a release is complete (i.e. the app/update is live).
* Merged into `develop` one last time before being removed.

##### hotfix/*

* Represents a "hotfix" update used to quickly address issues with the live app.
* Initially created from the corresponding version number tag on the `master` branch (e.g. `2.1.0` --> `hotfix/2.1.1`).
* Allows for production issues to be resolved quickly without interrupting active feature development or having to manually cherry-pick or re-apply fixes from `develop`.
* Follows the same process as a `release/*` branch to keep `develop` up-to-date with any fixes and finalize the hotfix release.

#### Key Stages

1. The repo is created with only a `master` branch, by default.
2. A `develop` branch is created from `master`.
3. `feature/*` branches are created from `develop`.
4. When a feature is complete, it's merged into `develop` (via PR) and then removed.
5. To initiate a release, a `release/*` branch is created from `develop`.
6. When a release is complete, `release/*` is merged into `develop` and `master`, tagged, and then removed.
7. If an issue in `master` needs to be resolved, a `hotfix/*` branch is created from `master`.
8. When the hotfix release is complete, `hotfix/*` is merged into `develop` and `master`, tagged, and then removed.

### Trunk-Based Development

Trunk-based development is a branching model that revolves around a single main "trunk" branch. The main idea is for all developers to integrate their in-progress feature work as quickly as possible to avoid complex merges and ensure success of the continuous integration and continuous delivery mechanisms. Because feature work merged into `trunk` may not be 100% complete, a "feature toggle" configuration system is often employed to ensure that only completed features are accessible in releases. 

## Commits

**Commit early, commit often!** Try to remember to push any pending commits to the remote repo at the end of every day so that all in-progress work is backed up.

Commit messages should be detailed and helpful - avoid anything that's not a complete sentence. You should aim to tell a story in the commit message (i.e. what was broken and how it was fixed). A good rule of thumb to follow is to begin commit messages with a verb so that the message completes the phrase "This commit...".

## Merging

All merges into `develop`/`trunk`, `release/*`, and `hotfix/*` should happen via pull requests. This ensures that all code gets reviewed at some point before it's shipped. For more information, see [Code Review Standards](../Code%20Review%20Standards/Code%20Review%20Standards.md).

## Deleting Branches

Branches should be deleted after they've been merged into `develop` or `master`. This keeps the repository clean and makes it clear where active development is occurring.

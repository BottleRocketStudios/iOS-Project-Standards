# Code Review Standards

## Introduction

Code reviews are critical to ensuring quality and efficiency in our software development process. We aim for 100% code contribution review coverage by utilizing pre-merge pull requests (PRs) to review code as it is integrated into the main development branches of the repository.

## Goals

There are two main goals with respect to code reviews:

* Facilitate a shared understanding of the codebase and foster a collective ownership of the project.
* Improve the quality of the code as much as possible (i.e. 2/3/4 brains are better than 1).

As you probably know, software issues cost more to fix later in the software development lifecycle. Code reviews serve as the first line of defense, helping to find and correct problems as early as possible.

## Guidelines and Considerations

* Reviewing code is of equal priority to writing code.
    * Be thorough - read, understand, and think about the code you're reviewing. Are there logic errors? Did they follow the coding style guidelines? Could this be simplified?
    * **DO NOT** skim through and blindly "approve" PRs.
* Code reviews should be small and cohesive. Having many small, focused reviews instead of one large review helps to ensure quality reviews. Keep this in mind when developing large features. It's perfectly acceptable to create PRs on your feature branch as you go rather than waiting until the end and doing a large PR into the main development branch.
* Provide context/instructions as necessary. If there is a GIF or screenshot that will help the reviewer understand what they're looking at, include it.
* When suggesting improvements, be generous with code examples to ensure that your feedback is as clear as possible. Checking out the author's branch and experimenting is encouraged!
* Aim to respond to all code review requests within 24 hours.
* All members of the team should all be required to review each PR before it's merged. This greatly improves project visibility, provides training opportunities, and fosters a shared ownership of the codebase.
    * If not possible due to team size, at the very least aim for 75% of the team to have completed a review before allowing merging. A PR should always require review from all of the lead developers as well.
* Be respectful.
    * Criticism is valuable, but only if it's constructive. Suggest improvements instead of only pointing out flaws.
    * Along the same line of thinking, give the reviewer the benefit of the doubt. If something sounds harsh, they most likely didn't mean it that way. Embrace offline brainstorming sessions or pair programming when necessary.
* Be vocal.
    * If you see a better way of doing something, then speak up! It should be **very rare** that you can't think of at least one small improvement that could be made to the code.
    * Ask for clarification when needed - either as the reviewer trying to understand the code or the author trying to understand a reviewer's feedback.

### Quality of Code

Overall, reviews should focus on the quality of the code itself (i.e. logic errors, force unwrapping, etc.) over trivial issues (i.e. tabs vs spaces, etc.). The development process should enable this though things like linters, static analysis, continuous integration, etc. so that the code is as clean as possible before humans start looking at it.

### Commented Out Code

PRs should not contain commented out code. It adds clutter and often doesn't get cleaned up once it's merged. As long as the commented out code existed in at least one commit, we can always use source control to restore it if necessary.

## Pull Request Process

Most of our projects utilize the PR functionality of [Bitbucket Cloud](https://bitbucket.org/) so this PR process is tailored to that experience. However, other PR mechanisms like [GitHub](https://github.com/) or [Bitbucket Server](https://www.atlassian.com/software/bitbucket/server) will generally follow the same process.

### Pre-PR Checklist

Before creating the pull request, make sure to do the following:

1. Review your own code. Ideally, you're doing this with each commit that you make in addition to a final look when previewing the PR.
2. Integrate the latest changes by merging `develop` into your `feature/*` branch.
3. Make sure to address any outstanding `TODO`s or commented out code that you intended to clean up.
4. Make sure there are no new compilation warnings or errors. Don't break the build!
5. Make sure all of the unit tests pass.

### Creating the PR

1. Author creates a PR from their `feature/*` branch into `develop`. Most of the time the default title and review description based on commit messages should be sufficient, but any additional context is welcome.
2. The repository should be configured to automatically add default reviewers to each PR, but if there is anyone missing, the author should make sure to add them manually.
3. If not previewed during the PR creation process, the author should immediately perform a self-review after the PR is created to spot and fix any final details before others start reviewing.

### PR Feedback Process

1. Reviewers review the code, adding comments and suggestions for improvements.
2. The author implements the suggested feedback or starts a discussion in order to get clarification or provide an alternate opinion.
3. Once satisfied with the implemented improvements (if any), the reviewer "approves" the PR.

### Merging the PR

1. Once all required reviewers have approved the PR, the author merges the PR.
2. Once merged, the `feature/*` branch can be removed.
3. After merging, the author should build and run the app to verify functionality one last time. This is especially important if there were merge conflicts that had to be resolved before the merge.
4. After merging, the author is also responsible for updating the relevant ticket(s) in the sprint board as well.

# Development Environment

## Xcode

### Using the Latest Version

We make a conscious effort to keep our projects on the latest version of [Xcode](https://developer.apple.com/xcode/). When new Xcode versions are released every fall, some of our largest projects might take a few months to upgrade, but most of our projects are using the latest version within a matter of days/weeks. During the summer, some projects also become early adopters of the latest beta versions of Xcode

### Embracing the Defaults

We prefer to keep our development environment as close to "stock" as possible. This means using the Xcode defaults for most everything, which:

* Negates the need to manage scripts that setup/configure the development environment.
* Reduces maintenance resulting from custom settings being blown away by Xcode updates/reinstalls.
* Ensures developers can contribute with as little friction as possible.

## Gitignore

We make use of [GitHub's Swift gitignore](https://github.com/github/gitignore/blob/master/Swift.gitignore) as the default gitignore for our projects. The only change that we typically make is uncommenting the `Pods/` line so that the [`Pods` folder isn't included in source control](../Dependency%20Management#checking-in-the-pods-folder).

## Cocoapods

Many developers naively use the built-in system Ruby to install Cocoapods using the `sudo gem install cocoapods` command. Unfortunately, this can lead to headaches due to the use of `sudo`.

The better way to install Cocoapods is to get off the system Ruby and use a Ruby version manager to negate the need for `sudo` access. [This excellent article](https://medium.com/@jules2689/homebrew-ruby-and-gems-78d6c26b89e) explains how to do this using [`rbenv`](https://github.com/rbenv/rbenv).

## Fastlane

[Fastlane](https://fastlane.tools/) is our main build tool. Again, a Ruby version manager should be used so that a `sudo`-less installation of Fastlane can be achieved.

## Build Servers

As a prerequisite for running our Fastlane scripts, we have custom build scripts that manage keychains, signing certificates, and provisioning profiles for our projects. We store certificates and provisioning profiles in a separate repository from the source code for the project in order to provide an additional layer of security.

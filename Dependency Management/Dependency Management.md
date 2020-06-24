# Dependency Management

External dependencies should be added to your project only after careful consideration. Always attempt to build the solution yourself by building on top of Apple's excellent frameworks, rather than immediately resorting to third party libraries.

Software built with many dependencies starts to feel a bit like "software engineering with duct tape" and will become a maintenance nightmare down the road. Simply put, fewer dependencies leads to a better understanding of the code and gives you more control over its evolution over time.

## Bundler

[Bundler](https://https://bundler.io/) is the preferred mechanism for managing the gems needed to build our iOS apps.

### Understanding `bundle` Commands

Knowing when to use `bundle install` vs `bundle update`, or even what the differences between the commands are can be tricky. In summary:

* `bundle install`
    * Fetches and installs the dependencies listed in the `Gemfile` according to the versions listed in the `Gemfile.lock`. This is the first command you'll use to install dependencies when you initially check out a repository as well as when you add or remove dependencies from the `Gemfile`.
    * If no `Gemfile.lock` is present, then the latest available versions of each dependency will be used (and a new `Gemfile.lock` generated).
* `bundle update`
    * Updates all dependencies, respecting any version locks present in the `Gemfile`. For example, if you lock a dependency down to version `2.0.0`, but version `3.1.0` is available, then Bundler will continue to use version `2.0.0`. This is the reason we recommend letting the `Gemfile.lock` handle versioning rather than manually defining version locks in the `Gemfile`.
* `bundle update <gem_name>`
    * Updates a specific dependency, again respecting any version locks present in the `Gemfile`.

### Versioning Ruby Gems

The `Gemfile.lock` should always be checked in to source control, as it contains the versioning information for the dependencies specified in the `Gemfile`. In general, it's not necessary to define explicit versions in your `Gemfile` because the `Gemfile.lock` will take care of this for you. In many cases, locking down your `Gemfile` with explicit version numbers will make it more difficult for you to quickly update the dependencies using the `bundle update` command.

In summary, we prefer `Gemfile` entries like this:

```ruby
gem 'cocoapods'
```

We discourage `Gemfile` entries like this:

```ruby
gem 'cocoapods', '1.9.3'
```

Your `Gemfile` makes use of the pessimistic version operator (`~>`) which can be a good middle ground, since it will still allow `bundle update` to work until the next major/minor version is released. In the example below, `bundle update` would continue to update `cocoapods` up until version `2.0` and `fastlane` up until version `2.150.0`:

```ruby
gem 'cocoapods', '~> 1.0'
gem 'fastlane', "~> 2.149.1"
```

Though `bundle update` can make updating dependencies a breeze, you should always take an extra few minutes to read the release notes for the dependencies being updated. Even if there are no obvious changes, it is generally a good idea to know how the dependencies you rely on change over time. 

## CocoaPods

[CocoaPods](https://cocoapods.org/) is the preferred mechanism for managing dependencies in our iOS apps.

### Checking in the `Pods` folder

In order to keep repository size as minimal as possible, most of our apps do not add the `Pods` folder to source control (i.e. `Pods/` is an entry in the project's `.gitignore`). An exception is made for apps that:

* Primarily deal with sensitive data or financial transactions. For these apps, we will check in the `Pods` folder so that we can more easily audit the changes made to third party libraries as they are updated over time.
* Require a non-BR build environment. Some client build environments don't allow network connections during the build process so all pod sources need to be present in the repository at build-time.

### Understanding `pod` Commands

Assuming you are using Bundler and have not installed Cocoapods globally all of these commands are used. If you **have** install Cocoapods globally simply use `pod <command>` instead of `bundle exec pod <command>`.
	
Knowing when to use `bundle exec pod install` vs `bundle exec pod update`, or even what the differences between the commands are can be tricky. In summary:

* `bundle exec pod install`
    * Fetches and installs the dependencies listed in the `Podfile` according to the versions listed in the `Podfile.lock`. This is the main command you'll use to install dependencies when you initially check out a repository as well as when you add or remove dependencies from the `Podfile`.
    * If no `Podfile.lock` is present, then the latest available versions of each dependency will be used (and a new `Podfile.lock` generated).
* `bundle exec pod update`
    * Updates all dependencies, respecting any version locks present in the `Podfile`. For example, if you lock a dependency down to version `2.0.0`, but version `3.1.0` is available, then Cocoapods will continue to use version `2.0.0`. This is the reason we recommend letting the `Podfile.lock` handle versioning rather than manually defining version locks in the `Podfile`.
* `bundle exec pod update <pod_name>`
    * Updates a specific dependency, again respecting any version locks present in the `Podfile`.
* `bundle exec pod repo update`
    * Updates your local copy of the [Cocoapods master repo](https://github.com/CocoaPods/Specs). You need to run this command periodically to make sure your local Cocoapods installation is aware of the latest versions of pods available during `bundle exec pod install` and `bundle exec pod update` commands.
* `bundle exec pod deintegrate`
    * Completely removes all traces of Cocoapods from your project. This really only needs to be run when there is a problem since it will rework your project's structure, which can lead to nasty merge conflicts.

### Versioning Pods

Similar to the `Gemfile.lock`, the `Podfile.lock` should always be checked in to source control, as it contains the versioning information for the dependencies specified in the `Podfile`. Here too, you should avoid defining explicit versions in your `Podfile` because the `Podfile.lock` will take care of this for you. In many cases, locking down your `Podfile` with explicit version numbers will make it more difficult for you to quickly update the dependencies using the `bundle exec pod update` command.

In summary, we prefer `Podfile` entries like this:

```ruby
pod 'Hyperspace'
```

We discourage `Podfile` entries like this:

```ruby
pod 'Hyperspace', '2.0.0'
```

If you really do feel you have a need to lock down your dependencies in the `Podfile`, the `~>` operator can be a good middle ground, since it will still allow `bundle exec pod update` to work until the next major/minor version is released. In the example below, `bundle exec pod update` would continue to update the dependency up until version `3.0`:

```ruby
pod 'Hyperspace', '~> 2.0'
```

When using `bundle exec pod update` you should always take an extra few minutes to read the release notes for the dependencies being updated the same way you are for your gems. Even if there are no obvious syntax changes, it is generally a good idea to know how the dependencies you rely on change over time. 

## Carthage

[Carthage](https://github.com/Carthage/Carthage) is another popular dependency management solution, but we rarely make use of it at Bottle Rocket. It's typically only used when required by a third party integration.

## Manual Integration

Manually integrating third party libraries and SDKs into the Xcode project is only done as a last resort. Some potential challenges you might face when manually integrating libraries include:

* Updating Xcode project settings, which can become a source of technical debt when the library is removed or its integration method altered.
* Figuring out library version numbers. There's no standard for how pre-baked SDKs and frameworks document their version number - sometimes it's in the file name, sometimes it's a constant inside a header file, sometimes it's a method called at runtime, and sometimes it's not included at all.
* Temptations to modify the library code directly (assuming raw source code is available). We try to avoid ever modifying third party code as it can lead to maintenance headaches as newer versions of the library are released.  

## Use of Open Source Software (OSS)

**Never use any GPL licensed component**! The GNU General Public License (GPL) is incompatible with the iOS App Store's terms and service agreement. Use of GPL components can, and most likely will, result in your app's rejection from the App Store.

In general any OSS that uses the MIT or BSD license is fine for our use. But before actually using it in a project, we require approval from the tech lead or the director of software development.

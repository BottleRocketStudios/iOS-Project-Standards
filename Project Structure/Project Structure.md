# Project Structure

## Xcode Folder Structure

There are typically two schools of thought when it comes to folder structure - "is a"-based and feature-based.

### "Is-a"-based

"Is-a"-based refers to grouping classes according to their type (e.g. Models, Views, View Controllers, etc.).

### Feature-based

Feature-based refers to grouping classes according to the feature or screen they belong to.

### Hybrid structure

We prefer a hybrid, but predominantly feature-based folder structure.

#### Keep top-level folders feature-based

The top-level folders typically follow a feature-based structure for the following reasons:

* Folder structure grows organically over time instead of needing to pre-define several folder types at project creation.
* Easier to onboard new devs. Related feature code is grouped together.
* Works better with Xcode's project navigator. You don't have to open/expand several sets of nested folders to see all of the building blocks that make up a screen or feature.

#### Use "is-a"-based structure for subfolders within a feature

Within a given feature or screen, "is-a"-based structure can be used to provide some additional organization (e.g. cells, controllers, models that are within a certain screen). You certainly don't want to have dozens of files sitting loosely in a single folder. Use subfolders to keep things organized at the feature-level.

#### Use a `Shared` folder for features used on multiple screens

For classes that are shared across multiple screens, a top-level `Shared` folder is used with "is-a"-based organization for its subfolders.

#### Utilize Xcode's default structure

In general, leave the files that Xcode generates with new project creation where they are. This can be helpful for the following reasons:

* Keeps the `AppDelegate`, `SceneDelegate`, and `App` (SwiftUI) at the top-level
    * As the entry point for your application, it's nice to be able to quickly dive in without having to expand a lot of folders.
* Keeps the `Assets.xcassets` (Asset Catalog) at the top-level
    * This makes it easy to get to your image resources from anywhere.
* Keeps the `Info.plist` at the top-level
    * Again, this makes it easy to get to from wherever you are in the project. Moving this file also requires updating the project's build settings.

### Keeping the file system in-sync

It's important to keep things organized at the file-level as well. Ideally your Xcode project structure should match the folder structure on the file system. This makes it easy to find things when browsing your repository in a web browser or file browser. Luckily, Xcode 9+ now does this by default.

#### Synx

Although old, Venmo's [Synx](https://github.com/venmo/synx) can help to automatically reorganize your folder structure to match your Xcode project structure. Another neat feature is that you can use the `--prune` flag to be alerted to orphaned files on your file system that are not referenced in the Xcode project. This makes Synx quite useful when inheriting an unorganized codebase or as a general maintenance/cleanup tool for older projects. Beware that extra care must be taken for [projects with localization](https://github.com/venmo/synx/issues/68).

## Target Structure

When creating a new Xcode project, you typically get the following targets automatically:

* `<main app>`
* `<main app>`Tests
* `<main app>`UITests

In order to facilitate the adoption of app extensions, we will usually create a "Kit" framework target that contains shared business logic, models, screens, views, etc. that are, or could conceivably be, used in an extension target.

* `<main app>`Kit
* `<main app>`KitTests

Of course, any extensions developed will also have their corresponding targets and test targets:

* `<extension>`
* `<extension>`Tests

Additional framework targets can be created for things like:

* Custom networking layer
* Complex data/model layer
* etc.

## Typical Project Structure Example

Starting with Xcode project file as the root, your folder structure should look something like the following:

```
<project name>
    <main app target>
        Resources
            Fonts
            ...
        Screens
            <screen name>
            <screen name>
            ...
        Shared
            Alerts
            Analytics
            Constants
            Controllers
            Environment
            Extensions
            Logging
            Managers
            Models
            Storyboards
            Views
            ...
        AppDelegate.swift
        Assets.xcassets
        Info.plist
    <main app unit test target>
    <main app UI test target>
    <shared target>
    <shared target unit tests>
```

![Xcode project structure](https://github.com/tylermilner/ProjectStructureExample/raw/master/Images/project-structure-example.png)

You can also reference our [example Xcode project](https://github.com/tylermilner/ProjectStructureExample).

## Colors, Fonts, and Images

### Colors

#### `UIColor` (UIKit) or `Color` (SwiftUI) Extension

In order to keep all color information contained in a single spot, all of your app's colors should live as `static let` properties inside an extension on `UIColor` or `Color`. You shouldn't be manually instantiating `UIColor` or `Color` objects elsewhere in your codebase.

It can also be helpful to add a `///` documentation comment above each property describing the red, green, blue, and alpha values for the color. This allows you to option-click a color's name throughout your codebase to quickly get a sense for a color's RGBA values. Keep in mind that these comments will need to be updated over time, as you update the colors themselves.  

#### Color Assets

Xcode 9 introduced named colors in asset catalogs. This is our preferred approach to setting up colors in a project since it allows you to define a palette of colors once and use them both programmatically and in Interface Builder.

Again, we make these colors available in code via the `UIColor` or `Color` extension. Keep in mind that the `UIColor(named:)` initializer is only available in iOS 11+ so you may need to make use of a `#available(iOS 11.0, *)` attribute to use the new API while falling back to another initializer for older OS versions. The `Color(_:bundle:)` initializer is available in iOS 13+.

#### Color Literals

If supporting versions of iOS prior to iOS 11, we prefer color literals to the older `UIColor(red:green:blue:alpha:)` initializer.

### Fonts

#### `UIFont` (UIKit) or `Font` (SwiftUI) Extension

In order to keep all font information contained in a single spot, all of your app's fonts should live as `static let` properties inside an extension on `UIFont` or `Font`. You shouldn't be manually instantiating `UIFont` or `Font` objects elsewhere in your codebase.

### Images

#### Image Resources

* All images should live inside of the main `Assets.xcassets` asset catalog. Each Target may have its own asset catalog.
* Images should be organized and grouped into subfolders according to some criteria (category/screen/feature/etc.).
* Images should have a consistent naming scheme (e.g. icons prefixed with `ic_`, etc.). Work with your designer to implement a scheme if they aren't already doing this by default.

#### Image Literals

While they don't read that great in code, we prefer using image literals because it helps to remove some of the "stringly-typedness" of the `UIImage(named:)` initializer. Another option is to create `static let` properties inside an extension of `UImage` (UIKit) or `Image` (SwiftUI).

#### SF Symbols

We prefer to use SF Symbols over bringing in a similar image asset. [SF Symbols](https://developer.apple.com/design/human-interface-guidelines/sf-symbols/overview/) provides a set of over 3,100 consistent, highly configurable symbols provided by Apple. They automatically align with text in all weights and sizes. They are great for representing a variety of UI elements such as navigation bars, toolbars, tab bars, context menus, and widgets. It is even possible to create custom symbols. SF Symbols are available in iOS 13+, macOS 11+, watchOS 6+, and tvOS 13+.

# Tools and Utilities

This is a collection of useful tools and utilities we typically use when developing apps at Bottle Rocket.

## Linters

We use linters to help enforce code consistency and quality between projects.

### SwiftLint

[SwiftLint](https://github.com/realm/SwiftLint) helps identify and enforce community-driven style and conventions in Swift code. Check out the [Linting section of our Code Style document](../Code%20Style/Code%20Style.md#linting) for more information on how we utilize SwiftLint in our projects.

### OCLint

[OCLint](http://oclint.org/) is a static analysis tool that can help to identify code smells in Objective-C code. It can also identify dead code like unused methods and parameters.

### IBLinter

[IBLinter](https://github.com/IBDecodable/IBLinter) helps identify issues in Interface Builder documents. It's a rather new tool so it doesn't seem ready for prime time just yet, but it's something we're watching as it evolves.

## Code Generation

### Sourcery

[Sourcery](https://github.com/krzysztofzablocki/Sourcery) is a code generation tool for Swift, allowing you to generate boilerplate code automatically.

## Code Cleanup

### Fui

[Fui](https://github.com/dblock/fui) allows you to find unused Objective-C imports (which can help to identify unused Obj-C classes).

### Fus

[Fus](https://github.com/tsabend/fus) finds unused Swift classes in your project.

### Periphery

[Periphery](https://peripheryapp.com/) finds unused Swift code in your project.

## Project Health

### Synx

[Synx](https://github.com/venmo/synx) reorganizes your project's file structure to match how your Xcode groups are organized. It can also identify unused image resources and source files that are not part of your Xcode project.

## Provisioning and Signing

### ProvisionQL

[ProvisionQL](https://github.com/ealeksandrov/ProvisionQL) is a Quick Look plugin for showing provisioning and signing information for provisioning profiles and IPAs.

## Build Automation

### Fastlane

[Fastlane](https://fastlane.tools/) is a suite of tools to make iOS development easier. It can handle everything from building your app to distributing it to the App Store.

## API Communication

### Postman

[Postman](https://www.getpostman.com/) is an app that allows you to save and execute HTTP requests. We use Postman to validate and test API functionality before we implement it in the app.

### Charles

[Charles](https://www.charlesproxy.com/) is an HTTP proxy that allows you to intercept and read all of the HTTP traffic that passes through a device. It's incredibly useful for debugging communication issues between the app and external APIs.

## Git

### SourceTree

[SourceTree](https://www.sourcetreeapp.com/) is a Git GUI app that provides great visualization and management tools.

### GitKraken

[GitKraken](https://www.gitkraken.com/) is another Git GUI that some of our devs prefer over SourceTree.

## Merging Code

### Kaleidoscope

[Kaleidoscope](https://www.kaleidoscopeapp.com/) is an advanced file comparison app. It performs many of the same functions as Apple's FileMerge, but can handle diffing many more file types.

## UI Specifications

### Zeplin

[Zeplin](https://zeplin.io/) is an app that allows for designers to easily communicate UI specifications to developers. Before using Zeplin, our designers would need to manually "blueprint" specs for app screens like spacing between elements, font attributes, colors, etc. Zeplin integrates with Sketch to automate much of that process.

### Lottie

[Lottie](https://airbnb.design/lottie/) is a library that allows mobile apps to easily render After Effects animations. Under the hood, Lottie uses JSON to encode the vector animations, which keeps the animation file sizes small and even allows animations to be downloaded at run-time.

### Lottie Player

[Lottie Player](https://github.com/willowtreeapps/lottie-player) is a standalone viewer app for Lottie animations. It allows designers to test their Lottie animations before exporting them to developers.

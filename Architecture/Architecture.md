# Architecture

## Model View Controller (MVC)

### Keeping It Simple

We feel that Apple got it right with this one. All of our projects follow [Apple's MVC architecture](https://developer.apple.com/library/archive/documentation/General/Conceptual/DevPedia-CocoaCore/MVC.html). We've found this beneficial for many reasons:

* Keeps the barrier to entry for understanding the code low, minimizing the amount of ramp-up time required to onboard new devs.
* Is well-documented.
* Avoids the need to rely on third party frameworks for app architecture. Less dependencies is always better (see [Dependency Management](../Dependency%20Management/Dependency%20Management.md) for more information).

### Avoiding Massive View Controller

Massive view controllers only happen when proper separation of concerns isn't implemented. By simply following [SOLID design principles](https://en.wikipedia.org/wiki/SOLID), you can make even a 300 line view controller a rare occurrence.

## Table and Collection Views

### Built-In Table/Collection View Controllers 

We **rarely** use the Apple-provided view controller templates (`UITableViewController` & `UICollectionViewController`). While they can be useful for beginners just starting out in iOS development, they reduce your ability to properly apply separation of concerns and can be challenging to build upon whenever you need to do non-default behavior.

### Data Sources and Delegates

The data source and delegate methods for table and collection views should not be implemented by the view controller. Instead, separate "table/collection controller" objects should be created for the task. This prevents table/collection view details from polluting your view controller, which leads to better separation of concerns and greatly simplifies its responsibilities.

## Model Objects

### JSON Deserialization

Data received from external APIs is almost always JSON. We prefer to to convert the raw JSON into native model objects **as soon as possible** rather than work with raw JSON dictionaries. With the release of Swift 4, we now make heavy use of the [`Decodable` protocol](https://developer.apple.com/documentation/swift/decodable) rather than relying on third party libraries like SwiftyJSON or Argo.

### Classes vs Structs

We generally prefer pure Swift structs due to their value-typed nature and ability of the Swift compiler to automatically generate the [memberwise initializer](https://docs.swift.org/swift-book/LanguageGuide/Initialization.html#ID214).

## Protocols

### Prefer Protocols Over Inheritance

The [Protocol-Oriented Programming in Swift](https://developer.apple.com/videos/play/wwdc2015/408/) session from WWDC 2015 is a great introduction to the power of protocols. It's worth a watch if you've never seen it or a re-watch for a quick refresher.

You should prefer using composition over inheritance when writing Swift code. Specifically, you should prefer __protocol based composition__ over inheritance.

Protocol based composition allows a struct or class to fulfill the desired behaviors of an object without requiring that direct inheritance - which is not available for a struct in any case.

Inheritance should be used as a last resort. There are two generally palatable situations where you may use inheritance:

* Base view controllers
* Inheritance required by mandated third party libraries

## UI Responsiveness

It's imperative that the app's UI remain responsive during all aspects of the app's lifecycle. In general, any delay greater than 200 ms will be immediately noticeable by the user and could cause them to question whether or not the system registered their tap.

* Reserve the main thread for UI work. Move as much work to background threads as possible.
* Avoid purposely ignoring user interactions. There should generally never be a need to globally ignore user interaction by calling [`beginIgnoringInteractionEvents()`](https://developer.apple.com/documentation/uikit/uiapplication/1623047-beginignoringinteractionevents) on `UIApplication`.

## Architectural Guidelines

When working on a new project it is imperative that all developers understand the scope and significance of any technical decision they make. In general, the more junior the developer, the more guidance and the less architectural decision making power the developer has. This is primarily to protect the customer as well as Bottle Rocket from “rogue” developers making a poor technology or other architecturally significant decision that would impact our ability to meet customer expectations.

In general, if a developer has to make a decision between development technologies (e.g. languages, tools, 3rd party components, complex implementations, etc.) then this is an architecturally significant decision that requires director level approval and notification. The sole intent of this guideline is to protect the client and Bottle Rocket, not to create an artificial bottleneck.

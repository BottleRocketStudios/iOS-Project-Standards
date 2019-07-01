# Code Style

## Linting

We make heavy use of [SwiftLint](https://github.com/realm/SwiftLint) to inform our code style decisions. Each project starts out with a copy of our [standardized .swiftlint.yml configuration file](https://gist.github.com/tylermilner/f33e33e3b4f23d8c6b2fdd4f87af98a1), which keeps things pretty close to default, but cranks the warnings up to the max by enabling almost all of the opt-in rules. This helps to ensure a consistent baseline structure of projects across teams.

Anecdotally, we have found that when we use SwiftLint in a pedantic manner, we end up with a code base with zero or near-zero crashes, and a significantly lower number of abnormal behavior bugs. Most of these can be attributed to not allowing for force-unwrapped variables, but there are other elements that SwiftLint has helped us discover early in development.

Rather than require all developers to install SwiftLint locally onto their machine, we [use Cocoapods to install SwiftLint](https://github.com/realm/swiftlint#using-cocoapods) into projects. This allows SwiftLint to be managed in the `Podfile`, just like any other dependency, and ensures all developers are using the same version.

## Readability

* Code is written for humans, not computers. Less clever, more verbose code is preferred to more clever, concise code.
* Aim for one class or idea per file. Long files that span several concepts are more difficult to parse compared to concise files that each focus on a particular idea.

## Consistency

> When in Rome, code as the Romans do.

Much of the code that we maintain wasn't initially written by us (or was written before these guidelines were in place). As such, do your best to adopt the style present in your particular project. The idea is that a consistent style (even if not ideal) is better than a mishmash of different styles. Ideally, some time should be devoted to modernizing the project's style, but that's not always possible depending on deadlines and where you are in the project lifecycle.

## Comments

* Because most iOS code is fairly verbose and "self documenting", comments should focus on details around intention, rather than implementation (i.e. explaining *why* vs explaining *what*/*how*).
* Make use of the Xcode `⌥` + `⌘` + `/` (option + command + slash) shortcut to automatically generate system-style documentation comments.
    * Be liberal with these for `public` methods and properties in framework targets.
* Document complex business logic and algorithms, ideally pointing to "living" documentation that might exist in some other system as well (e.g. Confluence, wiki, etc.).
* Document any "magic numbers". An arbitrarily hard-coded number should have a comment explaining what the number does or how it came to be.
* Hacks and workarounds should be documented. If there is any supporting information online like a post on Stack Overflow or a blog, include a summary of the important content along with the URL.
* Make use of the `///` comment above class/struct/enum/etc. definitions to provide a brief description of the purpose of the object. The `///` comment allows Xcode to show the content in the info popup that appears when `⌥`-clicking (option + click) a type.
* Deliberate deviations from standards should include a comment explaining why it was necessary.

Comments are very important when working on teams, since your solutions may not be obvious to everyone. A general rule of thumb is that if you think you may have difficulty remembering how something works or why you decided upon a specific solution a year from now, **comment it**!

## Tabs vs Spaces

We [embrace the Xcode defaults](../Development%20Environment/Development%20Environment.md#embracing-the-defaults) for just about everything, which means we use **spaces** since the Xcode default is 4 spaces for every "tab".

## Naming

Following industry standards, we use `camelCase` as the main naming convention for everything:

* Classes and protocols should always start with an uppercase letter.
* Methods, variables, and functions should start with a lowercase letter. This includes [acronyms](./Swift%20Style%20Guide.md#acronyms-in-variable-names).

### Naming Classes

There is an age-old saying in the software industry:

> There are only two hard things in Computer Science: cache invalidation and naming things.

Indeed, the problem of naming things hasn't gotten any easier. With generic names like manager, provider, coordinator, service, controller, etc., how do you know when to use the right one? We prefer the following convention:

* **Service** - an object that performs a task without persisting any state (i.e. a `NetworkService` executes a request and calls a completion block).
* **Controller** - an object that performs a task while persisting state (i.e. user object, authentication object, etc.).
* **Coordinator** - an object that manages the flow between view controllers/screens (i.e. `JobsCoordinator` manages the navigation between a jobs list, details, etc.).
* **Provider** - an object which creates and vends other objects when passed the required dependencies.
* **Manager** - anything that doesn’t really fit the other four options. Although there is probably still a more descriptive name you can use (see Ben Sandofsky's [The Trouble with Manager Objects](https://sandofsky.com/blog/manager-classes.html)).

## Swift Style Guide

For any styling concerns not covered by our SwiftLint configuration, refer to our [Swift Style Guide](./Swift%20Style%20Guide.md).

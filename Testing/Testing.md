# Testing

Testing is a vital aspect of any software development process. At Bottle Rocket, we utilize many testing techniques to ensure we ship extremely high quality software.

## Unit Testing

In its simplest form, a unit test verifies whether a piece of code (a "unit") works as expected.

### Types of Unit Tests

A unit test will usually fit into one of 3 categories, with varying levels of difficulty to write.

#### Return Value Verification

This is the simplest type of test to write and lends itself to functional programming techniques.

* Setup the system under test (SUT).
* Send the SUT a message.
* Verify the returned value is what you expect.

##### Example

Suppose we have a simple `Rectangle` class with a method for checking if a given width and height create a square:

```swift
struct Rectangle {

    static func isSquare(width: Int, height: Int) -> Bool {
        return width == height
    }
}
```

The `static` keyword gives us a clue that this is a good candidate for Return Value Verification. We can reasonably assume that this function is stateless, and any output directly depends on its input rather than some internal state (though that's not always the case). One of our tests might look like this:

```swift
func test_5x5rect_isSquare() {
    // Arrange
    let width = 5
    let height = 5

    // Act
    let isSquare = Rectangle.isSquare(width: width, height: height)

    // Assert
    XCTAssertTrue(isSquare)
}
```

Notice how we are simply passing data into the `isSquare(width:height)` function and observing the output. Another test would pass different values for `width` and `height` into the function and assert on that output.

#### State Verification

This is similar to Return Value Verification, but deals with objects that manage some sort of internal "state".

* Setup the SUT with an initial state.
* Send the SUT a message that should modify that state.
* Call an accessor method to get the state back and verify that it was mutated correctly.

##### Example

Let's say we're working on the authentication flow for our app. We might have `SignInViewController` (just pretend it's a bit more interesting):

```swift
class SignInViewController: UIViewController {
    // ...
}
```

If our authentication flow is complex, we'd probably like to factor some of the presentation flow logic out into its own class:

```swift
class SignInFlowCoordinator {

    private let navigationController: UINavigationController
    var currentViewController: UIViewController? {
        return navigationController.topViewController
    }

    init(navigationController: UINavigationController) {
        self.navigationController = navigationController
    }

    func startSignInFlow() {
        guard navigationController.topViewController == nil else { return }

        let signInViewController = SignInViewController()

        navigationController.pushViewController(signInViewController, animated: true)
    }
}
```

From here, we can employ State Verification to make sure the `currentViewController` property behaves as we expect:

```swift
func test_startSignInFlow_presentsSignInViewController() {
    // Arrange
    let navigationController = UINavigationController()
    let flowCoordinator = SignInFlowCoordinator(navigationController: navigationController)

    // Act
    flowCoordinator.startSignInFlow()

    // Assert
    XCTAssertNotNil(flowCoordinator.currentViewController as? SignInViewController)
}
```

#### Behavior Verification

This is the most difficult and involved type of test to write. Use this technique when you need to test something that performs side effects (i.e. interacts with another underlying object). In this technique, you'll make use of a mock object to verify functionality.

* Setup the SUT with an initial state, injecting mock object(s) via something like [dependency injection](https://en.wikipedia.org/wiki/Dependency_injection).
* Send the SUT a message.
* Verify the mock object was called correctly.

##### Example

Network requests are a prime example of side effects that you typically don't want to be performed as part of your core unit testing infrastructure. Suppose you're building a networking layer and have created `NetworkService` class as a thin wrapper around `URLSession`. In order to perform proper dependency injection, let's first declare a `NetworkSession` protocol so that we can mock the call to `URLSessions`'s `dataTask(with url:)` method:

```swift
protocol NetworkSession {
    func dataTask(with url: URL) -> URLSessionDataTask
}

extension URLSession: NetworkSession { }
```

Notice how we made sure the function name and signature matches with the one declared in `URLSession`. This allows us to create a zero-code extension declaring `URLSession` as conforming to our `NetworkSession` protocol.

From here, our `NetworkService` class might look something like this:

```swift
class NetworkService {
    private let session: NetworkSession

    init(session: NetworkSession = URLSession.shared) {
        self.session = session
    }

    func executeURL(_ url: URL) {
        let task = session.dataTask(with: url)
        task.resume()
    }
}
```

Notice how we require an instance of the `NetworkSession` protocol in the initializer. Because `URLSession` now conforms to the protocol, we can also use `URLSession.shared` as the default value to allow our actual app code to be a little cleaner.

Since we won't be using an actual instance of `URLSession` in our test, we'll also need to create a fake version of it called `MockNetworkSession`:

```swift
class MockNetworkSession: NetworkSession {
    private(set) var dataTaskWithURLCallCount = 0
    private(set) var dataTaskWithURLLastURL: URL?

    func dataTask(with url: URL) -> URLSessionDataTask {
        dataTaskWithURLCallCount += 1
        dataTaskWithURLLastURL = url

        return URLSessionDataTask()
    }
}
```

Notice how the mock captures information about how it was called. We're capturing both the number of times `dataTask(with:)` is called as well as the value of the `URL` parameter that was passed into it.

Now it's time to write the test. We want to make sure calling `executeURL()` on our `NetworkService` class will create the `URLSessionDataTask` from the `NetworkSession` provided at initialization time (i.e. we want to make sure our mock's `dataTask(with:)` method gets called in the correct manner):

```swift
func test_executingNetworkService_generatesDataTaskFromUnderlyingNetworkSession() {
    // Arrange
    let mockSession = MockNetworkSession()
    let service = NetworkService(session: mockSession)
    let url = URL(string: "http://apple.com")!

    // Act
    service.executeURL(url)

    // Assert
    XCTAssertEqual(mockSession.dataTaskWithURLCallCount, 1)
    XCTAssertEqual(mockSession.dataTaskWithURLLastURL, url)
}
```

The test is pretty self-explanatory - we create an instance of our `NetworkService` class using a `MockNetworkSession`, call `executeURL()` on it, and then make sure the `MockNetworkSession` was called the correct number of times and with the `URL` we provided.

This basic example shows how a mock object is used to verify the interaction between the `NetworkService` class and its underlying `NetworkSession` dependency. To complete the testing of our `NetworkService` class, we'd also want to create a mock version of the underlying `URLSessionDataTask` so that we can verify `resume()` is called to actually execute the request. As it stands now, we're calling `resume()` on a live `URLSessionDataTask`, which technically executes the request on the network. A more complete version of this example can be found in our [Hyperspace networking library](https://github.com/BottleRocketStudios/iOS-Hyperspace/blob/master/Tests/Helper/Mocks/MockNetworkSessionDataTask.swift).

### FIRST

Unit tests should be [FIRST](https://pragprog.com/magazines/2012-01/unit-tests-are-first):

* Fast - slow tests are not likely to be run as often.
* Isolated - tests should not depend upon external factors. Following the [single responsibility principle](https://en.wikipedia.org/wiki/Single_responsibility_principle), tests should have only one reason to fail.
* Repeatable - re-running a test should produce consistent results.
* Self-Verifying - make use of test assertion functions to verify conditions (as opposed to relying on a human to interpret results from something like console output).
* Timely - write your tests when you write your feature. You don't have to practice strict Test Driven Development (TDD) by writing your tests beforehand, but you should make an effort to test your code before you move on to the next feature.

### Quality of Test Code

Treat test code with the same amount of care as you would for your app code. This:

* Allows the test code to scale in the same manner as the app code.
* Prevents low quality test code from becoming a burden to maintain as the app changes.

Another guard against poor quality test code can be implemented at the source control level:

* As always, concentrate on small, cohesive commits. This means ensuring your commits that involve tests are *separate* from the ones that introduce or modify features.
* Along the same lines, create *independent* code reviews for features and functionality vs the tests of those features. This allows reviewers to better focus on improving the quality of the tests.

### Arrange, Act, Assert

To improve clarity of your tests, you should aim to structure your tests into 3 distinct sections:

* Arrange - this is where you setup your system under test (SUT).
* Act - this is where you actually perform your test (calling a method on the SUT).
* Assert - this is where you should make sure your SUT behaved in an expected manner.

See the examples above for an illustration of this.

### Documenting Tests

Take some time to ensure your tests are as self-documenting as possible. This will help greatly with test maintenance over long periods of time.

#### Use a naming convention for test names

Careful consideration of test method names can greatly increase clarity in your unit test classes. We like to use the following naming convention for unit test methods:

```swift
func test_condition_expectedResult() {
    // ...
}
```

#### Use the right assertion for the job

Prefer specific assertion macros (i.e. `XCTAssertEqual()`, `XCTAssertTrue()`, etc.)  as opposed to the general ones (i.e. `XCTAssert()`) to improve context around the expected results. Also make sure to provide custom failure messages when making use of the general `XCTAssert()` function.

### Bad Unit Tests

Avoid some common pitfalls associated with bad unit tests:

* No actual assertions (i.e. trying to game the code coverage by calling functions and classes, but not checking for expected output).
* Indiscrete tests (i.e. long test methods that attempt to test many aspects of the SUT at once as opposed to creating several test methods that each test a specific piece of functionality).

### Testing Private Methods

A common question when first beginning to write unit tests is "how do I test the private methods?". Your unit tests should actually just focus on testing public methods. Any underlying private methods should implicitly get tested as a result. If you find yourself needing to test something that's only privately accessible, that probably indicates some refactoring is necessary. A common solution can be to extract the private business logic into a structure that's injected into the SUT at creation time.

### Testing the Tests

Always take a few extra minutes to test your tests! Don't trust a "green" test on the first try.

* Make the simplest change in the source code of the SUT that will trigger a test failure.
* Validate your change by seeing the test fail.
* Undo the change and re-validate that the test passes again.

### Removing Tests

In general, avoid temptations to remove tests. A well-tested codebase is a safety net to avoiding bugs in the future. However, there are a few valid reasons why you might need to remove a test:

* When the feature or functionality the test was testing is removed.
* When the test is non-deterministic (i.e. relies on external conditions/network, relies on randomness, etc.).

### Architecting for Testability

There are several tips for making the code you write easier to test:

* Make use of abstractions where it makes sense.
    * Favor protocols over concrete classes.
* Keep methods highly cohesive.
    * Create several short/succinct/specialized methods as opposed to a few large methods.
* Separate logic from effects.
    * Get a list of inputs and outputs and identify dependencies on global state (e.g. file system, time/date, etc.).
    * Factor out logic that affects the global state (e.g. creating a `CleanupPolicy` struct as opposed to hard-coding the logic into a `cleanCache()` method).
    * Turn side effects into the output of functions. This makes the outputs testable rather than having to use a mock object.

In the end, testable code exhibits these characteristics:

* Provides a way for the client to have control over the inputs.
* Provides a way for the client to have control over the outputs.
* Avoids relying on internal state that may affect later output.

[WWDC 2017 Session 414 - Engineering for Testability](https://developer.apple.com/videos/play/wwdc2017/414/) is a great resource to see some of these techniques in action.

### Unit Testing Frameworks

There are several frameworks out there like [Quick](https://github.com/Quick/Quick), [Nimble](https://github.com/Quick/Nimble), [EarlGrey](https://github.com/google/EarlGrey), and [OCMock](http://ocmock.org/) designed to make writing unit tests easier. In general, we avoid using such frameworks in order to keep our dependencies to a minimum and keep the barrier to entry for writing unit tests low. We want to make it as quick and easy for every engineer on the team to be able to contribute tests to the project.

## UI Testing

### UI Automation Testing (`XCUITest`)

UI automation tests typically require more time to develop and maintain, but can be useful for verifying full system integration. These types of tests are typically the lowest priority for us since there is some potential for overlap with QA's automation efforts using [Appium](https://appium.io/).

### Snapshot Testing

Snapshot tests are like unit tests for individual UI components (or entire screens). We use the popular [iOSSnapshotTestCase](https://github.com/uber/ios-snapshot-test-case) library.

## Continuous Integration

Regular execution of the test suite should be part of your continuous integration process. Our open source projects use [Travis CI](https://travis-ci.org/BottleRocketStudios) to ensure that all tests are executed every time a pull request is created or updated. Other tools like [Fastlane scan](https://docs.fastlane.tools/actions/scan/) can be used to easily create test suite execution workflows.

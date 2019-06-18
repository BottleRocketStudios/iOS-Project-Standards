# Build Warnings and Errors

## Build-time

### Project Warnings

Project warnings add clutter and make it difficult to identify potential issues in your codebase.

* Aim for **0** warnings during build-time. If not obtainable due to third party libraries, aim for 0 warnings in all of the code that *you* own.
* If using Cocoapods, use the `inhibit_all_warnings!` flag to [silence warnings from pods](https://stackoverflow.com/a/13209057/4343618).

## Run-time

### Console Noise

Excessive console output makes it difficult to see useful information when running/testing/debugging your app. The worst offences involve console output that is a constant stream of incomprehensible text. The console log is a valuable tool. Don't put your project in a situation where developers are forced to ignore it!

* Keep logging to a minimum if possible. When you need to have a robust logging mechanism, consider using a logging library that allows for configurable logging levels (e.g. debug, info, warn, error, etc.). You can also use Apple's [unified logging system](https://developer.apple.com/documentation/os/logging), os_log, to accomplish this.
* If you do need to output log information to the console, consider using the capability of a breakpoint to output console output, so that logging code does not get committed to the code repository.
* Keep an eye out for system warnings that only manifest themselves in console output. Most often, these are things like unsatisfiable constraints or situations where UIKit methods are called on a background thread.
* Scrub all logging from production builds of your application. Excessive logging in a production app can make it easier for others to reverse engineer your app.

### Project Breakpoints

There are 3 types of breakpoints that can be used within Xcode:

* Code breakpoints
    * You're probably most familiar with these. You click on a line number to create a breakpoint for that line of code. The next time the app executes that line of code, it will interrupt the app just before execution to give you a chance to inspect the state of the app. From here, you can step over a line of code to execute the app line-by-line, step into/out of functions, or resume normal execution.
* Symbolic breakpoints
    * These breakpoints aren't set on any specific line of code, but are used to pause app execution when a specific method or function is called. These can be useful if you want to pause execution when a specific method is called, but are not sure where the method is being called from.
* Exception breakpoints
    * These breakpoints pause app execution when an exception or crash occurs. Enabling these breakpoints typically gives you a head start on fixing crashes since you'll get context about what triggered the failure instead of execution halting on `main()` with a generic error like `SIGABRT`.

In addition to making debugging easier, breakpoints can provide a useful safety net against latent errors that occur at run-time. Ensure that all developers have these enabled as "User Breakpoints" so that they persist across any project that's opened up in Xcode. Better yet, promote them to "Shared Breakpoints" so that they live in source control with the rest of the project files. For more information, check out our article on the [top 5 iOS breakpoints](https://medium.com/rocket-fuel/ios-breakpoint-secret-sauce-for-better-debugging-c0009f116ca1):

* All Objective-C Exceptions
* `-[UIApplication main]` (importing UIKit to enhance code completion in the debugger)
* `UIViewAlertForUnsatisfiableConstraints`
* `-[UIView(UIConstraintBasedLayout) _viewHierarchyUnpreparedForConstraint:]`
* `UICollectionViewFlowLayoutBreakForInvalidSizes`

### Sanitizers

Sanitizers also exist to help you catch run-time issues.

#### Address Sanitizer

The [Address Sanitizer](https://developer.apple.com/documentation/code_diagnostics/address_sanitizer) (ASan) is an LLVM based tool for detecting memory corruptions and other memory errors at run-time. [Enable ASan](https://developer.apple.com/documentation/code_diagnostics/address_sanitizer/enabling_the_address_sanitizer) in your app's scheme.

#### Thread Sanitizer

The [Thread Sanitizer](https://developer.apple.com/documentation/code_diagnostics/thread_sanitizer) (TSan) is an LLVM based tool for detecting race conditions at run-time. [Enable TSan](https://developer.apple.com/documentation/code_diagnostics/thread_sanitizer/enabling_the_thread_sanitizer) in your app's scheme.

#### Undefined Behavior Sanitizer

The [Undefined Behavior Sanitizer](https://developer.apple.com/documentation/code_diagnostics/undefined_behavior_sanitizer) (UBSan) is an LLVM tool for detecting undefined behavior at run-time. These are things like dividing by zero, loading memory from a misaligned pointer, or dereferencing a null pointer. [Enable UBSan](https://developer.apple.com/documentation/code_diagnostics/undefined_behavior_sanitizer/enabling_the_undefined_behavior_sanitizer) in your app's scheme.

#### Main Thread Checker

The [Main Thread Checker](https://developer.apple.com/documentation/code_diagnostics/main_thread_checker) is a tool that detects when AppKit/UIKit APIs are called on a background thread (which often leads to unexpected behavior). By default, the Main Thread Checker is enabled in your app's scheme when you debug your app using Xcode.

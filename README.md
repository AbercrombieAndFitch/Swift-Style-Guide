# Abercrombie & Fitch - Swift Style Guide

This guide was directly modified from [Netguru's Swift Style Guide](https://github.com/netguru/swift-style-guide/blob/master/README.md) which was based on:

- [The Swift Programming Language](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language)
- [Github Swift style guide](https://github.com/github/swift-style-guide)
- [Ray Wenderlich Swift style guide](https://github.com/raywenderlich/swift-style-guide)

There have been modifications and additions to the original guide. As time goes on the team at Abercrombie & Fitch will be modifying this guide to fit styles agreed upon by the team as a whole.

### Purpose of the style guide

This guide should help instruct your Swift code style and improve the over all:
- testability
- readability
- consistency
- simplicity

The reasoning behind each style choice will be detailed where appropriate, however for many of these sections the reasoning is merely help achieve those goals listed above, and to keep formatting issues out of github diffs. If a developer feels like it will be beneficial to deviate from the style guide they will be expected to create a pull request to update this guide before deviating on a regular basis. This guide isn't meant to interfere with coding nor progress. If a developer fails for follow the guide please point them to the section of this guide that handles the error so they know moving forward. Also if a particular code change requires the developer to deviate from the style guide it is encouraged that they deviate as long as the deviation helps to maintain the goals listed above.

## Table of contents

* [Testing Guide](#testing-guide)
* [Test Naming](#test-naming)
* [Testing Helpers](#testing-helpers)
* [Test File Structure](#test-file-structure)
* [Failing CI Builds](#failing-ci-builds)
* [Obj-C vs Swift](#obj-c-vs-swift)
* [Spacing](#spacing)
* [Comments](#comments)
* [Code organization](#code-organization)
* [Function Size](#function-size)
* [Naming](#naming)
* [Closures](#closures)
* [Types](#types)
* [Mutability – `let` over `var`](#mutability--let-over-var)
* [Optionals](#optionals)
* [Static vs dynamic code](#static-vs-dynamic-code)
* [Implicit getters](#implicit-getters)
* [Access control](#access-control)
* [Explicit references to `self`](#explicit-references-to-self)
* [Value types over reference types](#value-types-over-reference-types)

## Testing Guide

Following a TDD approach this guide will begin with an overview of testing patterns. Swift projects will use XCTest in conjunction with [Nimble](https://github.com/Quick/Nimble). Nimble was chosen to provide additional helpful matchers not included in the core XCTest framework. Developers are free to use matchers from either testing framework. However in a debate the Nimble matchers should be chosen for consistency.

All code should be written so that it is as testable as possible while maintaining the over all quality. The goal for each project will be that the code is 100% tested. That allows developers to reliably trust that a function will behave as expected and allow the developer to make assumptions based on the expected behavior.

If instances arise where testing goals need to be phased down it is expected that this code will already be isolated to classes or structs that cab be clearly omitted. Possible example: Subclasses of UIView.

If a particular test is taking longer to write than the method it is testing feel free to reach out for help on efficient ways to test that code.

Tests should only focus on one outcome per test

**Prefered**
```swift
func test_abs_givenANegativeNumber_itShouldHaveAPostiveResult() {
  let result = @(abs(-5))
  expect(result).to(equal(@(5)));
}

func test_abs_givenAPositiveNumber_itShouldHaveAPostiveResult() {
  let result = @(abs(7))
  expect(result).to(equal(@(7)));
}
```

**Not Prefered**
```swift
func test_abs_givenANumber_itShouldHaveAPostiveResult() {
  let result1 = @(abs(-5))
  expect(result1).to(equal(@(5)));

  let result2 = @(abs(7))
  expect(result2).to(equal(@(7)));
}
```

### Test Naming

Tests should initialize an instance that will be used for testing. This instance will be referred to as the testInstance. This clearly helps distinguish it from other objects that might be initialized in the test file. A common name also helps to quickly identify the object under test when adding to an existing test file.

Tests should be named using the following format

test_Unit-Being-Tested_Context_Expected-Result

Example func abs(_ x: Double) -> Double

test_abs_givenANegativeNumber_itShouldHaveAPostiveResult

### Testing Helpers

When reusing the same test setup or teardown for two or more tests it is encouraged to move that setup or teardown to it's own function. The function name should match the name of the context and provide insight into if the method is intended to be setup or teardown.

```swift
func setup_ScreenIsEnabled() {
  // Setup
}

func test_toggleEnabled_ScreenIsEnabled_itShouldDisableTheScreen() {
  setup_ScreenIsEnabled()
  // Test
}
```

Factory methods can also be used and be prefixed with factory_
example factory_userWithAgeRestriction

If needed it is permissible to create test helpers for factories used in multiple testing files, example reading stored JSON or building a stubbed object.

### Test File Structure

Test files should have the following structure

```swift
import XCTest
// Imports

class SystemUnderTest: XCTestCase {

    var testInstance : SystemUnderTest!

    override func setUp() {
        super.setUp()
        testInstance = // init
        // Put setup code here. This method is called before the invocation of each test method in the class.
    }

    override func tearDown() {
        // Put teardown code here. This method is called after the invocation of each test method in the class.
        super.tearDown()
    }

    // Custom Setup Functions

    func setup_Example() {
    }

    // Tests
    func test_Example() {
        // This is an example of a functional test case.
        // Use XCTAssert and related functions to verify your tests produce the correct results.
    }
}
```

### Failing CI Builds
CI builds will be set to fail for a change in any of the following metrics.
- an increase in errors
- an increase in Failing Tests
- an increase in warnings
- a decrease in code coverage

It will be up to the developer who submitted the PR to address the issues and make sure the code is in a merge-able state before bringing it to the team for code review.

## Obj-C vs Swift
Many of the projects will involve having to work with Obj-C and Swift. When working with the two languages it will be left the developers discretion as to which language they should use. The overall goal will be to switch to Swift however if a code change is needed in the Obj-C files (and the change can be made quicker in Obj-C) it is encouraged to use Obj-C instead of taking the time to convert the file to Swift.

## Spacing

Indent using 4 spaces. Be sure to set this preference in Xcode.

Vertical spaces should be used in long methods to separate its name from implementation. You may also want to use vertical spaces to separate logic within a function. Shorter methods (one or two lines) don't need such spacing.

## Comments

Comments should be avoided as much as possible. Remember that code should be self-documenting, so use comments only if necessary. Example from one of the Obj-C projects:

```objc
- (void)tableView:(UITableView *)tableView commitEditingStyle:(UITableViewCellEditingStyle)editingStyle forRowAtIndexPath:(NSIndexPath *)indexPath {
// This is an empty method needed for iOS 8.3. Without it swipe left to edit will not work. Found the solution on https://gist.github.com/scheinem/e36835db07486e9f7e64
}
```
If you decide to add comments, keep them up-to-date. Unmaintained comments should be removed.

## Code organization

Source files should have the following organization.

```swift
// 1. imports

import MoneyKit

// 2. classes, structs, enums

class Wallet {

// 2.1. public, internal properties

let cards: [Card]
private(set) var cash: Cash

// 2.2. private properties

unowned private let owner: Person

// 2.3. initializers

init(cash: Cash, cards: [Card], owner: Person)

// 2.4. public, internal functions

func affordsTransaction(transaction: Transaction) -> Bool

// 2.5. private functions

private func cardWithSuffiecientCash(cash: Cash) -> Card?

}

// 3. extensions, protocol implementations

extension Wallet: Printable {

  var description: String {
    return "\(owner.name) has \(cash) cash and \(cards.count) cards"
  }

}
```

Such organization helps others to reach important content earlier. It also saves time, confusion and improves readability.

## Function Size
Although this will fluctuate, functions should be kept to approximately four lines of code. This forces the developer to be mindful of how the code can be self documenting and allows it to be easily maintained and scaled in the future.

## Naming

Names should be meaningful and written in camelCase. When possible the name should also be compact however consistency and clarity vastly outweigh length. Try to ask yourself whether the name of a type sufficiently explains its behavior. Meaningful naming is very important to other developers because they define some expectations about their own roles.

### Generic types

Names of classes, structs, enums, enum cases, type aliases, protocols and generic types should be capitalized. Generic types' names should start with letter `T`, then `U`, `V` and so on.

### Functions and arguments

Function names should be as descriptive and meaningful as possible. Try to express its intent in the name, by keeping it compact at the same time.

Arguments should also be descriptive. Remember that you can use argument labels, which may be more meaningful to a user.

The first argument's name can be a part of the function's name...

```swift
func convertPoint(point: Point, fromView view: View) -> Point
```

... or its label can be explicitly required.

```swift
func convert(#point: Point, toView view: View) -> Point
```

Use default values for arguments where a function expects any value or some specific value most of the time. If a particular argument is not required for a function, it's good to make it optional and `nil` by default.

## Closures

If the last argument of a function is a closure, use [trailing closure syntax](https://developer.apple.com/library/ios/documentation/Swift/Conceptual/Swift_Programming_Language/Closures.html#//apple_ref/doc/uid/TP40014097-CH11-ID102).

Trailing closure syntax should be used if a function accepts a closure as its last argument. If it's its only argument, parentheses may be omitted. Unused closure arguments should be replaced with `_` (or fully omitted if no arguments are used). Argument types should be inferred.

```swift
func executeRequest<T>(request: Request<T>, completion: (T, Error?) -> Void)

executeRequest(someRequest) { (result, _) in /* ... */ }
```

Use implicit `return` in one-line closures with clear context.

```swift
let numbers = [1, 2, 3, 4, 5]
let even = filter(numbers) { $0 % 2 == 0 }
```

Also, remember that global functions are closures and sometimes it's convenient to pass a function name as a closure.

```swift
func isPositive(number: Int) -> Bool

let numbers = [-1, 2, 3, -4, 5]
let positive = filter(numbers, isPositive)
```

When the body of a closure grows beyond three lines it is encouraged that the body be separated out to it's own function.

## Types

Try to use native Swift types before you come up with your own. Every type can be extended, so sometimes instead of introducing new types, it's convenient to extend or alias existing ones.

Remember that Objective-C classes that have native Swift equivalents are not automatically bridged, e.g. `NSString` is not implicitly bridged to `String` in the following example.

```swift
func lowercase(string String) -> String

let string: NSString = /* ... */

lowercase(string) // compile error
lowercase(string as String) // no error
```

Types should be inferred whenever possible. Don't duplicate type identifier if it can be resolved in compile time:

```swift
// preferred

let name = "John Appleseed"
let planets = [.Mars, .Saturn]
let colors = ["red": 0xff0000, "green": 0x00ff00]
```

```swift
// not preferred

let name: String = "Amanda Smith"
let planets: [Planet] = [.Venus, .Earth]
let colors: [String: UInt32] = ["blue": 0x0000ff, "white": 0xffffff]
```

Also, associate colon with type identifier.

```swift
// preferred

class VideoArticle: Article
let events: [Timestamp: Event]
```

```swift
// not preferred

class VideoArticle : Article
let events : [Timestamp : Event]
```

Type aliases should be short and meaningful.

```swift
// preferred

typealias MoneyAmount = Double
```

```swift
// not preferred

typealias Money = Double
```

Types should never have prefixes because their names are already implicitly managed and prefixed by their module name.

## Mutability – `let` over `var`

It's safer to assume that a variable is immutable, thus it's highly recommended to declare values as constants, using `let`. Immutable constants ensure their values will never change, which results in less error-prone code.

Mutable `var` variables should only be used when necessary, e.g. when you're absolutely sure you will be changing their values in the future.

A good strategy is to start every variable as `let` and keep it as such until you must absolutely set it as a var. Code Reviewers should be diligent in calling out `var` vs `let`. Too many `vars` in a particular area of code can have strong negative effects on the over all simplicity and quality of the code.

## Optionals

Force unwrapping should be avoided at almost all cost. Implicitly unwrapped optionals lead to less safe code and can cause unwanted crashes. Use optional chaining or `if-let` bindings to unwrap optional values.

```swift
let user: User? = findUserById(123)

if let user = user {
println("found user \(user.name) with id \(user.id)")
}
```

Unwrapping several optionals in nested `if-let` statements is forbidden, as it leads to ["pyramid of doom"](https://en.wikipedia.org/wiki/Pyramid_of_doom_(programming)). Swift allows you to unwrap multiple optionals in one statement.

```swift
let name: String?
let age: Int?

if let name = name, age = age where age >= 13 {
/* ... */
}
```

### Guard vs if-let
When unwrapping `if-let` is preferred over `guard`. When doing multiple unwraps the following is preferred:

```swift
let name: String?
let age: Int?

if let name = name, age = age where age >= 13 {
/* ... */
}
```

Guard Syntax/Example:

```swift
let name: String?
guard let safeName = name else {
    return
}
```

For the situation above the naming of the variable becomes risky and complex. By using `if-let` syntax listed above there is no risk of accidentally accessing the unwrapped variable or adding bulky qualifiers before a name.

As with other areas of the style guide, if `guard` achieves a goal that `if-let` can not then it is acceptable to use. However the reasoning should be documented in the Pull Request and the deviation discussed with other members of the team. 

### Optionals IBOutlets

In the case of IBOutlets Implicitly unwrapped optionals are acceptable. A nil here would imply that the XIB or Storyboard was constructed incorrectly and could still result in a crash whether we're force unwrapping or not. In those situations the following is the preferred implementation.

```swift
@IBOutlet weak var undoButton: UIButton!
```

### Optionals In Tests

Implicitly unwrapped optionals can sometimes be useful. They may be used in unit tests, where system under test should never be `nil`. There's no point executing rest of the tests if one of them fails.

```swift
var testInstance: SystemUnderTest!

beforeEach {
  testInstance = /* ... */
}

afterEach {
  testInstance = nil
}

it("should behave as expected") {
  testInstance.run()
  expect(testInstance.running).to(beTrue())
}
```

## Static vs. dynamic code

Static code is code where logic and control can be resolved at compile-time. The Swift compiler is able to optimize predictable code to work better and faster. Try to make use of this feature and write as much static code as possible.

On the other hand, dynamic code's control flow is resolved at run-time, which means it's not predictable and, as a result, can't be optimized by the compiler. Avoid using `dynamic` and `@objc` attributes.

## Implicit getters

Read-only computed properties don't need an explicit getter, thus it can be omitted. This also applies to read-only subscripts.

```swift
struct Person {

let height: Float
let weight: Float

var bmi: Float {
return weight / (height * height)
}

}
```

## Access control

Access control modifiers should be specified on a top-level scope.

```swift
public extension String {
var uppercaseString: String
}
```

Use access control modifiers only if necessary. Pay attention to variables. Use `private` or `private(set)` appropriately. Don't add modifiers if they are are already a default.

```swift
// preferred

extension String {
var uppercaseString: String
}
```

```swift
// not preferred

internal extension String {
var uppercaseString: String
}
```

## Explicit references to `self`

Explicit references to `self` should only take place in closures and when the language requires it.

```swift
struct Person {

let firstName: String
let lastName: String

var fullName: String {
return "\(firstName) \(lastName)"
}

init(firstName: String, lastName: String) {
self.firstName = firstName
self.lastName = lastName
}

}
```

## Value types over reference types

Value types, such as structs, enums and tuples are usually simpler than reference types (classes) and they're always passed by copying. This means they're independent thread-safe instances, which makes code simpler and safer. In addition, `let` and `var` work as expected.

Value types are great for representing **data** in the app.

```swift
struct Country {
let name: String
let capital: City
}
```

On the other hand, reference types, such as classes, are passed by referencing the same mutable instance in memory. There are several cases when classes should be preferred. The first case emerges when subclassing current Objective-C classes. The second one is when you need to use reference types with mutable state, or if you need to perform some actions on deinitialization.

Reference types are great to represent **behavior** in the app.

```swift
class FileStream {
let file: File
var currentPosition: StreamPosition
}
```

Keep in mind that inheritance is not a sufficient argument to use classes. You should try to compose your types using protocols.

```swift
// preferred

protocol Polygon {
var numberOfSides: Int { get }
}

struct Triangle: Polygon {
let numberOfSides = 3
}
```

```swift
// not preferred

class Polygon {
let numberOfSides: Int
init(numberOfSides: Int)
}

class Triangle: Polygon {
init() {
super.init(numberOfSides: 3)
}
}
```

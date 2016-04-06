## Testing Framework

There are a multitude of testing libraries available that enable iOS developers to take advantage of all the benefits of writing tests. Since Xcode 5, Xcode automatically adds a test target that is linked to the XCTest library to any new project. This test framework allows us to write unit tests out of the box, without any additional libraries. Writing integrations tests is also possible without using any external frameworks using Xcode's automation instrument.

### Unit Testing

We can exclusively use XCTest for unit testing, or we can add another framework that facilitates BDD by making our tests clearer or more robust with custom matchers. These other frameworks run right on top of XCTest, their intent being the addition of useful syntax to the already bundled testing framework.

Using another framework in addition to XCTest is also advantageous if you want to stub methods, stub API responses, or create mock objects to aid in testing. Deeper coverage of unit testing with iOS is available in the book, [Test Driven iOS Development](http://www.amazon.com/Test-Driven-iOS-Development-Developers-Library/dp/0321774183).

BDD Frameworks:

1. Kiwi (includes mocks and stubs as well)
2. Specta and Expecta (the latter adds custom matchers)
3. Cedar

Mocks and Stubs:

1. OCMock
2. OHHTTPStubs (for network requests)
3. Nocilla (for network requests)

We'll include testing examples using Kiwi and XCTest.

### Running Tests

Running tests for a project in Xcode is as simple as hitting cmd-U. Run your tests now to ensure that your project has a HumonTests target that uses XCTest, set up for you by Xcode when you first started the project.

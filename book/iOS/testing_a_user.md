## Testing the User Object

### Testing Using XCTest

Our user object only has two methods to test, `initWithJSON:` and `isCurrentUser`. In the HumonTests directory, create a new test case class called HUMUserTests that is a subclass of XCTestCase. Add a static constant that will be the fake, placeholder ID we will use for the user object we're testing.

	static NSString *const HUMUserID = @"fakeUserID";

You'll be provided with a `setUp` method where you can put code that needs to be run right before running your test cases. In our case, we need to create an instance of the HUMUser class that we can use for our tests. We only need to do this once, so add it to the `setUp` method. Be sure to import HUMUser.h in this file and add the user property `@property (strong, nonatomic) HUMUser *user;` into the @interface.

	// HUMUserTests.m
	
	- (void)setUp
	{
    	[super setUp];
    	self.user = [[HUMUser alloc] initWithJSON:@{@"id" : HUMUserID}];
	}

Our first test will check that instantiating a user object doesn't return nil.

	// HUMUserTests.m
	
	- (void)testNotNil
	{
    	XCTAssertNotNil(self.user, @"User object should not be nil");
	}

Notice that this test is an instance method that returns `void` and starts with the word `test`. XCTest will not automatically run tests that don't conform to this convention. XCTest provides a variety of assertions that we can use, like `XCTAssertNotNil`, which checks that the first argument is non nil and prints the second string argument if the test fails.

Now let's test that the `initWithJSON:` method accurately sets the user's ID.

	// HUMUserTests.m
	
    - (void)testID
    {
    	XCTAssertEqualObjects(self.user.userID, HUMUserID,
                          	@"User ID should equal %@", HUMUserID);
    }

This time we'll use the `XCAssertEqualObjects` macro, which takes two arguments in addition to the error message. The error message itself a printf-style string, like the format string we pass to the `NSLog()` function. So, it can contain placeholders for useful arguments such as the `HUMUserID` that the ID should be equal to.

	...@"User ID should equal %@", HUMUserID);

Now we need to test if a user object can correctly determine if its ID is the same as the current user. Before checking this, we'll need to `setUserID:` to the same string as our `self.user.userID`.

	// HUMUserTests.m
	
	- (void)testIsCurrentUser
	{
		[HUMUserSession setUserID:HUMUserID];
    	BOOL isCurrentUser = [self.user isCurrentUser];
    	XCTAssertTrue(isCurrentUser, @"User ID should match current user ID");
	}

Run your tests now. If one of your tests fails, read the logs for something like "User ID should equal fakeUserID" or check the test navigator.

![Xcode's Test Navigator](images/ios_testing_a_user_1.png)

### Testing Using Kiwi

Now lets see how these tests would look if we wrote them using Kiwi's syntax. In the HumonTests directory, create a new test case class called HUMUserKiwiTests that is a subclass of XCTestCase. We'll be removing the entire implementation of this class and replacing it with tests using Kiwi syntax.

All of our user tests will be contained in this one spec file, contained within `SPEC_BEGIN(HUMUserSpec)` and `SPEC_END`. Our user spec will only contain one describe block, for testing our user object.

	// HUMUserKiwiTests.m
	
    #import "Kiwi.h"
    #import "HUMUser.h"
    #import "HUMUserSession.h"

    SPEC_BEGIN(HUMUserSpec)

    describe(@"User object", ^{
        
    });

    SPEC_END

Within the describe block, we'll define a user and a user ID string. We use the keyword `__block` so that modifications to these variables in the `beforeAll` block will be visible in our other test case blocks.

Since we only need to create one user object to be used in our user tests, we can place the initialization of this user object in a `beforeAll` block. This is especially helpful when we need to test an object that requires a lot of set up, but don't want to repeat this lengthy setup before every test.

	// HUMUserKiwiTests.m
	
    describe(@"User object", ^{
        
        __block HUMUser *user;
        __block NSString *userID = @"fakeUserID";
        
        beforeAll(^{
            user = [[HUMUser alloc] initWithJSON:@{@"id" : userID}];
        });
        
    });

The `it` blocks that come after the `beforeAll` block include a human readable string that will be logged if our test fails as well as a block that contains the actual test.

	// HUMUserKiwiTests.m
	
    it(@"should not be nil", ^{
        [[user shouldNot] beNil];
    });
    
    it(@"should have the correct ID", ^{
        [[user.userID should] equal:userID];
    });

    it(@"should match ID to current user", ^{
    	[HUMUserSession setUserID:userID];
    	BOOL isCurrentUser = [user isCurrentUser];
    	[[theValue(isCurrentUser) should] equal:theValue(YES)];
	});
    	
The last test checks if a user will correctly determine if its ID is the same as the current user, like we did before with XCTest. One of the differences is that with Kiwi, we box scalar values (like bools or ints) using the `theValue()` macro.

You'll notice that Kiwi tests read a bit differently than XCTests, with Objective-C style test syntax that favors blocks. Both test specs are testing the same cases, though. Whatever your deciding factors in choosing a test framework, good on you for taking the time to write tests.

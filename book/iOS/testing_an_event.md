## Testing the Event Object

### Testing Using XCTest

To test our event methods, create a new test case class called HUMUserSessionTests in the HumonTests directory that is a subclass of XCTestCase.

We'll need a property for a JSON dictionary that we'll use to create an event, and for an event that we'll use across our tests. Remember to import the HUMEvent and HUMJSONFileManager classes as well.

	// HUMEventTests.m
	
	@property (copy, nonatomic) NSDictionary *JSON;
	@property (strong, nonatomic) HUMEvent *event;

Then we'll create instances for these properties in the `setUp` method.

	// HUMEventTests.m
	
	- (void)setUp
	{
	    [super setUp];
	
	    self.JSON = [HUMJSONFileManager singleEventJSON];
	    self.event = [[HUMEvent alloc] initWithJSON:self.JSON];
	}

Now we'll test event creation, as well as multiple event creation. For multiple event create, we check that the `eventsWithJSON:` method returns two objects in an array given a JSON array with two dictionaries.

	// HUMEventTests.m
	
	- (void)testInitialization
	{
	    XCTAssertNotNil(self.event, @"Should initialize event with JSON");
	}
	
	- (void)testMultipleEventInitialization
	{
	    NSArray *JSONarray = [HUMJSONFileManager multipleEventsJSON];
	    NSArray *eventArray = [HUMEvent eventsWithJSON:JSONarray];
	    XCTAssertEqual(eventArray.count, JSONarray.count,
	                   @"Should initialize multiple events with JSON");
	}

Next we'll test helper methods on an event. We want to ensure that the JSON returned by the `JSONDictionary` method is the same as the JSON that we used to create the event.

We need a human readable string for each event so we have something to share via social networks later. We know what format we want this string is, so we'll check that what's returned formats the information correctly.

	// HUMEventTests.m
	
	- (void)testJSONGeneration
	{
	    BOOL jsonIsTheSame = [[self.event JSONDictionary]
	                          isEqualToDictionary:self.JSON];
	    XCTAssertTrue(jsonIsTheSame, @"Event should return valid JSON");
	}
	
	- (void)testHumanReadableString
	{
	    NSString *readableString = @"Picnic at Dolores Park, 9/15/13, 5:00 PM";
	    XCTAssertEqualObjects(self.event.humanReadableString, readableString,
	                          @"Event should initialize with JSON");
	}

Lastly, we changed equality on event objects (by overwriting `isEqual` and `hash`) so that two events with the same ID and the same creator ID will evaluate as equal.

	// HUMEventTests.m

	- (void)testEquality
	{
	    HUMEvent *sameEvent = [[HUMEvent alloc] initWithJSON:self.JSON];
	    XCTAssertEqualObjects(self.event, sameEvent,
	                          @"Events should be equal with the same ID and user");
	}
	
Now, run your tests by hitting cmd+U.

### Testing Using Kiwi

Now we'll create the same tests using Kiwi. Create a new test case class exactly you did before but called HUMEventKiwiTests.m

Now remove the entire implementation and replace it with a Kiwi formatted spec within `SPEC_BEGIN(HUMEventSpec)` and `SPEC_END`. Our user spec will only contain one describe block for our event object tests.

	// HUMEventKiwiTests.m
	
	#import "Kiwi.h"
	#import "HUMEvent.h"
	#import "HUMJSONFileManager.h"
	
	SPEC_BEGIN(HUMEventSpec)
	
	describe(@"Event object", ^{
	
	});

	SPEC_END
	
Within the describe block, we need to create a JSON dictionary and event like we did in the XCTest. We use the keyword `__block` so that modifications to these variables in the `beforeAll` block will be visible in our other test case blocks.

	// HUMEventKiwiTests.m
	
    __block NSDictionary *JSON;
    __block HUMEvent *event;

    beforeAll(^{
        JSON = [HUMJSONFileManager singleEventJSON];
        event = [[HUMEvent alloc] initWithJSON:JSON];
    });
	
After the `beforeAll`, add `it` blocks that correspond to the tests you wrote before.

	// HUMEventKiwiTests.m
	
    it(@"should not be nil", ^{
        [[event shouldNot] beNil];
    });

    it(@"should initialize multiple events", ^{
        NSArray *JSONarray = [HUMJSONFileManager multipleEventsJSON];
        NSArray *eventArray = [HUMEvent eventsWithJSON:JSONarray];
        [[theValue(eventArray.count) should] equal:theValue(JSONarray.count)];
    });
    
    it(@"should generate JSON from the object", ^{
        BOOL jsonIsTheSame = [[event JSONDictionary] isEqualToDictionary:JSON];
        [[theValue(jsonIsTheSame) should] equal:theValue(YES)];
    });

    it(@"should have a human readable string", ^{
        NSString *readableString = @"Picnic at Dolores Park, 9/15/13, 5:00 PM";
        [[event.humanReadableString should] equal:readableString];
    });

    it(@"should be the same as an event with the same ID and user", ^{
        HUMEvent *sameEvent = [[HUMEvent alloc] initWithJSON:JSON];
        [[event should] equal:sameEvent];
    });
    
Remember to box scalars like `eventArray.count` in `theValue()` when you're evaluating them.

Now run your tests again and revel in the abundance of green check marks.

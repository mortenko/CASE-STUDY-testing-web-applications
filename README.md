# Testing JS web applications

## Dictionary
 - **SUT** - system under test  F.E.  we are writing unit tests then STU is class, method  or object. If we are writing customer tests then SUT is probably whole app.  
When we are writing a test in which we cannot (or choose not) use a real depended-on component (DOC) we can replace it with a Test Double. 
 - **indirect input**   - behavior of the SUT is affected by the values returned by another component whose services are used we call this values indirect inputs.  
 - **indirected outputs** - when behavior of the SUT includes actions that can’t be observed through public API of SUT but which are seen by other systems or app.  components. It could be method or function calls to another component, messages sent on a message channel or records inserted into DB or  written to a file. Verification of the indirect output behaviors of the SUT requires the appropriate observation points   
- **control point** - how the test ask SUT to do something for it.  
- **fixture setup** - before desired logic of SUT can be exercised, the pre-conditions of the test need to be set up 
- **observation point**  - how the test inspects the post-exercise state of the SUT. 
- **test fixture** - all things we need in place in order to run a test and expect  a particular outcome. It’s first phase of the four-phase test.
- **hard-coded test double** - build the test double by hard-coding the return values or expected calls 
 - **depended-on component** - individual class or component on which SUT depends. 
- **result verification** - the test verifies that the expected (correct) outcome has already ocured.
- **assertion methods** (static methods) - give us a way to express the expected outcome in a way that is both executable by computer  and useful to the human reader.
- **testcaseClass** - set of related methods in one class. 
- **state verification** - SUT  after has been exercised and compare it to the expected state. 
- **overspecified software** - test says much about how the SW should be structured or behave
- **substitutable dependency** - SW component may depended on any number of other components. If we are testing component we need to be able to replace the other components  with Test double. 

Explanation of Indirect Inputs and Outputs
 The DOC (depended-on component) may return kind of values or throw exceptions that affect the behavior of the SUT. The indirect inputs received from DOC may be unpredictable (system clock or calendar). In other case DOC may be not available in the test enviroment or may not even exist. 

In other cases we need to verify that certain side -effects of executing the SUT have indeed occured. Its hard monitor these indirect outputs of the SUT.  This problem could be solved using test double.

 **__Testing of Indirect Inputs__**:

   it’s easier then testing indirect outputs bcs they depends on testing inputs. To test the SUT  with indirect inputs we have to be able to control  the depended-on component, which can cause to return every possible value. 
indirect inputs we want to induce include:
*   return values of methods/functions
* values of updatable args
* exceptions that could be thrown
In some cases the test can interact with the depended-on component to set up how it will respond to requests. For example if component provides data access so we can query some item from DB to cause that component will respond in the desired way (no item found, one item found…). in this case its possible to use DB itself as control point. 
If real component cant be use as control point we have to replace it with one we can control. 

**__Testing Indirect Outputs__**

 we must be able to observe the calls that SUT makes to the API of DOC. It’s also good control the returned value/s. 
In many cases , the test can use the depended-on component as an observation point:
   - asking the file system for the contents of a file that the SUT has written to verify that it exists and was written with the expected content.
    - using DB trigger to notify the test when record is written or deleted. 

There are two basic styles of indirect output verification:

**1.Procedural Behavior verification** - capturing calls to DOC during SUT execution and then comparing them with the expected calls after the SUT has finished executing. 
We replace substitutable dependency with test spy. During execution of the SUT, the Test Spy receives the calls and records them. 
After the Test Method  has finished exercising the SUT, it retrieves the actual calls from the Test Spy and uses Assertion Methods (page X) to compare them with the expected calls.

**2.Expected Behavior** - set up behavior spec during fixture setup phase of the test and then comparing the actual behavior with expected behavior.  This is typically done by mock object. 
During execution of SUT mock obj. receives the calls and compares them to the previously defined expected calls (behavior specification). If mock obj. receives an unexpected call, test fails immediately. 
Test failure show exact location in the SUT where the problem occurred  because of assertion methods are called from mock obj. 

Calls to depended on components often return objects, values or throw exceptions. If we expect exception thrown by DOC its good example of indirect input test condition -> injecting this input is a control point.  

   Test objects
    - in every unit test we usually focus the testing on single subject. This object often requires some collaboration objects (secondary objects) to perform its operations and the correctness of these collaborators may be not be target of the unit test. 
We may need collaborators for two reasons:
 - get desired tested behavior
 - for verification (changing state of collaborators ) 

### Test double:
Acc to Gerard Meszaros book  -> Test Double - is generic term for any kind of pretend object used in place of real object for testing purpose.
 TEST STUB
 replacing real object with test - specific object that feeds the desired indirect inputs into system under test. When called by SUT during test execution, the test   stub returns previous defined values. We can use Test stub as control point that allows us to the behavior of the SUT with various indirect inputs. We can use it also to inject values to allows us pass particular point in the SW.  We use stub if we can verify the indirects outputs.  

There are different kind of test Stub:
- Responder: use to inject valid indirect inputs
- Saboteur : use to inject invalid indirect inputs
- temporary test stub: is used instead of depended-on component (DOC). Often it’s real class with hard-coded return statement. When DOC is available it is used instead.
- procedural test stub:
- entity chain snipping:  variation of responder that is used to replace complex network objects with single test stub that pretends to be a network of objects. 

#### TEST SPY
 - more capable version of test stub 
 -use it to capture the indirect output calls made to another component by SUT for later verification by the test.
 We can use it if:
   - we are verifying the indirect outputs of the SUT and we cannot predict the value of all attributes of the interactions with the SUT ahead of time. 
    - we want assertions and we don’t think that mock object expectations is sufficiently intent-revealing
    - our test requires test-specific equality
    - failed assertion cant be reported effectively back to the Test runner 
———

#### MOCK Object
 - replacing an object the system under test (SUT) depends on with a test-specific object that verifies it is being used correctly by the SUT. 
- way to implement behavior verification while avoiding test code duplication between similar tests by delegating the job of verifying the indirect outputs of the SUT. 
- we don’t need to use state verification but we can use stub or fake object. 
 - we should not use mock object when failed assertion cant be reported effectively back to the test runner. 
 - could be strict (strict mock object fails if the calls are received in different order then was specified when mock obj. was programmed) or lenient (nice - tolerates out-of order calls)
- expected behavior must be specified before the SUT is exercised. The standard four-phase test is altered when we use mock obj.

Fixture setup
*   test constructs Mock Object
*   test configures Mock Object (omitted for hard-coded test doubles)
* test installs Mock Object into SUT
Exercise SUT 
*   SUT  calls Mock object; Mock Object does assertions
Result verification
*   test calls “final verification” method
Fixture tear down 
*    no impact
If expected method calls are never received by the mock object, mock objc can have trouble to detect that test is over and its time to check for unfullfiled expectations. Therefore we need to ensure that the final verification method is called.    
 
####  FAKE Object
   objects have actually working implementations but they have some shortcut , which is not suitable for production.
   replace component that SUT depends on with a much lighter-weight implementation
  much simpler  and lighter weight implementation of the functionality provided by DOC
  provide only functionality that is necessary for SUT.
  similar to test stub
  we can use it when SUT depends on other component that are unavailable or which make test difficult or slow and tests need more complex sequences of behavior than is worth implementing in stub or mock obj. 
if we want to control indirect inputs/ outputs its better to use stub/mock.

We can use it in following approaches:
- Fake DB - replace real DB with f.e. hash table  or better approach is using in-memory db (improve speed of tests). 
- fake web service / fake service layer 

Fake Object is used to replace real implementation that suffers from latency issues due to real messaging or disk i/o with much lighter in memory impl. 
We do not need to configure the Fake object with expectations or return values. 

#### Dummy Objects 
We pass an object that has no implementation as an arg of method called on the SUT.  So, we create an instance of some object that can be instantiated easily and with no dependencies pass it as parameter of the SUT. It will be not used within SUT  we dont need any impl. for this object.  If any method of dummy obj are invoked, test really should throw an error and trying to invoke an non-existing method. 
We can use it if we need to use objects as attributes of other object or args of methods on the SUT. 
If we need to control indirect inputs or verify indirect outputs of the SUT  we should use probably Test Stub or Mock objects.

If the object will be used by SUT we should consider providing Fake object.  Dummy object are not relevant to the test. 
The simplest implementation of dummy object is passing null (this solution is not very descriptive)
Good to use in dynamic-typed languages because null is not checked.
Dummy obj can be null but they aren’t the same as Null Object. Dummy object is not used by SUT so its behavior is irrelevant  while null obj. is used by SUT but is designed to do nothing. 

### TYPE OF TESTS

**black box testing** - also known as Behavioral Testing is software testing method in which the internal structure/design/implementation of the item being tested is not known to the tester. 

This method attempts to find errors in the following categories: 
   - incorrect or missing functions
   - interface errors
  - errors in data structures or external database access
 - Behavior or performance errors
 - Initialization and termination errors

this testing method is applicable to the following levels of SW testing:
- integration testing
- system testing
- acceptance testing

**White Box testing** -  is software testing method in which the internal structure/design/implementation of the item being tested is known to the tester. 
 The tester chooses inputs to exercise paths through the code and determines the appropriate outputs.  This method is called white box because the software program in the eyes of the tester is like white/transparent box inside which one clearly sees.
This method is applicable to the following SW levels:
 - unit testing
 - integration testing
 - system testing 

**Grey box testing** is combination of black/white box testing. Here is internal structure partially known. Tester know internal structure of software partially. 

**Unit testing** - a unit is the smallest testable part of software. It has usually has one or few inputs and usually a single output.  It should be function in procedural programming or method f.e. of some class in OOP programming.  
- its first level testing prior to integration testing
- its normally performed by SW developers but in rare cases it may be also performed by independent SW testers.
- this is primarily useful in integration testing
unit tests follow typical four phase of sequences:
 - setup
 - exercise
 - verify 
 - teardown 

**Integration testing**
 - testing performed to expose defects in the interfaces and interaction between integrated components
 - is perform after unit testing and before system testing
- methods - black box testing, white box testing and grey box testing
- for developers or tester

Approaches: 
- Bing Bang -  most of the units are combined together and tested at one go. This approach is taken when testing team receives the entire SW in a bundle.  
- Top Down - top level units are tested at first and lower units are tested step by step after that. 
- Bottom up  - bottom level units are tested first and upper level units step by step after that. 
- Sandwich/hybrid - combination of top down and bottom up approaches

**Software testing (functional testing)** - complete and integrated software is tested.  The purpose  of this test is to evaluate the system’s compliance with the specified requirements. 
 - the process of testing an integrated system to verify that it meets specified requirements. 

EXAMPLE

During the process of manufacturing a ballpoint pen, the cap, the body, the tail, the ink cartridge and the ballpoint are produced separately and unit tested separately. When two or more units are ready, they are assembled and Integration Testing is performed. When the complete pen is integrated, System Testing is performed

method - usually black box testing method

**Acceptance testing** - system is testing for acceptability. The purpose of the test is to evaluate the system’s compliance with the business req and assess whether it is acceptable for delivery.  
formal testing with respect to user needs, req and business processes conducted to determine whether or not a system satisfies the acceptance criteria and to enable the user, customers to determine or not to accept the system. 

EXAMPLE:

During the process of manufacturing a ballpoint pen, the cap, the body, the tail and clip, the ink cartridge and the ballpoint are produced separately and unit tested separately. When two or more units are ready, they are assembled and Integration Testing is performed. When the complete pen is integrated, System Testing is performed. Once System Testing is complete, Acceptance Testing is performed so as to confirm that the ballpoint pen is ready to be made available to the end-users.

method - black box testing
is performed after system testing and before making the system available for actual use. 

 - **internal acceptance testing** - this test are performance by members of product management sales and or customer support. 
 - **external acceptance testing** - performed by people who are not employees of the organization that developed this software. 


Another testing possibilities

 **Regression testing** - is functional testing used by testers to ensure that new changes in system has not broken any of existing functionality . 


#### TEST DRIVEN DEVELOPMENT (TDD)
1. add new test
each new feature begins with writing test. To write the test, the developer must clearly understand the feature’s specification and requirements. This can be accomplished through use cases and user stories. 
2. run all tests and see if the new test fails
this validates that tests are working correctly. 
3. write the code
write code that cause the test pass. At this point the only purpose of the written code is to pass the test. The programmer must not write code that is beyond the functionality that the test checks. 
4. run the tests
   if all tests cases now pass the programmer can be confident that the new code meets the test requirements and does not break or degrade any existing features. 
5. refractor code
  the growing code base must be cleaned up regularly during test-driven development. Duplication is removed. Object, class, module, variables and method names should clearly represent their current purpose and use as extra functionality is added. By continually re-running the test cases throughout each refactoring phase, the developer can be confident that process is not altering any existing functionality 

**Classical TDD** - use real objects if its possible and double if it’s possible and double if it’s awkward to use real thing. 
you have to not create only SUT (subject under test) but also all collaborators that the SUT needs in response to the test. 
its important to only think about what happens from the external interface and to leave all consideration of impl. until after you are done writing test. 

**Mockist TDD** - using any mocking object. Needs to create the SUT  and mocks for its immediate neighbors. This can avoid some of the involved work in building up complex features. 
writing the tests makes you think about the implementation of the behavior.
mockists are constantly thinking about how the SUT is going to be implemented in order to write the expectations. 

Law of demeter -  principles of least knowledge is design guideline for developing software. 
 is specific case of loose coupling. 
  three rules of LOD - 
    1) each unit should have only limited knowledge about other units 
    2) each unit should only  talks to its - dont talk to strangers
   3) only talk to your immediate friends
- for functions -> methods on specific object may invoke only the methods of the following kinds of objects. 

#### BEHAVIOR DRIVEN DEVELOPMENT (BDD)
 - emerged from TDD (can and should be used together with TDD and unit testing methods)
- assume the use of specialized SW tools to support the development process. 
- heart of BDD is rethinking of the approach to the unit testing and acceptance testing. BDD suggests that unit test names be whole sentences starting a conditional verb (should). Acceptance tests should be written using standard agile framework  “As [role] I want [feature] so that [benefit]”. Acceptance criteria should be written in the form Given [initial context] when [event occurs] then [ensure some outcomes]. 
you should not test implementation but instead behavior. So instead of thinking how the code is implemented we spend a moment of thinking of what the scenario is. 
 - was invented as a technique to better help people learn TDD by focusing on how TDD operates as a design technique. 
BDD is another variation of TDD that tends to use mocking testing. 
it helps with TDD in the way of thinking what an object needs todo. 

Principles of BDD
- define a test set for the unit first
- make the test fail
- then implement unit
- finally verify that the implementation of the unit makes the tests succeed.

Difference between TDD and BDD
 TDD tests are technical tests (mostly unit test) creating during development of feature and are written just before you implement a part of feature. In TDD you ask what is the application doing ?. 

BDD tests are often written before coding starts  and are human readable and anyone should be able to understand what they do. Focus on the behavioral aspect of the system rather than the implementation aspect of the system. In BDD you ask what user can see and do . 

State vs Behavior verification
 - easy collaboration -> classic TTDer -> I dont use a mock, stub or any kind of double. I use real objects and state verification.  If Im mockist TDDer I use a mock and behavior verification. 
 

#### Acceptance test driven development (ATDD)
- verifying work is done by passing acceptance tests. 
- users, testers and developers define automated acceptance criteria early in development process. 
- focuses on capturing requirements in acceptance tests and uses them to drive the development.

The source of truth:
Gerard Meszaros - http://xunitpatterns.com/
Martin Fowler - https://martinfowler.com/articles/mocksArentStubs.html
Wikipedia


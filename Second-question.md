## Project Selected: JSHint
## 3. Test Doubles
- Test doubles are basically placeholder or substitutes used in testing to replace real implementation of a function or module and are extremely useful for isolating a unit of work from the rest of the system. In the two test cases above a Test Double called "Stubs" is used.
- Below are some lines within the two tests functions that use stubs.
- ```JavaScript
  this.sinon.stub(rep, "reporter", function (res) {
      errors = errors.concat(res);
    });
  this.sinon.stub(process, "cwd").returns(dir);
  this.sinon.stub(fsUtils, "readFile")
      .withArgs(sinon.match(/indent\.html$/)).returns(html);
  this.sinon.stub(fsUtils, "exists")
      .withArgs(sinon.match(/indent\.html$/)).returns(true)
      .withArgs(sinon.match(/another\.html$/)).returns(true);
  ```
- Discuss the purpose of using these test doubles in the testing strategy.
- By using stubs, or doubles in general, the tests can focus on the behavior of the unit under test without relying on external dependencies. This ensures that tests can be done in a timely matter and doesn't change any external state of the system. Using stubs also allows for control over the test environment, such as mocking files that have known errors, as doing so without mocking may cause unexpected behavior on the system. Specifically in JSHint, stubs might have been chosen for simplicity, as it doesn't validate how dependencies are called like mocks, but are only concerned with controlling behavior of certain dependencies. Stubs may have also been chosen as it is more concerned with the final state of the test double and not with iteractions that took place to reach the state. Lastly, JSHint is testing using the Sinon.js library, which makes using stubs very straightforward. Thus, using stubs in JSHint allows for developers to have a much more reliable, faster, and focused way of testing command line behavior for JSHint.
# Why We Use TDD

**TDD is all about developers**

TDD is a developer-centric process where unit tests are written before implementation. Developers first write a failing test. Then, they write the simplest implementation to make the test pass. Finally, once the functionality is implemented and working as expected, they can refactor the code and test as needed. The process is repeated as many times as necessary. No piece of code or functionality is written without corresponding tests.

|PROS|CONS|
|:----|:----|
|<ol> <li>Allow the development and testing processes to be done at once</li> <li>Developers analyse requirements in the beginning of each sprint</li> <li>Increased confident of code changes</li> <li>Developers have ownership of their code quality</li> </ol> | <ol> <li> Requires writing more code up-front </li> <li>Requirements need to be elaborated in more detail</li> <li>Test maintenance can be time consuming</li> </ol> |

We can expand on these pros and cons highlights:
- TDD allows the development and testing process to happen at the same time, ensuring that all code is tested from the beginning. While TDD does require writing more code upfront, the written code is immediately covered by tests, and bugs are fixed while relevant code is fresh in developers’ minds. Testing should not be an afterthought and should not be rushed or cut if the implementation is delayed.
- TDD allows developers to analyze project requirements in detail at the beginning of the sprint. While it does require product managers to establish the details of what needs to be built as part of sprint planning, it also allows developers to give early feedback on what can and cannot be implemented during each sprint.
- Well-tested code that has been built with TDD can be confidently shipped and changed. Once a code base has an established test suite, developers can confidently change code, knowing that existing functionality will not be broken because test failures would flag any issues before changes are shipped.
- Finally, the most important pro is that it gives developers ownership of their code quality by making them responsible for both implementation and testing. Writing tests at the same time as code gives developers a short feedback loop on where their code might be faulty, as opposed to shipping a full feature and hearing about where they missed the mark much later.
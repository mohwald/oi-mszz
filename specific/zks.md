Nestihl jsem projít. Otázky mohou být:
- Describe and compare the V and W models of the software testing process. Explain static testing and its role in the W model. Describe individual methods of static testing. 
- Explain the principle of Model Based Testing (MBT) and compare its advantages and disadvantages with manual testing approach. Give some examples of models that can be employed in MBT. How does MBT relate to test automation? 
- Outline the main test automation principle and economics. What are possible levels at which tests can be automated? Give some examples of main approaches and technologies that can be used in software test automation. 
- Explain the equivalence class and boundary values concepts and principle of the Combinatorial Interaction Testing. What is a combinatorial explosion effect, how to effectively reduce the input data combinations? Principle of pairwise (2-way) and N-way testing. 
- Principle of path-based testing. Formal definition of system model and test coverage criteria (node/edge coverage, edge-pair coverage, prime-path coverage). How prioritization of process/workflow activities is modelled and handled in the generation of the test cases?

# Software Quality Assurance

- All parts comply against defined standards.
- Prime goal is to ensure **quality**.
- **External vs Internal quality**
    - Measures how software performs in real-time X quality of code

**Software Testing** = systematic activity to detect defects in a system and to provide a report
about quality of this system

**Defect** (bug)
- James Bach: Anything that threatens quality
- A difference between actual functionality of system and specification (expected functionality)

**Model-based Testing** (MBT)
- As manual: analyze design/docs + ask people
- Create **SUT** (System Under Test) model, then generate tests

**Defect Management approach**
- Ensures external quality
- Aims to prevent defects, detect them, minimize their impact
- Defects are categorized on priorities (severe, critical, low, ...)

**Quality Attributes approach**
- Correctness, Robustness, Readability, Testability, ...
- Ensures a level of quality for customers.
- Attributes are measurable or testable properties.
- **A quality requirement is a specification of the acceptable values of a quality attribute that must be present in the system.**
- Evaluation:
    - Simulation, backtests
    - Mathematical modeling (e.g. checking potential deadlocks)
    - Experience-based assessment
    - *Scenario-based evaluation*: stimulus - environment - artifact - result - response measure; focuses on extreme conditions (security, heavy loads, incorrect inputs, ...)

**Cost of quality**
- Definition by ISTQB: The total costs incurred on quality activities and issues and often split into prevention costs, appraisal costs, internal failure costs and external failure costs.
- Prevention cost + Appraisal cost + Internal Failure cost + External Failure cost


## Test process organization

**V-model**
```markdown
business model          production
 requirements          user acceptance tests    ^client
  functional design   system tests
   technical design  integration tests
    implementation  development tests
    ^               ^
    *Test basis*    *Test levels*
```
Expensive defects caused by wrong requirements are detected at the end of the testing!
W-model solves this with Requirements -> Specification -> Implementation -> Integration/Testing -> Maintenance

## Static Testing

ISTQB definition: Testing of a component or system at specification or implementation level without execution of that software, e.g. reviews or static analysis.

- Takes left place of V(W)-model - it is W actually.
- Makes revisions against previous V layers


## Combinatorial Testing

- Motivation: Exhaustive combinations are large ... combinatorial explosion
- Find a subset of combinations with high probability to detect defects.

**Equivalence class** (EC, Equivalence partition)
- According to the design specification (by which we are testing), the system does the same thing for all values of the equivalence class.
- e.g. look at if conditions.
- Types:
    - Invalid EC from technical view (data do not fit into datatype)
    - Invalid EC from business view (data are invalid from business logic, e.g. age=300)
    - Valid EC

**Boundary value**
ISTQB definition: An input value or output value which is on the edge of an equivalence partition or at the smallest incremental distance on either side of an edge, for example the minimum or maximum value of a range.

**N-way iteractions, uniform strength**
- Tests all n-tuples among all possibilities.
- *2-way coverage* (pairwise testing) is strong (Bures & Ahmed. "On the Effectiveness of Combinatorial Interaction Testing: A Case Study.", 2017)
    - Gives full guarantee that two conditions or one nested if are tested
- Good, when we don’t know anything about the system internal structure, otherwise inefficient.
- Because, we assume that **each parameter interacts with another** somehow.

**Mixed-strength test set**
- Remove pairs, which will hardly interact.


## End-to-end Testing

= technique that tests the entire software product from beginning to end to ensure the application flow behaves as expected.
- Ensures all integrated pieces work together as expected.
- Ensures correctness of application.
- Tests **from the end user’s experience from one end to another end by simulating the real user scenario** and validating the system under test.

```
   UI tests
  Integration tests
 Unit tests
Test data Preparation
```

**Horizontal VS Vertical E2E testing**
- tests navigation through system  X each component of the system
- good for business logic coverage X good for critical safety
- uses UI tests X uses unit & integration tests

### Page Object Model

- Design pattern to create Object Repository for web UI elements.
- Under this model, for each web page in the application, there should be a corresponding page class.
- "Login to a page with these credentials." vs "Type ‘username’ into a text box that can be located using CSS selector ‘#un’."

### Screenplay Pattern

**Behavior Driven Development**
- Business analysts, project managers, users or anyone without technical, but with enough business, knowledge can define tests.
- Given -> When -> Then
```
As James (the regular user)
I want to capture the most important things I need to do
So that I don’t leave so many things until the last minute
```

The pattern:
- **Actors** use **abilities** to perform **tasks** and ask **questions** about state of the system.
- Good:
    - Story focused (BDD)
    - Follows SOLID principle
    - Effective use of layer abstraction
    - Encourages good testing habits
- Bad:
    - Steep learning curve
    - BDD
    - Shift in thinking


## Testable Code

- Aims to make the code easy to tests.
- Antipatterns:
    - SOLID violations
    - non-deterministic code
    - static, global code
    - singletons
    - `new` operator

**Code smells**
- = any characteristic in the source code of a program that possibly indicates a deeper problem.
- Subjective to determine.
- Examples: duplicated code, shotgun surgery (cascading changes), comments, conditional complexity

**SOLID**
- **Single Responsibility Principle**: class should have only one reason to change.
- **Open/closed Principle**: software components should be open for extension, but closed for modification.
- **Liskov Substitution Principle**: derived types must be completely substitutable for their base types.
- **Interface Segregation Principle**: clients should not be forced to implement unnecessary methods which they will not use.
- **Dependency Inversion Principle**: depend on abstractions, not on concretions.

Patterns for testable code:
- **Invertion of Control**: IoC is when you have someone else create objects for you.
- **Dependency Injection**:
    - **Using values (services) produced within the class from new or static methods is prohibited.**
    - The client should accept values passed in from outside.
    - This allows the client to make acquiring dependencies someone elses problem.

**Test doubles**
- = object that can stand in for a real object in a test.
- Types of test doubles:
    - *Dummy* – just a value to make the compiler happy
    - *Fake* – working & functional unit with shortcut making it helpful in testing, like an in-memory database
    - *Stub* – object with hardcoded canned responses (state verification)
    - *Spy* – “records” values during the executions of the test
    - *Mock* – object used for verification that a certain method was called with specific parameters (behavior verification)


## Path-based Testing

- it is graph traversal.
- *Test coverage level*: which ratio of software and its functions is exercised by testing scenarios.

**Test depth level**: 
- Test all combinations of possible node paths.
- Practice:
    - TDL=1 -> Each action (graph edge) exercised once.
    - TDL=2 -> For each decision point (node), all combinations of possible input and output actions are exercised.
    - TDL=3 -> For each decision point (node), all combinations of possible input action and 2 consequent output actions are exercised. **Prime-path coverage is more practical option**.

**Prime-path coverage**
- Each prime path of G is toured (by the test cases)
- **Prime path** = simple path and does not appear as a proper sub-path of any other simple path.


## Exploratory Testing

- Motivation: Test basis is not known.
- Naive: Free testing - depends on individuals initiative
- Definition by James Bach (taken to ISTQB): Parallel learning, test desing and their execution. An informal test design technique where the tester actively controls the design of the tests as those tests are performed and uses information gained while testing to design new and better tests.
- Record, what was tested, create documentation too.
- No proof of test coverage
- Use when:
    - No time
    - No docs
    - Tested system is not critical
    - Testers are experienced or knows tested system

**Error guessing**
- Suprisingly efficient
- Based on:
    - History of development
    - Subjective experience of testers
    - Talks with developers

**Operations profile**
- Distribute test priority to the most used features.
- States of applications are tricky
    - Application can have huge many states.
- Data for operational profile:
    - Report logs
    - Application logs
    - Monitoring on server/user workstation


## Static Code Analysis

- = Method of debugging by examining source code before a program is run.
- Detects programming errors, security weaknesses, standard coding violations and flow issues.

**Static analysis vs Dynamic analysis**
- Identifies defects before X when the program is executed.
- Both can find and miss some defects.

**Code metrics** - set of software measures that provide developers better insight into the code they are developing


## CI/CD = continuous integration, delivery and deployment

**DevOps**
- = a set of practices that works to automate and integrate the processes between software development and IT teams, so they can build, test, and release software faster and more reliably.
- Bridges the gap between *development* and *IT teams* which originally worked separately.
- Handles all these: `Code -> Build -> Integration -> Test -> Release -> Deployment -> Operation`

**Continuous**
- = software changes that roll down the pipeline in a neverstopping and completely constant fashion
- Exceptions: delivery, deployment, integration

**Continuous Integration**
- Handles: `Code -> Build -> Intergration -> Test`
- Goals:
    - Find and fix issues quickly
    - Improve quality of software
    - Decrease time to realease new updates
- Automates code merging with testing small code chunks.
- Provides immediate feedback of code and integration errors.
- CI server needs definitions regarding:
    - Where is the source code repository?
    - How and when is the build triggered?
    - What are the build steps?
- When **trigger** event occurs (commit, scheduled event), CI server **agent** (runner) starts performing **build steps**.
- *Benefits developers.*

**Continuous Delivery**
- Handles: `Code -> Build -> Intergration -> Test -> Release`, approves for Continous Deployment
- = ability to get all changes (new features, bug fixes, etc.) into production to user hands.
- Aims at *safety*, *speed* and *sustainability*.
- "Make deployment predictable again."
- Desired goals:
    - Low risk releases
    - Faster time to market
    - Higher quality (automated tools give time to invest it into high-level testing)
    - Lower costs
    - Better products
    - Happier teams
- *Benefits business users.*

**Continuous Deployment**
- Handles: `Code -> Build -> Intergration -> Test -> Release -> Deployment`
- Automates deployment
- Ensures, new releases are automatically taken from download servers to target users
- *Benefits everybody.*


## Symbolic and Concrete Execution

- Manual Testing - easy, expensive, unable to spot rare defects
- Random Testing - difficult to cover corner cases, lots of tests
- Static Analysis - can produce FPs in large codebases

**Symbolic Execution**
- Generalizes testing
- Inputs *symbolic input values* making the program fail using *automated theorem prover*.
- Properties:
    - No FP
    - Cannot prove absence of errors
- Method:
    - Program -> *Flow Graph*
    - New symbolic input values are generated for each decision node
    - Traversal = evaluation on symbolic input values
    - Path constraint is tested for validity and satisfiability
- Limitations:
    - Path explosion (recursion, unbounded cycles)
    - Memory modelling (array, pointers, heap)
    - Environment iteraction (syscalls)

**Concolic Testing** (concrete/symbolic testing)
- Is run on concrete random inputs parallel
- Generates inputs to other paths than the concrete
- Symbolic input values are replaced with concrete values, which satisfies path condition
    - Can handle syscalls and complex conditions
- Limitations:
    - Can be non-deterministic
    - Poor coverage (both deterministic and non-deterministic)
    - Cryptographic functions cannot be solved in practice

**SMT** (Satisfiability Modulo Theories)
- Problem of determining, if formula in first-order logic is satisfiable.
- E.g. `sin(x)^3 = cos(log(y)*x) or b or -x^2 >= 2.3*y`
- The automated theorem prover needs *solver* to decide this.


## Data Consistency Tests

Motivation
- More sophisticated defects -> buggy data can pass tests, nothing shows up.
- Different function after corrupted function can fail => needs TDL=10

**CRUD** (create, read, update, delete)
- = necessary functions to implement persistent storage application
- Possible defects:
    - Some operations unimplemented
    - Some operations do not work correctly
    - At least one operation executed outside of expected flow
    - D is not delete, but archive...
- **CRUD matrix** - assigns C,R,U,D to operation/entity table, e.g. Edit client details/Client -> R, U
    - Example flaws: C is missing for create operation
    - Flaws do not necessarly mean mistake, but it is worth investigating.
- Use static testing for **completeness test** (all C,R,U,D present in data entity)
- Use dynamic testing for **data consistency test** (check whether function do not corrupt data)
    - *Basic coverage*: C -> {R,U} -> D, pick the most used function
    - *Complete R coverage*: Force R after each U
    - Compromise is needed.
- Types of CRUD matrix:
    0. Code level - Developer - constructed from SUT code by manual or semi-automated analysis
    1. Design - Designer/Architect - analyst uses CRUD matrix created by other-one during design
    2. Test analysis - Test analyst - create CRUD matrix from business or technical spec
    3. Test analysis - Test analyst - We summarize only a list of data entities and SUT functions and we independently propose corresponding Create, Read, Update, Delete operations by test analyst’s domain knowledge.
- Attributes play role - U should be followed by R sharing maximum n of attributes
- There can be hidden attribute corruptions
- Pick best R (best read)
    - By most # attributes sharing with entity
    - By most frequent calls by entity
    - By the most important read function by entity
    - .. Implement own read

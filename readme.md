# Assignment 3 assessment - README first

This IntelliJ project contains the full specification for the assessed stage of Assignment 3, in which you will implement
version 2.0 of the Cygnus store manager.

There are five additional markdown files:
* GeneralRequirements.md: summarises the basic requirements for Cygnus 1.0 which were covered in the warmup.  These are
  collected in one place for ease of reference.  There are also a few important differences which are clearly marked in
  a separate section.
* BookStore.md: summarises the specific details of the book store scenario, which were also covered in the warmup, again for
  ease of reference.
* PetStore.md: summarises specific details of the pet store scenario
* Supermarket.md: summarises specific details of the supermarket scenario
* acknowledgments.md: which we ask that you complete to indicate any assistance or collaboration on this assignment (during
  the warmup phase).  This is not marked, but is helpful for us to understand how students worked during the warmup
  phase and may be helpful for designing future similar assignments.

**Important note:** Part of the specification of assignment 3 that you are to implement is provided by the automarker.  
This is because each student will be randomly assigned additional store clients, one pet store (PawPantry) and one 
supermarket (GreenGrocer), each with different requirements that are different from those assigned to any other student.

You should submit your current solution to the warmup to the assignment 3 automarker on Gradescope as soon as possible
to obtain these additional requirements.  The automarker will also provide feedback on how well your current solution meets
the general requirements specified in the warmup/GeneralRequirements.md.  The automarker will be updated later with 
additional tests that will provide feedback on your specific requirements.

If you have not worked on a solution to the warmup exercises yet, you can just submit an empty project (e.g. this one)
and you will be able to see the additional requirements.


# Submitting

Submission is through GradeScope using the Assignment 3 autograder.

You may upload a zip file created from an IntelliJ project like this one.

You can submit as many times as you want.

The autograder does two important things:

1. Tells you your additional store scenarios to implement and additional validity/cost adjustment rules.
2. Runs tests which tell you whether your solution is correct (at least on the specific tested examples)

Your eventual grade will be entirely determined by the test results.  If all tests pass, your grade for Assignment 3
will be 100%.  However, the feedback you get from the test runs does not tell you everything about the test cases or 
requirements that are being tested, and the "score" produced by the automarker is a count of the tests passed, 
which is not guaranteed to directly map to your final grade.  Passing more tests is always better, but we reserve
the right to adjust the weights of tests to reflect that some requirements are easier or harder to meet than others.

For the autograder to work properly, the following things should be true of your submission in order for it to receive
credit:

* It **MUST** have a main class called `CygnusMain` with a `public static void main(String[] args)` method.  (It is fine 
  for you to create other classes with main methods to accommodate testing or for any other reasons, but we will only
  run the `CygnusMain.main` entry point.
* When called with an empty argument list, your submission **MUST** read inputs from `System.in` and write to `System.out`
  following the specification(s).
  (It is fine to also support reading from a file whose name is provided as a command line argument for your own convenience
  in testing, but the autograder will never do this.)
* You can create whatever additional classes, interfaces and code files you want, but all .java files **MUST** be   
  immediately inside the `src/` directory of the top level directory of the zip file you submit.  Do not put your code 
  into subdirectories of `src` or in the top level.  Do not use `package` either.  
  For clarity, this means than when the zip file is unzipped, your Java files should appear at paths like this:
  ```
  Assignment3/src/CygnusMain.java
  Assignment3/src/Store.java
  ...
  ```
  You can see these paths in the submitted code in Gradescope.  If your java files are at any other path (including
  a root-level `src` directory not inside the `Assignment3` subdirectory), the automarker will not find them and you 
  will get a mysterious error message and no tests will pass.
* Your solution **MUST**  compile using Java 17 and **MUST NOT** assume any features in more recent versions of Java or rely on   
  libraries not part of the standard Java library.  We will compile only your submitted Java code in a sandboxed
  environment without any libraries you might happen to add to the IntelliJ project.
* Your solution **MUST NOT** call `System.exit` to end execution.  Because the testing framework we use loads your code 
  dynamically using reflection and calls your `main` method directly, if your code calls `System.exit` then it kills 
  the autograder too, so you won't get any feedback.  Instead, if your code encounters incorrect input, it should print
  an error message as specified in `GeneralRequirements.md`.

Please also submit a completed acknowledgments.md file with your final submission.
***This is a WIP***

***Please note that this is a WIP until this notice has been removed***

This is a translation of the "The Power of 10: Rules for Developing Safety-Critical Code" by [Gerard J. Holzmann](http://spinroot.com/gerard/) into go.  See also the [wikipedia entry](https://en.wikipedia.org/wiki/The_Power_of_10:_Rules_for_Developing_Safety-Critical_Code).

<!-- TOC depth:6 withLinks:1 updateOnSave:1 orderedList:0 -->

- [The Power of 10 Rules](#the-power-of-10-rules)
	- [Rule 1: "Avoid complex flow constructs, such as goto and recursion."](#rule-1-avoid-complex-flow-constructs-such-as-goto-and-recursion)
	- [Rule 2: "All loops must have fixed bounds.  This prevents runaway code."](#rule-2-all-loops-must-have-fixed-bounds-this-prevents-runaway-code)
	- [Rule 3: "Avoid heap memory allocation."](#rule-3-avoid-heap-memory-allocation)
	- [Rule 4: "Restrict functions to a single printed page."](#rule-4-restrict-functions-to-a-single-printed-page)
	- [Rule 5: "Use a minimum of two runtime assertions per function."](#rule-5-use-a-minimum-of-two-runtime-assertions-per-function)
	- [Rule 6: "Restrict the scope of data to the smallest possible."](#rule-6-restrict-the-scope-of-data-to-the-smallest-possible)
	- [Rule 7: "Check the return value of all non-void functions, or cast to void to indicate the return value is useless."](#rule-7-check-the-return-value-of-all-non-void-functions-or-cast-to-void-to-indicate-the-return-value-is-useless)
	- [Rule 8: "User the preprocessor sparingly."](#rule-8-user-the-preprocessor-sparingly)
	- [Rule 9: "Limit point user to a single dereference, and do not use function pointers."](#rule-9-limit-point-user-to-a-single-dereference-and-do-not-use-function-pointers)
	- [Rule 10: "Complie with all possible warnings active; all warnings should then be addressed before release of the software."](#rule-10-complie-with-all-possible-warnings-active-all-warnings-should-then-be-addressed-before-release-of-the-software)
<!-- /TOC -->


# The Power of 10 Rules

## Rule 1: "Avoid complex flow constructs, such as goto and recursion."

The original paper:
> "Restrict all code to very simple control flow constructs -- do not use goto statements, setjmp or longjmp constructs,
> and direct or indirect recursion."

Go's take on Rule 1:
* Go actually has a [goto statement](http://golang.org/ref/spec#Goto_statements), but using it is unusual.  According to this rule, ***it should actively be avoided***.
* Go supports recursion, but again *by rule* it should be avoided.  [Arden Labs has a good writeup on it](http://www.goinggo.net/2013/09/recursion-and-tail-calls-in-go_26.html) :

> When we say that Go does not optimize for recursion, we are talking about the fact that Go does not attempt to look at our
> recursive functions and find ways to minimize stack growth.


## Rule 2: "All loops must have fixed bounds.  This prevents runaway code."

The original paper:
> "All loops must have a fixed upper-bound. It must be trivially possible for a checking tool to prove
> statically that a preset upper-bound on the number of iterations of a loop cannot be exceeded. If the
> loop-bound cannot be proven statically, the rule is considered violated."

It's possible to write an infinite loop as:
```go
for {
    //no bounds, so we have an infinite loop
}
```
but typically you have a loop bounds expressed:
```go
for i := 1;  i<=5; i++ {
}
```
_So can you get a warning if the upper bounds isn't set?_



## Rule 3: "Avoid heap memory allocation."

The original paper:
> "Do not use dynamic memory allocation after initialization."

There is nothing explict about go that prevents this, but rather I see this as part of the overall philosophy that influences a particular app/architecture/etc.  At least from people I've spoke to previously that worked on console games, this seems to be more common there than in other development areas.  

(need to insert a concrete example)


## Rule 4: "Restrict functions to a single printed page."

The original paper:
> "No function should be longer than what can be printed on a single sheet of paper in a standard reference
> format with one line per statement and one line per declaration. Typically, this means no more than
> about 60 lines of code per function. "


(can any linter check this?)


## Rule 5: "Use a minimum of two runtime assertions per function."

The original paper:
> "The assertion density of the code should average to a minimum of two assertions per function. Assertions
> are used to check for anomalous conditions that should never happen in real-life executions. Assertions
> must always be side-effect free and should be defined as Boolean tests. When an assertion fails, an explicit
> recovery action must be taken, e.g., by returning an error condition to the caller of the
> function that executes the failing assertion. Any assertion for which a static checking
> tool can prove that it can never fail or never hold violates this rule. (I.e., it is not
> possible to satisfy the rule by adding unhelpful “assert(true)” statements.) "

## Rule 6: "Restrict the scope of data to the smallest possible."

The original paper:
> "Data objects must be declared at the smallest possible level of scope. "

## Rule 7: "Check the return value of all non-void functions, or cast to void to indicate the return value is useless."

The oringal paper:
> "The return value of non-void functions must be checked by each calling function, and the validity
> of parameters must be checked inside each function. "

This is idomatic go with the using the built-in error type as one of the values returned from a function.  Go has a specific error type and returning that as part of a return is very normal.  For example (from the golang blog):

```go
f, err := os.Open("filename.ext")
if err != nil {
    log.Fatal(err)
}
// do something with the open *File f
```

Read the [section on error handling](http://blog.golang.org/error-handling-and-go) for a more in-depth discussion on go's error handling capability.


## Rule 8: "User the preprocessor sparingly."

The original paper:
> " The use of the preprocessor must be limited to the inclusion of header files and simple macro
> definitions. Token pasting, variable argument lists (ellipses), and recursive macro calls are
> not allowed. All macros must expand into complete syntactic units. The use of conditional
> compilation directives is often also dubious, but cannot always be avoided. This means that there
> should rarely be justification for more than one or two conditional compilation directives even
> in large software development efforts, beyond the standard boilerplate that avoids multiple inclusion of
> the same header file. Each such use should be flagged by a tool-based checker and justified in the code. "

## Rule 9: "Limit point user to a single dereference, and do not use function pointers."

The original paper:
> "The use of pointers should be restricted. Specifically, no more than one level of
> dereferencing is allowed. Pointer dereference operations may not be hidden in macro
> definitions or inside typedef declarations. Function pointers are not permitted. "

## Rule 10: "Complie with all possible warnings active; all warnings should then be addressed before release of the software."

The original paper:
> "All code must be compiled, from the first day of development, with all compiler warnings
> enabled at the compiler’s most pedantic setting. All code must
> compile with these setting without any warnings. All code must be checked daily with
> at least one, but preferably more than one, state-of-the-art static source code analyzer
> and should pass the analyses with zero warnings."

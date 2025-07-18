CSE 3341 Project 6
Overview
The goal of this project is to write an interpreter for a simple functional language called PLAN. The interpreter itself should be written in Scheme. A PLAN program is a list and defined by the following grammar:
⟨Program⟩  ::= ( planProg ⟨Expr⟩ )
⟨Expr⟩  ::= ⟨Id⟩
| ⟨Const⟩
| ( planIf ⟨Expr⟩ ⟨Expr⟩ ⟨Expr⟩)
| ( planAdd ⟨Expr⟩ ⟨Expr⟩ )
| ( planMul ⟨Expr⟩ ⟨Expr⟩ )
| ( planSub ⟨Expr⟩ ⟨Expr⟩ )
| ( planLet ⟨Id⟩ ⟨Expr⟩ ⟨Expr⟩ )
⟨Id⟩	::= a | b | . . . | z
⟨Const⟩	::= integer constant
As examples, here are five valid PLAN programs
1.	(planProg 5)
2.	(planProg (planAdd (planAdd 7 (planIf (planMul 4 5) 0 10)) (planMul 2 5)))
3.	(planProg (planLet z (planAdd 4 5) (planMul z 2)))
4.	(planProg (planLet a 66 (planAdd (planLet b (planMul 2 4) (planAdd 2 b)) (planMul 2 a))))
5.	(planProg (planLet x 66 (planAdd (planLet x (planMul 2 4) (planAdd 2 x)) (planMul 2 x))))
Each PLAN program and expression evaluates to an integer value. The semantics of a program are defined as follows:
1.	The entire program (planProg ⟨Expr⟩) evaluates to whatever ⟨Expr⟩ evaluates to.
2.	(planIf ⟨Expr⟩ ⟨Expr⟩ ⟨Expr⟩) evaluates the first expression. If this is > 0 then the planIf returns the result of the second expression, otherwise it returns the result of the the third expression.
3.	(planAdd ⟨Expr⟩ ⟨Expr⟩) evaluates to the sum of the values the two expressions evaluate to.
4.	(planMul ⟨Expr⟩ ⟨Expr⟩) evaluates to the product of the values the two expressions evaluate to.
5.	(planSub ⟨Expr⟩ ⟨Expr⟩) evaluates to the difference of the values the two expression evaluate to.
6.	(planLet ⟨Id⟩ ⟨Expr⟩1 ⟨Expr⟩2) has the following semantics. First, ⟨Expr⟩1 is evaluated. The resulting integer value is bound to the identifier ⟨Id⟩. Then ⟨Expr⟩2 is evaluated, and the result of that evaluation serves as the value of the entire planLet expression. The binding between the id and the integer value is active only while ⟨Expr⟩2 is being evaluated.
 
7.	⟨Id⟩ evaluates to the value the identifier has been bound by a planLet expression. PLAN will use dynamic scoping, i.e. if there are multiple bindings for the identifier the most recently executed active binding is used.
8.	⟨Const⟩ evaluates to the value of the integer constant. Based on these rules, the five programs from above evaluate to:
1. 5
2. 17
3. 18
4. 142
5. 142

Implementation
Write a Scheme function called plan that takes as input a single PLAN program and outputs the result of evaluating that program. For example, an invocation
( plan	’ ( plan Prog 5 ) ) should produce the output 5.
Your code must work on the scheme48 interpreter. There can be a great deal of variation in how scheme interpreters work, so please make sure you only use scheme48.
The following instructions that limit what you can do. Please ask on piazza if any of this is at all unclear:
1.	The only built-in Scheme functions you are allowed to use are
ˆ define, equal?, car, cdr, cons, list, append, cond, if, and, or, not, quote, ’, +, -, *, =, <,
>, null?, list?, symbol?, integer?, pair?
ˆ car/cdr variants, like cadr, caar, cddr, and so on
ˆ let and lambda, but I do not recommend these unless you are interested in extending the project beyond what is required
You should not use any other built-in functions without first asking permission.
2.	As you approach this project, make sure you commit fully to the functional programming paradigm; in particular you need to give up on trying to assign to variables or edit data structures.
The following instructions and suggestions are intended to help you and/or simplify your interpreter’s implementation:
1.	Name your file ”myfns.scm” and have a function named “plan” so your code interacts nicely with my scripts.
 
2.	You do not need to write a scanner or a parser, the scheme interpreter automatically handles that for us and we will use the binary tree representation of the input as our parse tree.
3.	Create many functions that each have a simple, clear purpose. You do not have the ability to do sequencing, everything will have to be recursive.
4.	Do not use the PLAN keywords as names your functions, i.e. instead of “(define (planAdd
...” use names like “(define (evalPlanAdd ...”. This will prevent you from making a common mistake I have seen in past semesters, and should help you avoid confusion.
5.	You are guaranteed that the input given to the interpreter will not be empty, and will contain a valid PLAN program. The program will be valid both syntactically and semantically. Syntactically, you can assume that any program given is valid with respect to the grammar from above. Semantically, you can assume that any evaluation of an identifier has at least one existing binding for that identifier. Your implementation does not have to contain error-handling code. Do not worry about arithmetic issues such as underflow or overflow.
6.	First write code to handle PLAN programs with just constants (test cases 1-5). Then add the code for handling binding ids to values (test cases 6-10).
7.	In order to maintain the set of bindings, consider using a list where each element of the list is a specific binding. A binding is just a pair: the symbol and the bound value or expression. You cannot define global variables in scheme, so you will need to pass this list as an additional parameter.
8.	Using (load “FILENAME”) or ,load FILENAME inside the scheme48 interpreter allows you to load a file named FILENAME with your implementation of plan and any other helper functions.


Testing
I have given you two files to help you with your testing:
1.	cases.scm contains 10 test cases, indexed 1-10. Start the scheme interpreter and then load this file into the scheme interpreter with the command ”,load cases.scm”. You can then run each test case individually with the command “(test X)”, where X is an integer 1-10. This will output first the expected output for the test case, and then the output from your interpreter
2.	TestMyfns.scm is a script you can run from the command line (not the scheme interpreter) to run all 10 test cases.

Debugging Scheme Code
My best suggestion for debugging your scheme code is the “display” function, which is essentially like Java’s System.out.print. You can use it like this:
(define	(foo x y)
(display ”In my foo function”) (newline) (display x) (newline)
(display y) (newline) (+ x y)
)

Please note that “display” is not on the list of allowed functions; you should delete or comment it out before submitting your code.
Also, the “display” function returns nonspecific (it has no return value). It is safest to put all your displays before the body of your function.

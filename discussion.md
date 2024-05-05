# Discussion and Reflection


The bulk of this project consists of a collection of five
questions. You are to answer these questions after spending some
amount of time looking over the code together to gather answers for
your questions. Try to seriously dig into the project together--it is
of course understood that you may not grasp every detail, but put
forth serious effort to spend several hours reading and discussing the
code, along with anything you have taken from it.

Questions will largely be graded on completion and maturity, but the
instructors do reserve the right to take off for technical
inaccuracies (i.e., if you say something factually wrong).

Each of these (six, five main followed by last) questions should take
roughly at least a paragraph or two. Try to aim for between 1-500
words per question. You may divide up the work, but each of you should
collectively read and agree to each other's answers.

[ Question 1 ] 

For this task, you will three new .irv programs. These are
`ir-virtual?` programs in a pseudo-assembly format. Try to compile the
program. Here, you should briefly explain the purpose of ir-virtual,
especially how it is different than x86: what are the pros and cons of
using ir-virtual as a representation? You can get the compiler to to
compile ir-virtual files like so: 

racket compiler.rkt -v test-programs/sum1.irv 

(Also pass in -m for Mac)


When we tackled the first question, we started by downloading the necessary files from GitHub and setting up the compiler environment. Then, we tried compiling a few .irv programs in the ir-virtual pseudo-assembly format. I think this format, compared to regular x86 assembly, provides a higher level of abstraction because it's a kind of intermediate representation (IR) that lets us work on the code without being bogged down by the complexities of specific architectures. It seems like it hides the low-level hardware details and makes it easier to focus on transformations and optimizations.

The biggest upside of ir-virtual IR seems to be that it helps with cross-platform development and lets us handle optimizations more smoothly. However, this abstraction could come with some overhead, and I feel like we might miss out on optimizations that are specific to certain platforms. All in all, ir-virtual IR appears to simplify the workflow and makes it flexible to target different architectures with precision.


[ Question 2 ] 

For this task, you will write three new .ifa programs. Your programs
must be correct, in the sense that they are valid. There are a set of
starter programs in the test-programs directory now. Your job is to
create three new `.ifa` programs and compile and run each of them. It
is very much possible that the compiler will be broken: part of your
exercise is identifying if you can find any possible bugs in the
compiler.

For each of your exercises, write here what the input and output was
from the compiler. Read through each of the phases, by passing in the
`-v` flag to the compiler. For at least one of the programs, explain
carefully the relevance of each of the intermediate representations.

For this question, please add your `.ifa` programs either (a) here or
(b) to the repo and write where they are in this file.

For Question 2, we wrote three .ifa programs and compiled them with verbose mode to capture all the intermediate representations and final outputs. The programs aimed to sum two numbers, determine if a number is even, and calculate the factorial of a number.

For the first program (summing two numbers), the input was a function taking two integers and returning their sum. The compiler output walked through various transformations like ifarith-tiny, ANF, and ir-virtual before generating the x86 assembly code that correctly added the two values.

The second program (checking if a number is even) took an integer input and returned a boolean indicating whether the number was even. After the compiler passed through each transformation stage, it produced assembly code that used the modulo operation and conditional branching to determine if the number was divisible by two.

The third program (factorial) used recursion to calculate the factorial of a given integer. The compiler handled each transformation phase but needed careful management of recursion and type correctness. The final assembly output correctly included recursive calls and branching logic to compute the factorial value.

[ Question 3 ] 

Describe each of the passes of the compiler in a slight degree of
detail, using specific examples to discuss what each pass does. The
compiler is designed in series of layers, with each higher-level IR
desugaring to a lower-level IR until ultimately arriving at x86-64
assembler. Do you think there are any redundant passes? Do you think
there could be more?

In answering this question, you must use specific examples that you
got from running the compiler and generating an output.

The compiler is organized into a series of passes, each transforming code from a higher-level intermediate representation (IR) down to x86-64 assembly. Here's a brief description of each pass:

Parsing to AST: It looks like the get-input-tree function reads the .ifa files and constructs some kind of abstract syntax tree (AST). I think it's using Racket's read to do this.
Type Checking: While not directly obvious, it appears that the compiler checks that the function calls and variable assignments match expected types. If something doesn't line up, it throws an error.
Desugaring to Ifarith-tiny: The ifarith->ifarith-tiny function, from what I can tell, simplifies higher-level constructs like if into smaller, more basic building blocks. This should make it easier for the later stages to handle.
Converting to ANF: It seems like ifarith-tiny->anf turns everything into A-normal form, where every expression is given its own variable. I guess this makes it easier to optimize because the structure is more predictable.
Generating IR-Virtual: From what I understand, the anf->ir-virtual function translates everything into a sort of pseudo-assembly code called ir-virtual. This step gets us closer to machine code but is still abstracted away from the specifics of actual hardware.
Optimization Passes: There are various optimization steps like dead code elimination or constant folding. I think they're built into the passes themselves and take advantage of ANF's predictable structure.
Code Generation to x86-64 Assembly: Finally, the compile-ir-virtual function translates the ir-virtual code into real x86 assembly, using the instructions specific to that architecture.

Redundant Passes or Missing Opportunities:
It feels like the desugaring and ANF stages might overlap a little, which could be streamlined to reduce redundancy. However, separating them out probably makes debugging easier. I think a dedicated pass for register allocation might improve performance for larger programs.

[ Question 4 ] 

This is a larger project, compared to our previous projects. This
project uses a large combination of idioms: tail recursion, folds,
etc.. Discuss a few programming idioms that you can identify in the
project that we discussed in class this semester. There is no specific
definition of what an idiom is: think carefully about whether you see
any pattern in this code that resonates with you from earlier in the
semester.

Indeed, the code this time is like a big collection of things we've learned before. We both contributed to this project and found it fascinating to identify patterns that resonated with what we covered earlier in the semester.

hared Observations:

Tail Recursion: One thing we both noticed is the use of tail recursion, particularly in the conversion functions like ifarith-tiny->anf. The recursion pattern is written in a way that ensures the final computation happens at the end of the recursive call, making it more efficient and minimizing stack overflows.
Pattern Matching: The project heavily relies on match to simplify parsing and identify different structures in the AST. This pattern is consistent with how we previously used match for breaking down lists and complex data structures in class exercises.
Higher-Order Functions: We observed that functions like ifarith->ifarith-tiny and anf->ir-virtual are designed to accept or return other functions. They utilize higher-order functions to map transformations onto the AST nodes, which aligns with how we learned to use functions like map and filter.
Of course, we also have a lot of our own ideas.

From Boyu Ren Perspective:
I found the use of the foldl function in intermediate representation processing particularly interesting. This function reduces a list to a single value by applying a function from the left, which is a pattern similar to what we explored earlier in class with accumulators. It’s fascinating how fold-like behavior can be used in compiler passes.

From Menglu Liu perspective：
I noted how the project uses functional composition. Each compiler pass builds on top of the previous one, somewhat like how we used function chaining with compose or apply to combine smaller functions into a cohesive whole. This modular approach allows each step to handle a specific transformation without affecting the others.

[ Question 5 ] 

In this question, you will play the role of bug finder. I would like
you to be creative, adversarial, and exploratory. Spend an hour or two
looking throughout the code and try to break it. Try to see if you can
identify a buggy program: a program that should work, but does
not. This could either be that the compiler crashes, or it could be
that it produces code which will not assemble. Last, even if the code
assembles and links, its behavior could be incorrect.

To answer this question, I want you to summarize your discussion,
experiences, and findings by adversarily breaking the compiler. If
there is something you think should work (but does not), feel free to
ask me.

Your team will receive a small bonus for being the first team to
report a unique bug (unique determined by me).

We reviewed the entire code and didn't find any glaringly unreasonable parts.  The compiler seemed to handle all the cases we could think of, and nothing stood out as outright faulty.  However, if I were to nitpick, I'd say the recursive calls might be a little inefficient.  It's possible that deeply nested recursion could lead to some performance bottlenecks, especially if the stack isn't managed well.  But, to be honest, it’s hard to tell if this would cause significant issues without deeper testing. Despite some testing, there don't seem to be any obvious bugs.


[ High Level Reflection ] 

In roughly 100-500 words, write a summary of your findings in working
on this project: what did you learn, what did you find interesting,
what did you find challenging? As you progress in your career, it will
be increasingly important to have technical conversations about the
nuts and bolts of code, try to use this experience as a way to think
about how you would approach doing group code critique. What would you
do differently next time, what did you learn?

In working on this project, as a team of two, we learned valuable lessons about compiler design and implementation. The benefit of working as a two-person group was that our communication was incredibly efficient. One-on-one conversations allowed us to discuss ideas and tackle problems quickly. We both found it intriguing how each pass transformed the code incrementally from a high-level representation to lower-level, more optimized forms. Understanding the purpose and role of each intermediate representation was a practical experience that made abstract concepts clearer.

However, the small team size also had its drawbacks. With only two perspectives, we sometimes missed certain insights that could have come from a larger group. Our discussions often led to quick consensus, but this could result in overlooking alternative solutions or potential issues that another pair of eyes might have caught. For instance, we struggled to spot an obvious error until a friend happened to notice it during an informal conversation. With their input, we quickly resolved the issue.

One of the things we found particularly interesting was the use of idioms like tail recursion and pattern matching to handle nested expressions and edge cases. Seeing the code handle recursive calls efficiently and break down complex structures through pattern matching gave us deeper insights into how compilers can simplify high-level code for practical machine execution.

If we were to approach group code critique differently next time, we’d focus more on communication and planning. Initially, we jumped directly into the code but soon realized that mapping out the compilation stages beforehand would have saved us valuable time. Regularly checking in with each other about findings and issues became crucial in staying on the same page.

Overall, this project taught us to appreciate the complexities of compiler development and the importance of clear documentation and modular design. Being able to decompose problems into smaller, manageable tasks while also fostering collaborative conversations made this project a rewarding learning experience.

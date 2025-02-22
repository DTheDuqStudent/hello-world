# WHAT IS 4RPL?
As mentioned in README.md, the programming language that will be used for this project is a stack based language called "4rpl" (you can debate about how it's pronounced, I say it like /fɔrpʊl/ or "four pull"). The idea is that we have a stack which is used to store data and perform operations, not to be confused with the Runtime Stack which as you may know keeps track of stuff like function calls, local variables, and return addresses during program execution. NOTE: Some of this information was taken from [the official 4RPL website](https://www.knucklecracker.com/wiki/doku.php?id=4rpl:overview). The reason why this is here is so that you don't constantly have to alt-tab between the repository and another website.
# How it works
When you want to do arithmetic, in a language like c++ or java you'd write out some expression such as 6 + 3 (or to be more specific something like ```protected int foo = 6 + 3;```) This is what we call "infix notation" which is often how we are taught to do math in school. For a stack based language however, it's a little different. We *push* values/operands onto the stack and then apply operations to them, so 6 + 3 becomes ```6 3 add```. Starting off we push 6 onto the stack, and then the 3 so at this point the stack has 2 values on it. The ```add``` operator *pops* 2 values from the stack, in this case adds them together, and then *pushes* the result on the stack, so in this case we'd have a 9 on the stack. This way is called "Reverse Polish Notation" (or RPN for short) and is the key to how 4rpl works!
# RPN vs. Infix
One of the biggest advantages of using RPN is that **you don't need to use parentheses at all, because the order of operations is simply determined by the position of operators in the expression.** Some examples of this are listed below:
- Infix: ```(4 + (5 * (2 + 1)))```
- RPN: ```2 1 add 5 mul 4 add```
- Infix: ```((8 - 4) * (7 + 3))```
- RPN: ```8 4 - 7 3 + *```

If you want to convert between these notations, you can do so by using Edsger Dijkstra's "Shunting-yard" algorithm. You typically DON'T need to do this manually when writing in 4rpl, it's more just helpful to understand the relationship between traditional mathematical notation and how 4RPL expects expressions to be written.
# Your first 4RPL program
As the tradition goes, one of the first programs that a new programmer creates is something that says "Hello World!". To do that in 4RPL, all you'd need to do is `"Hello World!" Trace`

When you run this, the text "Hello World!" (or whatever you change the string to) will appear in the console window. There's 2 ways that 4RPL does value output, `Trace` and `Trace2` up to `Trace5` which pops values from the stack to the console window. The other way is using `Debug` which writes values to a log file (RPL.txt) on your computer. Moving forward, we'll use `Trace` since it provides immediate feedback in the console.
# General Syntax
You may have noticed that in the RPN vs. Infix section the first example used keywords `add`, `mul`, ect. while the second example used `+`, `-`, and `*`. This is because 4RPL allows for both keyword and operator forms to be used interchangeably for certain arithmetic operations. `7 11 add` and `7 11 +` are equivalent as is `13 0 mul` and `13 0 *`, `6 4 div` and `6 4 /`, and so on.
## Comments
To create a comment in 4rpl, just use `#` and continue to the end of the line:
- `#I am a comment!`

Keep in mind that 4RPL does NOT support multi-line comments.
## Whitespace and Commas
- As you might have noticed in the examples, values must be seperated by spaces. `32 add` won't work because `add` pops 2 items on the stack but there's only 1 when it's used (32).
- You may use commas (',') to improve code readability as they get replaced by an empty string internally. If you do this, *make sure that there's a space around the comma!* `4,6` will not work as intended as that's interpreted as a single value (though this is good for if you want to write out a larger value such as `100000000` since that and `100,000,000` are equivalent). `4, 6` or `4 ,6` on the other hand WILL work as you'd expect.
# Data Types
4RPL supports several primitive types, with certain operations doing implicit conversions.
## Primitive Types
- **Integer**: Whole numbers (`3`, `-1`, ect). Appears to support 32-bit values (-2,147,483,648 to 2,147,483,647).
- **Float**: Decimal numbers (`3.14`, `0.2`, ect).
- **String**: Text enclosed with quotes ("Hello there!", "Gilly is best Shih-Tzu!", "Programming is fun").
- **Boolean**: Implicit evalutaion (a value of 0 is `False`, while any non-zero value is `True`).
## Game Specific Types
These are types that are specifically meant for 4RPL scripting.
- **NULL**: Represents an "empty" or absent value. Some functions can return null so you'd need to watch out for that when debugging.
- **UID**: Units in 4RPL have a "Unique Identifier" which can be used for various functions. It's represented as an integer.
- **Vectors**: Sets of 2 (V2), 3 (V3), or 4 (V4) values. These can be used to represent stuff like game coordinates and colors:
```
1 0 0 0.5 V4 ->color #The values are for red, green, blue, and alpha so in this case <-color would represent translucent red.
24 15 55 V3 ->position #A 3D position on a level.
47 0 V2 ->flat_position #A 2D position on a level.
```
## A note on Type Conversion
4RPL can automatically convert strings into numbers *if the string represents a VALID number*.
```
3 "2" add #Pushes 5 on the stack. "2" (string) → 2 (integer).
"1" "-7" sub #Pushes 8 on the stack. "1" (string) → 1 (integer), and "-7" (string) → -7 (integer).
2 "0.6" mul #Pushes 0 on the stack. "0.6" (string) → 0 (integer). Strings do not directly convert into floats.
"Uh oh spaghettio!" 5 div #Pushes 0 on the stack because "Uh oh spaghettio!" cannot be converted into a number, so it evaluates to 0 (equivalent to 0 5 div).
```
# Variables
Variables in 4RPL work differently compared to what you see in Java or C++. Rather than directly assigning values, variables interact with the stack using arrow operators `<-` and `->`. How they work is `->` POPS a value from the stack and stores it in whatever you name the variable, while `<-` PUSHES the value of the variable onto the stack. For example:
- `<-foo 3 add ->sum`

What's happening is `<-foo` pushes the value of foo onto the stack, `3` pushes 3 onto the stack, `add` pops both values, adds them, and pushes the result on the stack, and `->sum` pops the value on the stack and stores it in the variable sum. Here's some more examples both in what you'd typically see in something like Java/C++ and how you'd do the equivalent in 4RPL:
- Traditional: `int c = 7;`
- 4RPL: `7 ->c`
- The stack: [] → [7] → [] because 7 was popped into c.
- Traditional: `y = m * x + b;`
- 4RPL: ```<-m <-x mul <-b add ->y
#Look familiar?```
- The stack: [] → [m] → [m, x] → [(m * x result)] → [(m * x result), b] → [(result of adding (m * x) and b)] → [] because that value was popped into y.

Something to note for variables (and functions when we get there) is that 4RPL is a *case-insensitive* language. If you do something like `4 ->bar` then `<-bar Trace`, `<-BAR Trace`, and `<-BaR Trace` will all output 4.
# Warp Notation
Even though 4RPL uses Reverse Polish Notation, it offers an alternative syntax which is called "warp notation" and involves using `()`. How it works is it moves the command before the opening parenthesis to the closing parenthesis. While this might feel more familiar to programmers coming from traditional languages, keep in mind that it *doesn't* offer any functional benefits over RPN style since 4RPL is fundamentally stack-based:
- RPN: `<-foo <-bar add` OR `<-foo <-bar +`
- Warp: `add(<-foo <-bar)` OR `+(<-foo <-bar)`
- The stack: [] → [foo] → [foo, bar] → [(result of adding foo and bar)]
- RPN: `2 3 add 4 mul` OR `2 3 + 4 *`
- Warp: `mul(4 add(2 3))` OR `*(4 +(2 3))`
- The stack: [] → [2] → [2, 3] → [5] → [5, 4] → [20]

Essentially, parenthesis do NOT change operator precedence unlike more traditional languages. They're merely for "warping" commands to different positions to make code more familiar for those who aren't used to RPN since they don't even affect the execution order (the stack operations remain the same.)
## A warning on using warp notation
Though you may use both RPN and warp notation in tandem, this can make the code considerably harder to follow and maintain even if it's syntactically valid:
- RPN: `<-a <-b add <-c mul`
- Warp: `mul(add(<-a <-b) <-c)`
- What am I looking at: `mul(<-a <-b add <-c) #(Not the hardest to follow but you get the idea.)`

Because of this, it's suggested that you use either RPN or warp notation (preferably RPN since that perfectly matches 4RPL's stack-based nature).
# Functions
4RPL has both built in functions, and the ability for users to define their own. Compared to what you see in java/c++, 4RPL functions are different in a few ways.
## Function Syntax
- To define a custom function, use ':' followed by whatever you want to call it. There are a few built in functions that do this such as `:Once`, `:GameLoaded`, and `:_UICallback`.
- Calling a custom function involves using '@' followed by the function name. Built in functions such as `V2` and `DamageUnit` don't require this.
- 4RPL functions don't use brackets or have an explicit "end" statement, a function ends when another function begins or at the end of file (see example):
```
@Operate
@GoSleep

:Operate
  "I am operational! Or so it seems..." Trace
  #End of Operate because we have another function block coming up.

:GoSleep
  "Alright that's enough for me, nighty night." Trace
  Self StunUnit #Notice that "Self" and "StunUnit" don't include the '@' when calling them, this is because they are built in functions.
  #End of GoSleep because we've reached the end of the file.
```

You'll notice that `Operate` simply prints stuff to the console via `Trace` as does `GoSleep` before calling a `StunUnit` function with the parameter of `Self`. To have a custom function take parameters, all you need to do is use `->` as needed inside of said custom function. To have a function "return" something, you'll want the function to push something on the stack for later use.
```
#A function that both takes a parameter and "returns" a value. Assume that <-foo has a value.
<-foo @IncreaseAge ->new_age

:IncreaseAge
  ->age
  <-age 1 add #Because there's no -> after this, the result of adding 1 to age is left on the stack.
```
## Multiple Parameters⚠️
When making functions that take multiple parameters, keep stack ordering in mind. Because a stack is Last-In-First-Out (LIFO), parameters end up being popped in the *reverse* order of how they were pushed. This may lead to bugs if you're not careful!
```
:Divide
  ->denominator  #This pops the last value that was pushed.
  ->numerator    #This pops the SECOND TO LAST value that was pushed.
  <-numerator <-denominator div
  #Yes 4rpl does already have division built in, this is more to get the point across.
```
To avoid this, consider documenting the expected stack order in comments.
# Data Structures
There may be times where you'll need to organize a lot of values. To handle that in 4RPL, you can use Lists and Tables.
## Lists
Lists are basically dynamically sized arrays in 4RPL. There are functions that return these so to mess with them, some of the options you have are:
- `CreateList`: Pushes an empty list on the stack.
- `AppendToList`: Adds a value to the END of a list.
- `GetListCount`: Pushes the number of entries in a list on the stack.
- `PopList`: Removes AND pushes the value from the END of the list on the stack. WARNING: IF THE LIST IS EMPTY THEN A **NULL** IS PUSHED ON THE STACK INSTEAD.
- `GetListElement`: Pushes the value stored at the specified index of the list on the stack. Keep in mind, lists are 0 indexed.
- `SetListElementRPN`: Pops a value from the stack and stores it at the specified list index (the previous value is overwritten.)
- `RemoveListElement`: Removes the element at the specified index of the list. Elements AFTER the removed element are shifted towards the START of the list.
- `ClearList`: Pops a list from the stack and removes all the entries from it.
- `ListToStack`: Pushes all of the elements from the list onto the stack in the order they appear in said list.
- Example:
```
CreateList ->names
"Wilbur" <-names 0 SetListElementRPN #Best English Bulldog, may he rest in peace.
<-names "Gilly" AppendToList
<-names "Cozy Kirin" AppendToList
<-names ListToStack Trace3
#The output will be "Wilbur, Gilly, Cozy Kirin".
```
## Tables
Tables are useful for organizing structured data (it basically works like a hash map). You might for example want a template for defining game objects such as enemy types, projectiles, and towers. With a table, you can easily do that:
- `CreateTable`: Pushes an empty table on the stack.
- `GetTableKeys`: Pushes a list containing all of the keys from the table on the stack. Keys are represented by strings.
- `GetTableValues`: Pushes a list containing all of the values from the table on the stack. Values can be whatever you want (numbers, strings, vectors, ect.)
- `SetTableElementRPN`: Pops a value, table, and key (string) to set an entry.
  - WARNING: Table keys are actually **CASE SENSITIVE.** "Health" and "HEALTH" will NOT give the same value.
- `RemoveTableElement`: Removes an element from the table given a key.
- `ClearTable` Pops a table from the stack and removes all the entries from it.
- Example:
```
CreateTable ->attributes
2 <-attributes "Health" SetTableElementRPN
3 <-attributes "Eyeballs" SetTableElementRPN
0.15 <-attributes "HealRate" SetTableElementRPN
True <-attributes "AttacksCozyKirin" SetTableElementRPN
#CreateUnit is how we spawn game objects in 4RPL which takes 3 parameters:
#ID (a string, determines WHAT we even are spawning), a vector (where it will spawn), and a table (values that will be set on the newly created unit).
36 20 17 V3 ->position
"angryPetOwner" <-position <-attributes CreateUnit ->angry_pet_owner
```
# Control Flow
When it comes to programming logic, control flow is very important, especially in a tower defence game where you'll need to constantly be checking stuff like if an enemy target is in range. Like most languages, 4RPL has your standard `if` blocks which work about how you may expect them to operate. The main idea that that we pop what's on the stack and evaluate it as either `True` or `False`, from there determine what to execute. Since we don't use brackets, to define the end of an if statement you must use `endif`, similar to Visual Basic.
```
#Basic example of how an if statement works. We push foo's value on the stack, and evaluate it as either true or false.
<-foo if
  #<-foo has been evaluated as true, do something.
else
  #<-foo has been evaluated as false, do something else.
endif
```
```
#A more "practical" game example. You can nest if statements no problem.
<-fire_counter 0 eq if
  #Fire counter is 0, see if we have enough ammo to fire.
  <-ammo <-firing_cost gte if
    #We have sufficient ammo. Find a target to fire at.
    @FindTarget
  endif
else
  #Still on firing cooldown.
  <-fire_counter 1 sub ->fire_counter
endif
```
## Logic and Comparators
You can use all the standard comparators such as `lt`/`<`, `gt`/`>`, `eq`/`==`, `neq`/`!=`, `lte`/`<=`, `gte`/`>=` for conditional stuffs, there are even dedicated comparators for 0!
```
eq0 #Essentially a convenient way to do 0 eq/0 ==
neq0 #0 neq/0 !=
lt0 #0 lt/0 <
lte0 #0 lte/0 <=
gt0 #0 gt/0 >
gte0 #0 gte/0 >=
```
Logic operators also work how you may imagine they would, those operators being `and`/`&&`, `or`/`||`, `not`/`!`, and `xor`. `and`, `or`, and `xor` all pop 2 values from the stack, do the appropriate operation and push either True or False back to the stack. `not` as you may guess only pops 1 value from the stack.
```
#Basic example to determine if a tower should operate using not.
<-is_stunned not if
  @Operate
endif
```
```
#Example of how we might set up a win condition of sorts.
<-elapsed_time <-survival_time gte <-gilly_health gt0 and if
  @CelebrateVictory
endif
```
## Switch statements
You might have heard of the gag (or depending on who you ask, horrors) about programmers using a bunch of `if`/`else` chains to handle logic. If that's something you want to avoid, then you're in luck as 4rpl has a switch statement for this kind of situation! To create a switch statement, you'll need both `switch` and `endswitch`. Cases are defined by using `case` and `endcase` and unlike what c++ and java do, the 4rpl case is a lot more flexible. The only something that needs to happen is that something must be pushed onto the stack which the case can evaluate as true or false. Unlike C++ and Java where you would need to use `break` statements to prevent fall-through, fall-through is actually impossible in 4RPL because each `case` must be explicitly terminated with `endcase`. This makes the code both cleaner and safer!
```
#Example of a switch statement that would behave like a c++ or java switch statement.
#This determines what sort of enemy we will create.
#Assume that <-type has been given a numerical value.
switch
  <-type lte0 case
    @SpawnAngryPetOwner
  endcase
  <-type 1 eq case
    @SpawnGiantFloatingNose
  endcase
  <-type 2 eq case
    @SpawnBlob
  endcase
  <-type 3 gte case
    @SpawnCodeHater
  endcase
endswitch
```
```
#This example shows the flexibility of case statements.
switch
    <-enemy_health <-enemy_max_health 0.5 mul lt case
        #Enemy is below 50% health.
        @EnterBerserkMode
    endcase
    <-distance_to_gilly 100 lt <-has_ranged_attack and case
        #Enemy is close to Gilly and has ranged attack.
        @UseRangedAttack
    endcase
endswitch
```
Interestingly enough, you may have code in between cases. See the following example for how that works.
```
#Like other languages, once a case is evaluated as true, the execution jumps to after endswitch
switch
    <-is_angry case
        @Rage        #If is_angry is true, only this runs
    endcase
    #This code ONLY runs if the above case didn't match
    True ->has_hat
    <-has_hat case
        @HatTrick    #If has_hat is true, only this runs
    endcase
    #This code ONLY runs if NO cases matched, but since has_hat was assigned to be True, then this will never get executed.
    @Default
endswitch
```
If you're wondering about performace, `switch` is faster than `if`/`else` chains if you have more than 2 cases. So this
```
switch
    <-type 1 eq case
        @HandleType1
    endcase
    <-type 2 eq case
        @HandleType2
    endcase
    <-type 3 eq case
        @HandleType3
    endcase
endswitch
```
is faster than this.
```
<-type 1 eq if
    @HandleType1
else
    <-type 2 eq if
        @HandleType2
    else
        <-type 3 eq if
            @HandleType3
        endif
    endif
endif
```
## One Time Execution
In the context of the engine (which runs at 30 UPS or updates/frames per second), 4RPL scripts will execute every frame. That's needed for stuff like making enemies move towards targets, but sometimes you'd only want something to run only ONCE (such as initializing attributes like health or triggering special events). You can of course do this using an if statement and flag variable, but there's 2 more convenient ways to do that.
```
#A once block can be used to define code that ONLY runs once as you'd expect.
once
  0 ->heal_rate
  2 ->health
  0.3 ->speed
endonce
```
For an even simpler approach you can use the built in once function like so:
```
:Once
  #A simpler way to initialize attributes
  0 ->heal_rate
  2 ->health
  0.3 ->speed
```
Some other notes:
- Once blocks can NOT be reset in any way. If you want a resettable block of code, then you'll need to use an if statement with the flag variable inside.
- You CAN nest once blocks inside of each other, though I have no idea why you'd need to do that.

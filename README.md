# Fail language

Language based on lox and tweaked from book "Crafting Interpreters" by @munificent.

## Properties
- regular grammar
- recursive descent parser

## Keywords
### Constructs
- if, else
- for, while, do-while (break, continue)
- fun
- class (this, super)

### Data types
- var
- Boolean: true, false, and, or
- none

### Misc
- print

## Grammar
Statements:
   
    program      → declaration* EOF ;
    declaration  → funDecl
                 | varDecl
                 | statement ;
    funDecl      → "fun" function ;
    function     → IDENTIFIER "(" parameters? ")" block ;
    parameters → IDENTIFIER ( "," IDENTIFIER )* ;
    varDecl      → "var" IDENTIFIER ( "=" expression )? ";" ;
    statement    → exprStmt
                 | forStmt
                 | ifStmt
                 | printStmt
                 | returnStmt
                 | whileStmt
                 | block;
    exprStmt     → expression ";" ;
    forStmt      → "for" "(" ( varDecl | exprStmt | ";" )
                             expression? ";"
                             expression? ")" statement ;
    ifStmt       → "if" "(" expression ")" statement ( "else" statement )? ;
    printStmt    → "print" expression ";" ;
    doWhileStmt  → "do" statement "while" "(" expression ")" ";" ;
    whileStmt    → "while" "(" expression ")" statement ;
    breakStmt    → "break" ";" ;
    continueStmt → "continue" ";" ;
    block        → "{" declaration* "}" ;

Expressions:

    expression  → comma ;
    comma       → assignment ( "," assignment )*
    assignment  → identifier ( ( "=" | "+=" | "-=" | "*=" | "/=" | "**=" ) assignment )?
                | ternary ;
    ternary     → logic_or ( "?" expression ":" ternary )?
    logic_or    → logic_and ( "or" logic_and )*
    logic_and   → equality ( "and" equality )*
    equality    → comparison ( ( "!=" | "==" ) comparison )*
    comparison  → term ( ( ">" | ">=" | "<" | "<=" ) term )*
    term        → factor ( ( "-" | "+" ) factor )*
    factor      → exponent ( ( "/" | "*" ) exponent )*
    exponent    → unary ( ( "**" ) unary )*
    unary       → ( "!" | "-" | "++" | "--" ) unary
                | postfix ;
    postfix     → primary ( "++" | "--" )* | call ;
    call        → primary ( "(" arguments? ")" )* ;
    primary     → NUMBER | STRING | "true" | "false" | "none"
                | IDENTIFIER
                | "(" expression ")"
                // Error productions...
                | ( "!=" | "==" ) equality
                | ( ">" | ">=" | "<" | "<=" ) comparison
                | ( "+" ) term
                | ( "/" | "*" ) factor
                | ("**") exponent;
                
Other:

    arguments   → expression ( "," expression )* ;
               
### Notes
Unary '+' operator is not supported.

Currently continue statement does not work as expected for for loops, incrementors must be manually incremented.

## Rules
### Operator precedence (highest → lowest)

    Name	      Operators	               Associates
    Call          a()                      Left
    Postfix       a++ a--                  Left
    Unary	      ! - ++a --a              Right
    Exponent      **                       Left
    Factor	      / *                      Left
    Term	      - +                      Left
    Comparison    > >= < <=	               Left
    Equality      == !=                    Left
    Logical And   and                      Left
    Logical Or    or                       Left
    Ternary       ?:                       Right
    Assignment    =, +=, -=, /=, *=, **=   Right
    Comma         ,                        Left

### Truthyness
Fail follows Ruby’s simple rule: false and none are falsey and everything else is truthy.

## Escape sequences
    \" – double quote
    \\ – single backslash
    \b – backspace
    \r – carriage return
    \n – newline
    \t – tab

## Added features
Additional features mostly based on tasks from book:
- multiline comments
- postfix and prefix increment/decrement operators
- ternary operator
- exponent operator
- prevent access to unassigned variables (no implicit initialization to none)
~~- changed the order of var assignment (first definition of variable with none and then assignment)
  to prevent accessing variable from outer scope during initializing~~ (will be fixed at later stage)
- accept escape sequences
- added comma operator
- added operator overload for string multiplication ("abc" * 2 → "abcabc")
- added break and continue
- added do-while statement
- added shorthand assignment operators +=, -=, *=, /=, **=
- added prevention of assignment inside if, loop and ternary condition expressions (currently as runtime exception, considering doing that at parse time)
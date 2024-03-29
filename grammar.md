Copyright (c) 2015-2021, the Selfie Project authors. All rights reserved. Please see the AUTHORS file for details. Use of this source code is governed by a BSD license that can be found in the LICENSE file.

Selfie is a project of the Computational Systems Group at the Department of Computer Sciences of the University of Salzburg in Austria. For further information and code please refer to:

http://selfie.cs.uni-salzburg.at

This is the grammar of the C Star (C\*) programming language.

C\* is a tiny subset of the programming language C. C\* features global variable declarations with optional initialization as well as procedures with parameters and local variables. C\* has five statements (assignment, while loop, if-then-else, procedure call, and return) and standard arithmetic (`+`, `-`, `*`, `/`, `%`) and comparison (`==`, `!=`, `<`, `<=`, `>`, `>=`) operators. C\* includes the unary `*` operator for dereferencing pointers hence the name but excludes data types other than `uint64_t` and `uint64_t*`, bitwise and Boolean operators, and many other features. The C\* grammar is LL(1) with 6 keywords and 22 symbols. Whitespace as well as single-line (`//`) and multi-line (`/*` to `*/`) comments are ignored.

C\* Keywords: `uint64_t`, `void`, `if`, `else`, `while`, `return`, `struct`, `for`

C\* Symbols: `integer_literal`, `character_literal`, `string_literal`, `identifier`, `hex_literal`, `,`, `;`, `(`, `)`, `[`, `]`, `{`, `}`, `+`, `-`, `*`, `/`, `%`, `=`, `==`, `!=`, `<`, `<=`, `>`, `>=`, `&`, `|`, `~`,

with:

```
integer_literal   = digit { digit } .

character_literal = "'" printable_character "'" .

string_literal    = """ { printable_character } """ .

identifier        = letter { letter | digit | "_" } .

hex_literal       = "0x" hex_letter { hex_letter } .
```

and:

```
digit  = "0" | ... | "9" .

letter = "a" | ... | "z" | "A" | ... | "Z" .

hex_letter = "a" | ... | "f" | "A" | ... | "F" | digit .

logical_operator = "&" | "|" | "~" .
```

C\* Grammar:

```
cstar             = { type identifier
                      [ "=" [ cast ] [ "-" ] ( integer_literal | character_literal ) ] ";" |
                      type identifier "[" integer_literal "]" { "[" integer_literal "]" } |
                      "struct" identifier struct |
                      "struct" identifier "*" identifier ";" |
                    ( "void" | type ) identifier procedure } .

type              = "uint64_t" [ "*" ] .

cast              = "(" type ")" .

struct            = "{" { variable ";" } "}" ";" .

procedure         = "(" [ variable { "," variable } ] ")" ( ";" |
                    "{" { variable ";" } { statement } "}" ) .

variable          = type identifier [ "[" integer_literal "]" { "[" integer_literal "]" } ] |
                    "struct" identifier "*" identifier .

statement         = ( [ "*" ] identifier [ "[" expression "]" { "[" expression "]" } ] |
                    identifier [ "->" identifier { "->" identifier } ] | 
                    "*" "(" expression ")" ) "=" expression ";" |
                    call ";" | while | if | return ";" .

statement_var     = ( [ "*" ] identifier [ "[" expression "]" { "[" expression "]" } ] |
                    identifier [ "->" identifier { "->" identifier } ] | 
                    "*" "(" expression ")" ) "=" expression |
                    call | return . 

call              = identifier "(" [ expression { "," expression } ] ")" .

expression        = and_expression { "|" and_expression } .

and_expression    = expression { "&" expression } .

compare_expression = shift_expression
                    [ ( "==" | "!=" | "<" | ">" | "<=" | ">=" ) shift_expression ] .

shift_expression  = simple_expression { ("<<" | ">>") simple_expression } .

simple_expression = term { ( "+" | "-" ) term } .

term              = factor { ( "*" | "/" | "%" ) factor } .

factor            = [ "~" ] [ cast ] [ "-" ] [ "*" ]
                    ( integer_literal | character_literal | string_literal |
                      identifier [ "[" expression "]" { "[" expression "]" } ] |
                      identifier [ "->" identifier { "->" identifier } ] | call | "(" expression ")" ) .

while             = "while" "(" expression ")"
                      ( statement | "{" { statement } "}" ) .

for               = "for" "(" statement_var ";" expression ";" statement_var ")"
                      ( statement | "{" { statement } "}" ) .

if                = "if" "(" expression ")"
                      ( statement | "{" { statement } "}" )
                    [ "else"
                      ( statement | "{" { statement } "}" ) ] .

return            = "return" [ expression ] .
```


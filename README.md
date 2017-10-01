**NOTE:** This is NOT the official Lua distribution. This is a fork of Lua that
implements non-standard extensions to the language. The official Lua website is
[http://www.lua.org](http://www.lua.org).

# Lambda Expressions

Base version: `lua-5.3.4`

### Summary

Modifies the Lua lexer and parser to support lambda expressions as a shorthand
for anonymous functions. They may be used anywhere an expression is expected.
Formally, this patch modifies the language specification (section 9 of the
Lua reference manual) as follows:

```
exp ::= nil | false | true | Numeral | LiteralString | ‘...’ | functiondef |
        lambdaexp | prefixexp | tableconstructor | exp binop exp | unop exp 

lambdaexp ::= '@(' [parlist] '->' exp ')'
```

### Usage

```lua
-- declaration:
local id = @(x -> x)
assert(id(5) == 5)

local mul = @(x, y -> x * y)
assert(mul(2, 3) == 6)

-- as a parameter to a function:
local s = ('abc'):gsub('b', @(-> 'z'))
assert(s == 'azc')

-- nested lambdas:
local compose = @(f, g -> @(... -> f(g(...))))
local add_two_then_mul_five = compose(@(x -> x * 5), @(x -> x + 2))
assert(add_two_then_mul_five(10) == 60)

-- variable capture:
local function f(x)
  return @(-> x)
end
assert(f(123)() == 123)

-- simple function call syntax:
seq.new    {1, 2, 3, 4, 5}
   :map    @(x -> x ^ 2)
   :filter @(x -> x > 5)
   :iter   (print)
-- prints 9.0 16.0 25.0 for a reasonable implementation of seq
```


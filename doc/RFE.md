# Dotlang - Requests for Enhancement

? - name: simple, pure, simpla (simple + language), func, 
Lisp - list processing
electron is good but a bit long
photon? This is good. but is name of a game engine.
This is mostly influenced by go and c/c++ and scala. 
Let's name it after C. Like "microc".
betterc
main features:
- immutability: Haskell
- type system: C, C#
- generics: C++
- first-class function: Go
Go#?
potent
brute
jumpstate - Simple and powerful state management for Redux.
dotlang.org is free, dot is a short and simple word and a dot is a simple notation which is also powerful because it is used everywhere.


N - A notation to join two arrays.
In Haskell we have:
```
qsort (p:xs) = qsort lesser ++ [p] ++ qsort greater
    where lesser  = filter (< p) xs
          greater = filter (>= p) xs
```
It uses `++` notation to merge two arrays. 
Why can't we use a `join` function? 
`func join(x: any[], y:any[]) -> any[]`
We can. 
This is approch of Go.

N - How to dump an object? core.

N - Example for DB and fake DB:
```
type DBInfo := ...
func select(db: DBInfo, s: table) ->
type FakeDBInfo := @DBInfo...
func select(db: FakeDBInfo, s: table) -> ...
```
Can we use this to fake a function?
```
func secondary() {
}
func primary(x: int) {
  secondary()
}
...
;test code - I want to primary call my secondary instead of default
;if secondary has some input which depends on primary's input, this can be achieved with subtyping.
primary(10)
```

N - Study Monad. Can we really live without them? yes I think we can.

N - Real-world use case: Connect to database.
How can we fetch result of a SELECT? 2D array
How can we create a dynamic query? string interpolation
How can we model INSERT - hash
e.g. building a tuple at runtime based on result of select? no.
```
func queryDatabase(db: DBInfo, sql: string) -> cursor
func queryDatabase(db: DBInfo, sql: string) -> (fieldNames: string[], data: any[,])
...
var (fields, data) = queryDatabase(db, "SELECT * from Employees")
;type of data is a 2D array
var firstField: any = any[0][0]
```

N - RWUC (real-world use case) - How can I read a json file?
```
func parseJson(x: string) -> any
type Json := (name: string, value: Json | int | ...)
```

N - `LIKE` operator -> regex

N - To redirect:
```
func process(x:int, y:int, z:int) -> ...
func process(x:int, y:int) -> process(@$, 10)
```
We can apply `@` on `$` too!

Y - Regex operator.
This should be done in core functions, but as a shortcut:
```
x = ( data ~ '^hello' )
```
x will be an array containing all matches. You can use groups for matching.

N - Shall we add `in` operator? Check if data x is inside array.
we can define `find` function.
`func find(x: any[], data: any) -> bool`

N - review naming conventions
- We want distinct names for function, type and data.
- Options: `some_name`, `SomeName`, `someName`
current: `someFunctionName`, `my_var_name`, `SomeType`, `my_package_or_module`

N - Should `any` cover `none`?
I don't think so. So if `map` expects a fn whose output is `any` we cannot pass a lambda which returns nothing. 

Y - if hash does not have a key, `ht[key]` will return `none`.
But this does not make sense: `doWork(ht['non-exist'])` what will happen at runtime if `doWork` expects an int?
`if ( ht['noway'] :: none )` if key is not in hashtable.

Y - shall we use functions behind syntax for array and hash?
So developer can customize `arr[0]` or `ht["data"]` for his own types?
```
func queryValue(x: any[], y:int)->any
func queryValue(x: any=>any, k: any) -> any
func queryKeys(x: any=>any) -> AnyIterator
```
We won't let developer customize everything (`+`, `-`, ... or other custom operators).
But what can be application of this?
We can define `+` for tuples.
problem: `x+y` maybe at runtime type of x or y is not int and we won't know what function to call at compile time.
But primitives cannot be subtypes. So if type of x/y is `int` then definitely native add should be done.
`i = j * 5` is same as `i = mult(j, 5)` 
If you see them and don't know the meaning, you have to take a look at the code.
Either the library developer should provide doc about operator meaning or you should check the code.
So if PL does not support operator overloading, this does not solve "code readabiity" problem.
The only issue with operator overloading is "context". We have a previous understanding of `*` and expect a certain behavior.
If the library developer, has mis-used it, then we will get confused. But with method naming, at least there is a name which can be more descriptive than `*`. But shall we make decisions instead of developers? Why not let them make their own decisions?
The problem with operators can be exactly repeated for functions.
List of operators: `[]`, `+-*/`, `==`,`!=`, `++`, `--`, comparison, slice.
It should be minimum: 
- `opIndex` for `[]` reading and writing and slice for array and hash
- `opMath` for `+-*/** %`
- `opCompare` for `==, !=, <=, ...`
- `opIterate` for `++, --`
So if a type implements `+` it has to implement all math operators.
And this is only possible for non-primitive types (but you can implement for an alias).
And note that operators cannot mutate their input so all of them will return the result.
Advantage: We can define `type string := char[]` and overload operators for it. 
So string is not a primitive anymore.
result: string assignment is a reference assignment. to clone use `@`: `var str2 = [@str1]`
user can define his own data types which behave like an array.
what about slice? same as opIndex but with two int arguments.
hash-table syntax is just a function in core.
Because of immutability, you cannot override `=` operator. 
`func opAssign(x: Customer) -> Customer { return x }`
`func opAssign(x: Customer) -> Customer { return @x }`
Does not make sense.

Y - Maybe by using type alias we can stress that some types must be equal in a function.
```
type T
func add(x: T[], data: T)-> T    ;input must be an array and single var of the same type and same as output
add(int_array, "A") will fail
```

N - Cant we unify array and hash?

Y - what if we define string as `char[]` and override `+` operator for that? and len?

Y - Loading code on the fly.
`func loadCode(file: string)`
How can we define type of a general function? `func(any)->any`
just mention in the core functionality

N - How can we define a function that can accept anything and return anything?
How can we define a function that accepts only one input and returns anything?
`type Function := func(any)->any`
`type SingleFunc := func(i:any)->any`
what does this mean?
`type SingleFunc := func(any,any)->any`
Why do we need to define a function that can accept "Anything in any number" and return anything?
even when we load a plugin at runtime, the input/output must be specified.
Or in map function.

Y - Remove extra operators overridable.
- OpIterate
- opMath
- opCompare we really dont need to compare. we just call some sort methdo with a lambda.
We also need to be able to overload `~` operator so we can have regex for strings.
we need `~` and something to concat. 
users should be able to customize this. 

N - unify function syntax
`type fadder := int -> int -> int`
`type fadder := func(int,int)->int`

Y - Why not provide regex as a function? Then we don't need the silly operator `~` for all types.

Y - To prevent confusion with numbers, shall we change add/remove operators?
`++` `--` as binary opertors
main use: for string.
we can provide `+` as a built-in operator that works on numbers and arrays.
It join arrays together.
Then we can eliminate `opAdd` and `opRemove` operator functions.

Y - Find a good name.
Notron
light
hotspot
Electron
spot
go/d+lang
something+lang
idlang
dotlang - it is simplest shape and also very powerful in english language and our language.

Y - can `f(1,2,3)` be redirected to `func f(x:int, y:int)`? 
According to subtyping, it can. `(1,2,3)` is like a Circle and `(x:int, y:int)` is like definition of a shape.
But this will be too confusing. 
Putting any rule against subtyping, will be agains gen but I think we have to.
First: function input is not a tuple, it is a series of arguments. So subtype and inheritance is not applied to the input list itself. Of course caller can use explode to prepare this list.
Second: So a call to `f(1,2,3)` have to call a function with name `f` and three input arguments. No less and no more.

N - If `A` has `x:int` and `B` has `x:int` then type `C` can inherit from both of them!
And it can be used as any of these two types.

N - Type hierarchy is created based on data and type names.
So `StackElement` is a different type from `any`. Although data-wise they are the same.

N - Can we provide a mechanism to organize code better?
How can a developer find a function?
Maybe we can force the single space: `func add` so if user want to find a function, he only needs to search for a specific pattern.
Maybe we can force a specific filename based on the types involved in functions.
Module is enough.

N - Can we make defining empty types easier?
`type Stack := %StackElement[]`
Empty types are defined using `%` prefix.

Y - Can we have `Circle | Shape` type?
No. Because they overlap. This is essentially `Shape`.
Overlap is not a problem, one type being subset of the other, is the problem.

N - When we define `type MaybeInt := Nothing | int` we are defining a new type! Aren't we?
Can we use this notation to provide some kind of generics?
`type MaybeType`
`type Maybe := None | MaybeType`
`func process() -> Maybe with { MaybeType := int }`?
`type VectorElement`
`type Vector := VectorElement[]`
`type IntVector := Vector with { VectorElement := int }`
`func append(x: Vector, y: VectorElement)->Vector` type of y is VectorElement.
No. No generics. It is tempting to say there should be a relation between function inputs but this should not be dealt with at syntax level.
You can put assertions inside function body to make sure appropriate arguments are passed to the function.

Y - Clarify about sum types. Is everything around `|` a type?
Even `None`? We can consider them as a simple type which has a single possible value which is the same.
Shouldn't we isolate them from other symbols? No. After all, they are just types. 
Special types which have single value.

N - Clarify more about types used in collections which mimic generics.

N - focus on performance issues.

N- Like in julia we can have dispatch checked with type:
`func work(x: T, y: T)-> ...` this will be invoked if type fo x and y are the same.
In Julia we can have: `myappend{T}(v::Vector{T}, x::T) = [v..., x]`
```
type VectorElement
type Vector := VectorElement[]
func append(x: Vector, y: VectorElement)->Vector
```
append definition should know that type of `y` is related to type of `x`. So if it is receiving a:
`type IntVector := int[]` - It should be aware that `VectorElement` is now `int`.

N - Assign values from an array of `any` to a tuple.
`var customer = data_array`
But there is no order in a tuple, while array has order.

N - easier notation to loop in a map
`loop(k: map) { ... }`
`loop( k,v: map) {...}` seems reasonable.
Maybe we can let user use the `:` notation too.
any function can be called with `:` as separator.
`add(x:y)` No its not useful.
We can have a lambda with two inputs.

Y - Kotlin has this:
`val positives = list.filter { x -> x > 0 }`
maybe we can have this rule that `a.b c` means `b(a,c)`
But its against all other idioms.
Maybe we can have other notations different from dot:
`func add(X:int, y:int)`
`add(x1,y1)`
`x1 >> add y1` its not working as add's first argument is supposed to be x
`x1 >> add $_, y` it's working but not as simple as `x.add y`
`x..add y` is a syntax sugar for `add(x,y)`?
`x,y..add z` -> `add(x,y,z)`
general idea: bringing function parameters before function name.
`x,y >> add ,z` ?
maybe we can just allow `x >> f y` if there would be no ambiguity. no its not general.
`x.>add(y)` => `add(x,y)`
this notation can help make reading code easir.
what about `(x)add(y)` ?
`(x)add y`
We can add a new operator like `~`:
`x ~ y` means `y(x)` and it would be a syntax sugar
A better example is `contains`: `contains(str, ":")` vs `str ~ contains(":")`
`a ~ b(c)` => `b(a,c)`
`~` will be chain operator but for the beginning.
`var index = str ~ contains(":")`
if we have `func contains(string, strin)` then `contains(":")` will create a function pointer: 
`contains(":")` => `func (x: string) -> contains(":", x)`
what about named args?
`str >> contains(pattern=":")`
or maybe we can just use space:
`str contains(":")`
`str->contains(":")`
`str.length` will call `length(str)`
When we see `A.B` we don't know if it is referring to tuple member or `B` is a function.
But if `B` is a noun, it's a member. If it's a verb then its a function.
Casing is not enough.
`str!contains(":")` - `!` already has it's own meaning.
unless we change comparison operators: `<>` for not equal, `==` for equal, and `not` for not.
Swift, Julia, Rust, Scala uses `!=` - Maybe we should use that one too.
`str ~ contains(":")`
`str contains(":")`
If we also allow paren-less function call this can become ambiguous:
`contains str, ":"`
`str.contains(":")`
we can force, if first arg comes before function name, call should have `()`.
but the we cannot write: `list.filter {...}`
space is better than dot.
`str ~ contains(":")`
`str contains(":")`
The problem with space is ambiguity. There can be cases where there is confusion over meaning of it.
`x fetchData contains ":"` what does this mean? It fetches a string data using input x, then checks if it contains `:`.
completely unreadable.
`x ~ fetchData ~ contains ":"` better.
`~` is like chaining but before. `>>` and `<<` are chain after (at the end) of arguments.
`func save(x: int, y: int, z: customer)`
`save(1,2) << loadCustomer(5)`
`loadCustomer(5) >> save(1,2)`
`func save(z: customer, x:int, y:int)`
`5 >> loadCustomer ~ save(1,2)`
`save(1,2) ~ 5 >> loadCustomer`
This will become too confusing. Either we should ignore this or unify all chainings.
`input ~ f($_, a,b,c)`
`string >> contains($_, ":")`
`input >> f(x,y,z)` => `f(x,y,z,input)`
`input >. f(x,y,z)` => `f(input, x, y, z)`
`string >. contains(":")`
not bad but still not very readable.
Can we use single letter operator?
`input | f`
`input |> f(x,y)` => `f(x,y, input)`
`input >| f(x,y)` = `f(x,y, input)`
`f(x,y) |< input` = `f(input, x, y)`
`f(x,y) <| input` = `f(x,y, input)`
OR
`input .> f(x,y)` => `f(x,y, input)`
`input >. f(x,y)` = `f(x,y, input)`
`f(x,y) .< input` = `f(input, x, y)`
`f(x,y) <. input` = `f(x,y, input)`
`str .> contains(":")`

N - also make a syntax sugar for `loop`:
`loop(x:y) {...}` means `loop(y) (x) -> { ... }`

N - function cannot change inputs even if assigns them to a local var. the only way is to clone.

Y - Think about method call dispatch with respect to multiple inheritance and polymorphism.
We can do this step by step:
- When function `f` is called with n inputs, we find all functions with same name and number of inputs, called candidate list.
- If call is made with names, drop functions whose inputs does not match with given argument names.
- Sort candidate list: Bottom-most one should be the one whose input types match with static type of arguments. This is the last option.  Top-most should be the one with types matching dynamic types (if exists).
- For each candidate, for each input, remove candidate it if it's type is not super-type of argument (according to subtype rules). 
- Define score for each candidate: distance between dynamic type of input and function parameter type.
- The one with minimum score is chosen. If we have multiple candidates, there will be runtime error.
- Starting with the first argument `a` with static type `Sa` and dynamic type `Da`.
  - For each function `f0` in the candidate list:
    - Let's call first input type `S0`.
    - if `S0` does not match with `(S1, Da)` type range, then discard `f0` from candidate list.
- After processing is done, choose the topmost function from candidate list.
- The possibilities on the caller-side are endless, so we should index and classify functions.
At compile time we know function name and number of arguments. Also we know the set of all candidates.
This will give us a list of `n` candidates from which we will need to select one at runtime.
q1. Is it possible to set this at compile time? Like developer says call this specific function.
q2. Can we use hash or some other method to make this faster?
`func f(x:int, y:int)` can be coded as `f/x/int/y/int` or `f_x_y_2/int/int`.
How can we quickly decide distance between two types? infinite means they are not related at all. zero means they are exactly the same.
`type Shape := (name: string)`
`type Shape2 := (name: string, r:float)`
`type Circle := (@shape, r:float, d: float)`
Distance between Shape and Circle is 2. Between Circle and Shape2 is 1.
each argument's type in function signature is either: primitive, array, hash, sum, tuple, named, any.
- primitive: type must match with corresponding variable being sent. If not, ignore candidate.
- array: arg must be array. calculate distance for array element's type.
- This should not be a ranking algorithm. Because ranking equals ambiguity for the developer.
Algorithm must be clear and simple and it's result will be either a single function or a runtime error.
We also may call a function where there is no definition for static types. We may use `any` version.
So we create a list of candidates. process them and drop those who don't pass tests. if result is a single function, call it, else runtime error.
But there can be multiple candidates:
```
func draw(Shape, Shape)
func draw(Circle, Circle)
...
draw(cir1, cir2)
```
Process each candidate. If it's better than current candidate, replace current. 
If it is the same, mark a flag and continue.
When search is finished, if flag it set, runtime error. Else call the current function.
This is basically a sort and rank algorithm. Just if there are two best options -> we throw error.
To measure "better than current candidate" we have to have a method to quantify this.
So problem of method dispatch boils than to comparing two functions against a call.
```
call: f(a,b,c,d)
f1: func f(ta,tb,tc,td)
f2: func f(Ta, Tb, Tc, Td)
```
- If call is being made with named arguments, this will help us drop more candidates before this step. But at this step, names don't really matter.
- With a scoring algorithm, we just need to compare a function implementation against the call.
```
call: f(Sa:Da,Sb:Db,Sc:Dc,Sd:Dd) - Sx:Dx are static (e.g. Shape) and dynamic (e.g. Circle) type of arguments
f1: func f(ta,tb,tc,td) - tx is type of function input
```
- if ( ta==Sa, tb==Sb, tc==Sc, td==Sd) score=0 (will be chosen only if no other candidates a better score)
- if ( ta==Da, tb==Db, tc==Dc, td==Dd) score=infinite (will definitely be chosen)
Type hierarchy is more like a graph. On the root is `any` and all empty types.
Below are single field types and so on.
We need to call an implementation which makes most use of provided fields. If we are passing 20 fields in total, the one that uses all of them (works with dynamic types) is the best choice. Obviously we cannot call a method which needs more parameters than what we have sent to it. Worst case: a function that does not use any of fields (all inputs are any or empty).
- Solution 1: Rank candidates, based on a score: number of fields they are using.
- Solution 2: Arg by arg, select functions that have highest fields covered for current argument.
Solution 2 makes sense because developer can control the dispatch by introducing appropriate functions which cover first arguments rather than all of them.
For sum type arguments, the typethat matches most fields is selected for comparison.

N - Implement a simple logic inference
```
;format: A is B, B is C -> A is C
type Sentence = (source: string, target: string)
func processSentence(s: string) -> Sentence[]
{
    return split(s, " is ")
}
func checkQuery(data: Sentence[], source: string) -> string | none 
{
    loop(s: data)
    {
        if ( s.source == source ) 
        {
            if ( s.target == surce ) return true
            return checkQuery(data, s.target)
        }
    }
    
    return false
}
```

N - Implement a binary search tree and use it to read data from file and find a specific number
```
type BST := Empty | (data: int, left: BST, right: BST)
func processFile(s: string) -> BST
{
}
func find(bst: BST, x: int) -> bool
{
    return false if ( bst == Empty )
    
    return true if bst.data == x
    return find(bst.left, x) if (x<bst.data)
    return find(bst.right, x)
}
```

N - convert a binary string to a number
```
func convert(s: string) -> ulong
{
    var result: ulong = 0;
    loop(x: s) {
        result += 2**counter if ( x == '1') 
        counter++
    }
    return result
}
```

N - reverse a string
```
func reverse(s: string) -> {
    var len = s :> len
    var result: string
    loop(seq(len, 0)) result += s[$0]
    return result
}
```

N - lambda input in loop must have `any` type. Because it can be anything.
`func loop(con: iterator<T>, body: func(x:T)->loopOutput)`
but in fact, if we are looping as `loop(10)` it will be an int.
if we are checking a condition, `loop(x>10)` then there will be no `x`.
if we are iterating: like map but without an output.
`func loop(data: any[], body: func(x:any)->loopOutput)->loopOutput`
we can use assert to make sure everything has correct type. 
Also note that we can pass any function which is subtype of `func(x:any)`!
so if we have `x: Customer[]`
we can call: `loop(x, func(x: Customer) -> ...)`
`loop(x, func(x: Customer) -> ... )`
`loop(x) (x: Customer) -> { ... }`

N - count vowels in a string
```
func count(s: string) -> int 
{
    return count(s) (x:char) -> x in 'aeoiy'
}
```

Y - can we remove need to put `()` in function call/loop/if?

N - Do we need `in` operator to check if something exists in an array?
It can be applied to anything. It invokes this:
`a in arr` -> `return or(loop(arr, (x) -> x == 'a'))`
but we can simply write a function for that!
`func in(x: any, y: any[])`
`if ( data :> in(arr1) ) ...`

N - with the rule of no need to embed in paren if last input if lambda, we can introduce keywords if the function has only a lambda input. 
```
if ( x > 0 ) { ... }
try { ... }
```

N - can we do import using `@` operator? Let's not make the language cryptic.

N - operator to add to beginning of array
`arr + x` add to the end
`x + arr` add to the beginning

N - how can we have `==` built-in while `bool` data type is not built-in?

Y - adding compile time assertions - this can replace templates somehow
`func push(s: Stack, x: any) { assert x[] :: s.data }`
C has `static_assert` same as D.
Maybe we can add `static` keyword and it can invoke compiler to execute any kind of statement, not only assert.
`static assert date>10`
what can we do in a static block?
1. type checking
2. check environment variables
`assert a::b` 
we can say assertions that are `::` will be evaluated at compile time. 

Y - The array and hash data structures are pretty handy but problem is we cannot alias them in any way.
`type Vector := int[]`
Then what about a vector of string?
We are not looking for a full generics. But like validation `where` which is only applicable when you are defining a custom type we can have similar case for type re-write.
`type Vector := V[]`
`type IntVector := Vector with { V := int }`
And this `V` parameter cannot be used anywhere else.
`func shift(v: Vector) -> V ...`
This will accept any vector and return V which is any. User has to cast it.

Y - Closure should not have write access to free variables. Because if closure becomes a thread, this will become a shared mutable state.

N - can we have a hash of type to function pointer?
Like a set of factories. We can but we won't have a Type type.

N - We have a problem with map/loop I thought we don't:
`loop(x, func(x: Customer) -> ... )`
This should not compile because loop signature is:
`loop(x: any[], func(x: any)->...`
No. It's ok. `Customer` is a subtype of `any`.
So `func (x: Customer)` is a subtype of `func (x: any)`


Y - Now that closure can only have read-only access, maybe we should make `loop` a keyword.
`loop(5) { ... }`
`loop(x>5) { ... }`
`loop(x: array) { ... }`
`loop(k: hash) { ... }`
`loop(k,v: hash) { ...}`
`loop(x: IterableType) { ... }`

Y - for `if` we can force user not to use paren.

Y - better operator for chaining - maybe `.`
beause 4 different operators can become really confusing.
`str :> contains(":")`
first of all, chaining is just a syntax sugar. 
So we don't need to cover every possible scenario (from left to right, from left to left, ...)
So we will have `A.B` having two meanings: access field B of tuple A or call method B with input A.
If paren becomes mandatory, this will not be a problem.
`A.B` is field access, `A.B()` is method call.
`str.contains(":")`
We can allow paren removal but cannot be used in conjunction with this syntax sugar. But it will be another exception which is not like the gen/orth we want.
If something is allowed, it should be allowed everywhere.
But for example for `filter` we want to have: `list.filter { ... }`
No this is not good. It is hard to read amd can become confusing in a large code-base.
`str.length()` -> `length(str)`
If we mandate paren, filter becomes like this: `list.filter() { ... }` which does not make any sense.
But there should be a way to reduce number of all those `{}` and `()`s.
`my_string.contains(":").send(file)`
`@(x,y).process()` -> `process(x,y)`
What about eliminating paren everywhere?
`f x y z` to call `f(x,y,z)`
`str.contains ":"` -> `contains(str, ":")`
This is not good because function name and it's input are not separable.
`map(data, { ... })`
So:
- In function call, paren is mandatory even if there is no input (`str.length()`)

Y - unify data types : int and uint for all precisions.
Go has the proposal:https://github.com/golang/go/issues/19623
Lisp and Smalltalk support it.

Y - Is it a good idea to have `str.contains(":")`?
Golang allows this. Same for D (Uniform Function Call Syntax (UFCS))
Same for C++
and somehow Rust.

Y - Should we make loop/if an expression?
`var t:int[] = loop(var x:10) x`

Y - Because we can have `and/or/...` lets enforce paren for if.

N - What about inside paren? Can we eliminate commas?
`list.procees(1 2 3)`

Y - There is not much use for having `if/else` a syntax sugar. Maybe we should make them keywords too.
`if`, `else`

N - One idea to better org:
two file types: types and functions.
And in type file we specify the modules that can have them.

Y - Suppose we have `First` and `Second` type and `type S := First | Second`
can S accept a variable which both supports First and Second type?
```
type First := (x:int)
type Second := (y: string)
type S := First | Second ;this is valid because F and S do not overlap
var t : (x:int, y: string) = (1, "G")
;t is satisfying both First and Second
if ( t :: First ) ... true
if ( t :: Second ) ... true
```
```
type S
type First := (x: int)
type Second := (y: string)
var t: (x: int, y: string) = (1, "G") ;type of t is S and First and Second
```
Seems that sum type does not mix well with implicit inheritance.
We should have only one of them.
Even if we remove implicit inheritance, we cannot remove multiple inheritance. So we can have a type inherit from A and B and a sum type as `A | B`.
But sum types give us expressiveness. Maybe we should replace it with another thing.
Interface does not give us any type checking. You can assign a string to a variable of type `DoW`.
Why not keep it? And accept this problem. And issue a compiler/runtime error when it is misused.
`type Tree := A | B | C` a variable of type Tree must be either A or B or C.
So if you want to use a variable as a Tree which is none of them or multiple of them, there will be an error either by compiler or by runtime.

N - get rid of `any`. If you want this, you have to implement generics!

N - can we simulate sum types with type inheritance?
```
type Operator;
type Plus := @Opertor
type Minus := @Operator
type Multiply := @Operator
type Divide := @Operator
var g: Operator = Add
if ( g :: Add ) ...
```
We can simplify by allowing define multiple type in one line. `type A,B,C := D`
`type SAT, SUN, ... := DoW`
`type DoW := none`
We have a `none` type which has only one possible value.
But how to have and send variables?
`var op: Operator`
`op = Minus()` - cast nothing to minus. It's possible because minus does not have any value.
`if ( op :: Minus ) ...`
How can we define a tree?
```
type Tree
type Empty := @Tree
type Leaf := int
type TreeNode := (node: int, left: Tree, right: Tree)
func dfs(t: Tree) {
    if ( t :: Empty ) return 0;
    if ( t :: Leaf ) return 1;
    ...
}
```
`type Tree := Empty | int | (node: int, left: Tree, right: Tree)`
The only problem is that now we can send a string as a tree, because of subtyping rule!
Solution1: Let it be. Less exceptions and restrictions, means better.
Solution2: Disallow automatic subtyping for `Tree` by adding some config. -> No new notation.
We can add a random field to make sure no other type will be subtype of Tree but that is not necessary.
Solution 3: Define base type inherit from `none` instead of `any`.
Or maybe we can add a better name, like `unit`, but then again, any type that has a `unit` in it, will become a Tree.
What about maybe/optional?
`type Maybe`
We are already using this for exceptions.
`type Maybe`
But `|` is more expressive.

N - Maybe in a type we should indicate is it an implicit parent or an explicit parent.
implicit parent - every other type can be it's child as long as fields match
explicit parent - every type must match fields and indicate it's parent
Why not make parent explicit?
Even if some type is in a lib and later someone else wants to inherit from it, they can just mention parent name and they can use that type everywhere.
In this way, no one can send an int instead of a tree in accident.
This will still be done with explode operator but not inside () because there are other types too.
`type Point := (@Shape, x: int, y: int) @Drawable @DT @GE`
Then what about sum types?
`type ST := int | string` the sum type itself cannot be child of anything. It's inner types can be.
`type ST := int @A @B | string @C @D @E`
So `(x:int, y: string)` cannot be child of `(x:int)` because it must be stated explicitly.
Problem is: What if the type is defined in a library and we cannot change it? This should be completely implicit.
But in that library, they don't need any extra information about that type.
Anywhere else, we just tag that type with new type.
So: to indicate inheritance, one should tag a type with it's parent and contain parent fields. This can be done in one step using explode operator.
`type X := int @A @B` means variables of type X can be used as int or A or B type.

N - Can we define abstract types? Which are empty and you cannot return values of them. Only for their children.
`type A := any where false` incorrect.
`type A := any`
`func A(any) -> assert false` ?

N - allow tagging a type so we state it's parent. It's optional but can help make code readable.
```
type Tree
type EmptyTree
type Leaf := int @Tree ;tag Leaf so we indicate it's parent type is Tree
```

N - can we allow mutable function inputs with some kind of container?
If we allow closure to modify free variables, this can be done with ease. No need to change syntax or add a new notation.
or maybe we can specify a special type of lambda only to change a value.
Like `set` lambda.
Like converting a local variable to a closure which is bound to it.
`var g:int = 12`
`var cl: func(int) = %g`
`cl(11)` will set value of g
No.
What about this?
```
type Wrapper := (set: func(x: any), get: func()->any)
var intWrapper := (a: int, set: func(x: int) -> { a=x }, get: func()->a) ;this is incorrect
```
Or let's have a core/built-in keyword which gives you two functions for an input: getter and setter.
or maybe only setter.
`processData(x,y,z, setter(x))`
or add `&` notation. This definitely should not be attached to type name because it will make type system mode complicated.
`func process(x: int, y:int, &z: int)`
- This definitely applies to function argument. Not to type or variable or any other thing.
- Caller does not need to do anything special.
Can this be simulated by compiler?
```
func check(&x: int[]) -> x[5] = 11
...
var t: int[]
check(t)
;above is changed to this:
t = check(t)
;and the function becomes:
func check(x: int[])->int[] y=[@x] & y[5] = 11 & return y
```
In other words, result is copied back to the caller. 
function receives a read-only reference to the variable. makes changes to a clone and returns it.
But the developer can do this himself! why complicate compiler and notation by this hack?

Y - what should be default value for a sum type? or any?
for any you must initialize. 
for sum type, same.

N - Is it possible to make sure functions that accept a Tree (or Stack or ...) will not accept `any`?
I want to make sure `func DFS(x: Tree)` won't receive a Stack for example, just because both are empty.
`any <- Tree, Stack ...`
The easiest solution is to introduce a special field that is unique to a Tree.
Any Tree or subtype will definitely have it while a Stack won't have it. So dev cannot send a Stack by mistake.
Now, what that "unique field" should be? 
We cannot waste memory by adding a field which is not supposed to contain any data. This will be just a compile time thing.
We can add a small field with a random name. 
`type Tree := (dsddasdsadasd: int)`
`type Leaf := (@Tree, x: value)`
But still any other type that has an int field will be considered a subtype of Tree.
We should add some kind of a Tag to types.
If a type wants to be subtype of other, it should contain subset of it's fields and same tags.
Will this preserve good things that duck typing gives us? If base type is defined in a library, we can make it subtype of our own type by adding our own type without a tag!
An easy way is to add a dummy field of type `none` or a type which inherits from none. It is according to all rules, no new exception or notation. We may just add a sugar for this.
`type Tree := (dummy: none)`
Maybe we need a shotcut to define a new type in-place based on any or none, if it's going to be used in only one place.
`type Stack := %StackElement[]` -> StackElement is child of any.
```
type TreeBase := none   ;this type does not have any value. It is just a market/tag
type Tree := (id:TreeBase)`
type HeapBase := none
type Heap := (id:HeapBase)`
```
Let's don't change it. It will only complicate the language, adding new notation or syntax.

Y - More thinking about type syste, subtyping, type equality and type aliasing and matching rules.
`type A := B` means A is a new type that internally is exactly same as B.
`type T` is an empty type or it is for `any`? If empty type, it does not have any value or representation.
If it is any then it can represent anything. It should represent nothing (none).
if we differentiate any from none, then an empty type which inherits from none...?
I think none should be parent of any (if they are not separate). Because of subtyping rules.
So if a lambda is supposed to have none output, it can return anything (and it will be ignored).
if it's output is any, it cannot return none.
Whenever a specific type is expected, you can provide a more specialized type with more fields and details but not vice versa.
So if (x,y) is expected, you can provide `(x,y,z)` (as function input or output or ...).
So when type T is expected, you can provide either T itself or any of it's subtypes.
.....
We have two categories of types: named and unnamed.
Unnamed: `int, string[], float => int, (int,int)...` - They are created using language keywords and notations like primitive type names, `any`, arry or hash, ....
Named: `type MyType := ?????` These are defined by the developer and on the right side we can have another named or unnamed type. Right side is called underlying type.
We have two special types: `nothing` and `anything`. All types are subtypes of `anything` (except `nothing`). `nothing` is only subtype of itself. So if a function expects nothing (which is weird) you can only pass a nothing to it and nothing else. If a function expects `anything` you can pass anything to it (except `nothing`).
We have 7 kinds of type: tuple, union, array, hash, primitive, function.
We write C <: S which means C (child) is subtype of S (supertype). 
- A type is subtype of itself.
- Primitive: C and S are the same
- Array: if their elements <:
- Hash: Vs <: Vc, Kc <: Ks
- function: C:func(I1)->O1, S: func(I2)->O2 I1<:I2 and O1 <: O2 and if inputs are named, they should match.
- Sum types: C: C1|C2|...|Cn and S: S1|S2|...|Sm if Ci<:Si and n<=m
- Tuple: C=(C1,...,Cn) and S=(S1,...,Sm) if Ci==Si and n>=m and if both have named fields, they must match
Variable of named type can be assigned to underlying unnamed type and vice versa. `type SE := int` then SE and int are assignable.
Suppose that we have a function `func f(x: T1, y: T2, z: T3)`
You can call this function with 3 data, if type of each data is subtype of corresponding function argument. if input is named, it should match with names on the function declaration.

N - Type hierarchy
The ones who are below are those who can accept more general data. so (int,int) is below int.
As a result top of the tree if `nothing`.

N - Should we consider `(int)` same as `int`?
If we do so, a func which expects `(int)` can be called with an integer.

N - What about function pointer/lambda?
A lambda has a `func` type. The lambda we want to send to a function should have a type which is subtype of expected type.

N - Go does not permit adding a new function to an existing type if the type is outside file of new function. Can we do the same thing here? It will help organizing the code.
But we do not want to tie data (type) and functions.

N - The implicit subtyping for empty types can be confusing sometimes. Is it possible to make it more explicit and readable.
Think about different situations like multiple functions, type hierarchy, function overriding. 
Note that we want a simple and readable language with minimum rules and exceptions.
If `type` defines a new type, then user cannot use another type instead of that (if that another type does not have a direct matching function)
```
type SE := int
type DE := int
func f(x:SE) ->...
var g: DE = 12
;you cannot call f with g because their type do not match
;you can of course cast
f(SE(g))
```
Type alias is a different type but it is subtype of its target type.
You cannot send a `DE` when `SE` is expected. Two named types can never be equal.

N - what happens if I send a `(int,int)` when `(int)` is expected?
If both are tuples, then this is a simple case of inheritance. If tye is not specified, it's ok and we can pass.
If type name is specified then they must match. but there is ambiguity so there will be error.

N - Golang has similar syntax for type assert and extract.
`x = y.(int)`
`switch ( y.(type)` 
Can we make them similar too?
Similarly, `x.[]` is a good notation to use. 
custom cast can be done in custom functions.
`Circle.(Shape)`? Does not seem nice.
What about type checking? `::`
`if ( x :: int ) { y=int(x) ... }`
Does not seem a good idea.

N - Suppose that `type SE := int` and we have only one function: `func f(SE)` 
can we send an integer to it? NO.
We should not be able to do that.
if we only have `func f(int)` we can send int and SE to it? NO. Let's make things simpler.
In golang you cannot.
The simpler one is: If two functions we have, each will go to same type.
Make it simple: Same type always.
The purpose of defining named type is not to redirect calls to another type because those calls are already being made with underlying type. `type SE :=int` purpose is not to catch calls that are passing int. The purpose is defining new functions with new type (SE) and caller will send correct SE type not int.
So there should be no implicit casting. So that's why in golang it says, "A named and an unnamed type are always different".
`type SE := int` means internally SE is an integer and you can easily connvert it.
So as a result of this, you can never send `Heap` in place where `Tree` is expected.
But where `Tree` is expected you can pass subtypes of Tree.

N - should `int` be subtype of `nothing` or `any`?
You can send a circle where a shape is needed. shape is supertype and circle is subtype.
You can send any where int is needed? no.
You can send int where any is needed? yes. so any is supertype and int is subtype!
I think there are two different concepts here. any and super-sub typing.
any can be considered a sub-type of everything.
Maybe we should treat any and nothing separately. nothing is a type which does not have only one valid value: nothing.
any is a type which contains all types in the system (like int | string | ...).
These two can be considered keywords to mean a function can accept everything or nothing.
Other than these two, rules of parameter sending is specified with subtyping. 
So if f expects type T you can send a subtype of T. If f expects a Shape you can send a shape.
So we don't need to put any/nothing in the type tree.
You can send nothing where int is needed? no.
You can send int where nothing is needed? no.
An input cannot be of type nothing. 
It can only be used as output of a function or block. But this is not general.
It should be allowed as an input but it does not make sense.

Y - rename `any` to `anything` and `none` to `nothing`.

Y - loop can accept expression and will output an array:
`var x: int[] = loop(10)` same as `[0..9]`
maybe we can even remove 1..10 notation.
we can extend loop like `loop(2,20)` means 2 to 19.

Y - provide shortcut for tuple:
`x,y = func1()` will assign x and y output of func1.
`return 1,2` will return a tuple of two values.
`return (x=1, y=2)` same as above with name.
maybe we should write: `x,y = @func1()` so we are expanding a tuple.
`(x,y) = func1()` does not have a meaning. what is the left side?
`@` is inverse of `()`.

N - can we simplify function by saying its input is a tuple?
If so, we should permit sending subtypes to the function which makes things confusing.
Also we should permit optional values which makes method dispatch difficult.

Y - if we use function name as a variable it will be confusing.
`func f(x:int)->`
`var t: func(int) = f`
Maybe we should add a notation. The only advantage is prevent confusion.
When I see `t=u` and t is lambda, then is `u` another lambda or it is a function name?
`t=&u` this is more intuitive. but is used for continutation.
`t=^f` to denote pointer to a function.

N - `&` can be used for continuation in one line. for multiple line, create a block.

N - Returning modified data in output as an alternative for mutability is good but may be bad for performance.
`x=f(a,b,c,x)`
`func f(a,b,c,x) {...y=@x..return y}`
Is it possible that we keep things pure and also give permission to change the value?
I think, only if we provide lambda. Anything else will either be complicated or making things impure.
Giving lambda gives control to the outside world to control/mock things.
idiomatic way: return modified value
suppose in real-world we have a function which receives an array of 1000 customers and wants to mark something.

Y - Do we need `where`?
If we need it, it should be allowed in function definition too.
maybe we can extend where to allow modification through lambda. Then we can use it for mutable function input.
But first lets solve the method dispatch issue:
`func f(x: int where {$>12})...`
`var g:int = 12`
can we call `f(g)`?
Let's remove `where` it makes things simpler. And prevents future exceptions.

Y - add `ref` keyword.
what if inline lambda can change local variables. It cannot be shared between two threads because it is online.
BUT this will be a big exception.
But even haskell has mutable arrays.
What if we employ a concept similar to monad?
The function returns an action (lambda?) which will change the array. The caller will execute this action and it's local variable will be updated.
why not let the developer decide if something should be immutable or not?
everything being immutable is some kind of exception too!
Of course default is immutable for everything but function input can be mutable too.
But we should not go too far (an immutable array of mutable int, an immutable hash with mutable values, ...)
Now that function does not accept a tuple, we can have a special definition for function signature.
There we can denote if an input argument is mutable or not.
This definitely does not change anything about type or ...
We are not defining a NEW type. We use same types.
The only requirement: function should clearly indicate mutable input.
Sender should indicate argument which is being sent as mutable -> this will make code readable.
If I see `process(x,y,z)` I don't know whether any of these 3 arguments will be changed or no. But when there is a notation I know when I read the function.
C# has `ref` or `out` keyword. This is good because does not imply change in the type.
`func process(x: int, ref y: int)`
`process(t, ref u)`
Good but it is a bit long! 
We can replace it with a notation like `%` but I think `ref` is more descriptive (and intuitive at least for c# developers)
The good thing is that its not possible to use ref when declaring a tuple or any other place.
And a function can call another func with ref input either if argument is local or its a ref too.
This definition helps us keep method dispatch, type casting and many other places without a change.

Y - if we have `func process(ref x: int)` can we also have `func process(x:int)`?
I think we can have (gen. do not ban anything unless you really have to). But it would make code complicated but still readable.
Because when the call is made, we make clear which version we want to call.
So `ref` will affect method dispatch too.

N - q find cases where we have "you cannot" in the specification. These are exceptions.
Of course some of them are necessary: you cannot assign a string to int.

N - if we agree that function input is not a tuple, can we use `x:12` notation to send named function input?
It won't be intuitive and consistent.

Y - If named types can never be equal it will be against subtyping rules -> exception.
Named types are subtype if their underlying types are subtype.
So anyway someone can send a Heap when a Tree is needed.
The correct and general way: Named types are what their underlying types are in terms of subtyping. They are equal to their underlying type.

N - if we have `(x:int, y:string)` and two method candidates accept int and string, there will definitely be a runtime error in method dispatch -> runtime error.

N - if a method accepts `any` we should not be able to send anything to it except any.
is this correct? No: All types are subtypes of `any`

N - what about comparison?
what data types can be compared? `x==y` (other comparisons are only for int and float).
equality check is only possible for data of the same type, unless user has provided `opEquals` methods.

Y - User can check if a constraint is valid using `::` opertor.
`type Even := int where $ %% 2`
`if ( myNum :: Even ) ...`
And cast normallt: `var t = Even(g)` it will give exception if g is not even.

Y - `%%` operator for check divisibility `x%%2` = `x%2==0`

Y - if `::` is used with literals, it checks both for type and value equality. Else checks for type matching.

Y - What we are trying to achieve using `where` is available in some other languages and is called refinemenet types.
Perl6, Haskell, Rust (proposed), Ada
Check to see how it is implemented there and if it is general.
Can we instead of this general concept, use a sum type where each section has a criteria?
`type Num := int $>0 | int $<0 | int $==0`
Another option: where cannot be used to affect method dispatch. It will be executed when method is chosen.
Another option: Each data type has different states. we can put these states in a sum type and dispatch based on those sum types. Example of states: int (positive, negative, zero), file (open, closed), ...
We can do all these checks in the function too but this can happen in a lot of functions -> a lot of code repeated.
Advantage: With assert we have to do it everywhere we want to work with the data. but with where only once.
About method dispatch: We can do dispatch normally without where clause mentioned in function signature. Then when a method is chosen we can check where clause. 
If we encode this into a sum type, we can dispatch based on it's type too and leverage method dispatch.
`type MyInt := Positive where $>0 | Negative where $<0 | Zero where $==0`
`func process(x: Positive)`
`func process(x: Negative)`
But most of the time we only want to denote valid/invalid cases.
What about a specific data type: `type Validated := (x: any, validator: func(any)->bool)`
`type IntV := validated with { }` But this needs suppose from runtime.
Other solution which is not readable: function with specific naming.

Y - How does `where` affect inheritance?
if A inherits from B does it also inherit it's validations?
A valid Circle must be a valid Shape too!
If so, you must refer to them when you define circle:
`type Circle := (@Shape, r: float) where { $ :: Shape and $.r>0}`

Y - How does `where` affect method dispatch?
It just makes things more complicated everywhere.
Can't we just simplify it?
For example if `x` is casted to `PositiveInt` for a function call, then casted to `SmallInt` for another function.
Then can we call the first function again? No. The data has one type at each time. If we change type from PositiveInt to SmallInt, all the information about PositiveInt will be lost.
What about making data and type and predicates separate? 
Predicates can be like labels for a variable. We can assign as many labels as we want to a variable.
A function can check for any number of labels.
Labels are not changed until variable is changed.
Operations: Add a label to a variable, check if variable has a label.
A label is a function which returns bool.
Can we handle this transparently by caching function results?
`func isPositive(x: any) -> bool`
If this is called once for a variable, it's result can be cached. so next time another function calls this, it will use cache.
All of this can be handled behind the scene and transparently. No need to do anything by the developer.
Just put checks whenever you want. Just know that functions that receive a single input and output `bool` are predicate functions and are treated like labels.
This is a good example of reducing complexity while retainin much of benefits.

N - in `where` ability to define custom errors.

N - can we make `::` notation simpler and more intuitive like go, but expressive?
`::` is doing multiple things. in `if ( x :: int)` its a bool operator for type checking.
in `x :: { y:int -> ...}` it is a case statement.
But these two definitions are compatible.

Y - Clatify `with`.
We are using with subtyping. Now this is trying to give depth subtyping.
`type Point := (x: Shape, y: Shape)`
`type Data := Point with { Shape := Circle}`?
Are we going to have both? Doesn't it make things more complicated?
Can we simplify it?
What comes inside `with`? What is the purpose of with?
If IntStack is going to be a subtype of Stack, then we have depth subtyping.
`func push(s: Stack, x: any)`
We want to be able to send a Stack with same structure but different type (int) to push function.
covariant: a variable that can accept any of it's subtypes
`@` is used to width subtyping. `with` is for depth subtyping.
`func equals(x: any[], y: any[])->bool`
We can do width subtyping with `@` with minimum effort. What about depth subtyping?
Maybe we can do this similar to templates (by adding parameters that can be used for depth subtype)
`type Map := Source => Target`
`type MyMap := Map with { Source := int, Target := string }`
`type MyMap := int => string` same as above.
a map of `list<int>` to `list<string>`?
I feel that the effort to keep gen and orth and no exceptions, is making the whole subtyping more and more complicated.
Java does not let this subtyping to exist.
C++ makes it easy with generics but I think there is no inheritance.
Easy way: dont provide anything. Let the developer copy the super-type data structure.
`type Stack := StackElement[]`
`type IntStack := int[]`
`type Packet :=   (status: Data[], result: (x:int, y:int),       headers: xany[] => yany[])`
`type IPPacket := (status: int[],  result: (x:int, y:int), headers: int[] => string[])`
`type IPPacket := (^Packet{Data := int, xany := int, yany := string})`
We can make it super-flexible by type-generating functions.
C++ covers this type of subtyping with generics.
But in structural subtyping, we want types to match if their structures match. This should not rely on the designer of base type see the future and how his type will be used. 
Requiring use of generic types, will make language more complex and require designer of a type to forsee the future about how his type will be needed.
Let the designer of the base type do his job and design the type as he wishes.
The user, should be able to "customize" and "re-write" other types to create a new type.

Y - We should modify type system explanations to support depth subtyping too.

Y - Clarify about using `@` to subtype an array or sum type.
```
type arr := xany[]
type optional := Empty | xany
type arrInt := @arr{xany := int}
type optionalInt := @optional{ xany := int}
```
definition of `@` then will become too broad. Maybe we should use another notation.
`@` will solely be used on data and used to explode/clone data.
`&` can be used. This denotes some kind of reference which makes sense.
What happens to the original `&` opertor then?
Advantage: The definition of explode `@` does not make sense for non-tuple types.
So using `&` is a good idea. It duplicates another type at place of declaration.
We can call it "type duplication" operator. or type copy or type reference operator.
Let's make `^` a type copy operator and `&` to assign lambda to function.

Y - method dispatch.
A method will be chosen which satisfies most fields of the tuple.
`func process(any[])` - 0 fields are covered
`func process(Shape[])` - 2 fields are covered
`func process(Circle[])` - 5 fields are covered
if we call process with a Circle array, the third function above should be chosen.

N - Can we define the expected type in-place?
`func printName(x: (name: string))...`
Any data type that contains a string name can be passed to `printName`.
If we make function call with x parameter: `processData(x)` this will call a copy of processData function which accepts a parameter with `name` input (named or typed).

N - can we make type system simpler?
solution 1: remove subtyping.

Y - Use `&` to assign function name to a lambda.

Y - can we prevent casting circle to shape?
when we want to have only methods on circle and if something is defined for shape but not circle, dont call it.
`func process(s: Shape)`
we don't want to implement all Shape methods for a circle and we don't want runtime system to fall back to shape when a method is not defined for circle but defined for shape.
what about casting?
`func Shape(c: Circle) -> return c` this is the default behavior
`func Shape(c: Circle) -> assert false` this prevents calling any method which is defined on Shape, with an instance of Circle.

N -  As NASA found out, passing an object of type DistanceInInches to a function expecting DistanceInCentimeters can be problematic.
I think this typing is not ideal. You should have something like this:
`type Distance := (value: int, unit: CM | IN)`

N - What about continue execution?
Do we really need it?
Usage: When we are supposed to provide a simple expression, we can put multiple expressions and combine them using `&`.
No we don't really need it.

Y - we will need to define a lot of empty types for generics. can this be avoided?
`type Packet :=   (status: Data[], result: (x:int, y:int),       headers: xany[] => yany[])`
`type IPPacket := (^Packet{Data := int, xany := int, yany := string})`
we can say, `!T` will define internally a type T which is based off `anything`. It will not conflict with any other type.
And you can replace it with `^Parent{T:=int}` or `%`.
`%` local-anything-type creator will define a local type based off anything.
baseically `%T` means anything but can be referenced in child types to specialize the type.

Y - Remove operator overloading.
it is not general.
`opIndex` this needs reference access which does not make sense with the syntax:
`func opIndex(ref x: Customer, i: int, v: string) -> `

N - casting code must be written in the same file that type is defined.

Y - Disallow writing custom cast function with `TypeName(x)` name.

N - Strategy about casting: if we let user disallow cast from circle to shape, he should also be able to disallow cast from shape to circle which is against polymorphism that we have. 
The code can become really confusing.
Let's forget about casting at all. toString and normal conversion from int to string and ... will be provided by runtime using normal functions.
Casting notation is not confusing because function names must start with lowercase.

N - `var z = Car(age=121)` this is confusing and similar to function call. It's not. function name start with lowercase.

N - in go we can have:
```
if v, ok := value.(migrater); ok {
    v.migrate()
}
var c int
if c = b; a > b {
    c = a
}
```
Maybe we can use semicolon to group some statements together into a single expression.
But this hurts readability.
```
var (v, ok) = Migrater(value)
if (ok) v.migrate()
v.migate if (ok)
```

Y - use `!` for local anything type. `%` is used for numbers.

Y - adding paren to if makes code more readable. same as what we have for loop.

N - tree definition:
`type Tree := (x: !T, left: ^Tree{ T := !T }, right: ^Tree{T:=!T})`
Here using `!T` is mandatory because if we use plain T:
`type T`
`type Tree := (x: T, left: ^Tree{ T := T }, right: ^Tree{T:=T})`
it becomes confusing.
```
type Hash := !K => !V
type hh := ^Hash { K := int, V := string }`
;or
```

Y - maybe we can just eliminate `^`! if you want width subtyping:
`type Circle := (Shape, r: float)`
for depth subtyping:
`type IntStack := Stack{T:=int}`
or
`type IntRecord := (x:int, y:float, Stack{T:=int})`
using an unnamed field in tuple, means expand definition of that type here.
Also to simplify (as an option), you can assume order:
`type Stack := !A[]`
`type IntStack := Stack{int}`
here, if we don't specify name, first will be `A` second will be `B` and so on.
This makes syntax cleaner.
`type Tree := (x: !A, left: Tree{!A}, right: Tree{!A})`

N - Array cannot be used for queue or linked-list because it is supposed to be consecutive memory locations.

N - push
`type Tree := (x: !A, left: Tree{!A}, right: Tree{!A})`
`func push(x: Tree)->anything`
`func push(x: Tree{int})->int` this is ok
`func push(x: Tree{T})->T` not valid.
what comes on the right side of `{A := B}` must be either a concrete type or another alias.

N - method dispatch
`func process(s: anything[])`
`func process(s Shape[])`
`func process(s: Circle[])`
`var g: Shape[] = createCircleArray()`
static type of g is shape array but dynamic type is circle array.
```
type LinkedList := (x: !T, next: ^LinkedList{ T := !T })
type ShapeLL := ^LinkedList{ T := Shape }
type CircleLL := ^LinkedList{ T := Circle }
func process(x: LinkedList)
func process(x: ShapeLL)
func process(x: CircleLL)
```
```
type Tree := (x: !T, left: Tree{ T := !T }, right: Tree{T:=!T})
type ShapeTree := Tree{ T:=Shape }
type CircleTree := Tree{ T:=Circle }
```

Y - can we simplify polymorphism
we have two types: width and depth
width:
`type Shape := (name: string)`
`type Circle := (^Shape, r: float)`
depth:
`type Stack := %Element[]`
`type IntStack := ^Stack{ Element := int }`
Golang does not let you define two methds with the same name even if parameters are different.
When user writes `process(x,y,z)` there is a chance that his intended process function is not called.
Because of polymorphism and ... .
one solution: import only one file at a time.
this does not stop the problem but makes tracking easier.
langauges with multiple dispatch: common lisp, perl 6.
one approach: define fixed parameter, which cannot be overriden and determined at compile time.
`func process(x: Customer, f: !File)`
this function can only be called with second argument of static type of File.
obviously, you cannot overload this with other File children.
output of functions with the same name must be compatible.
```
func process(s: Shape) -> doWork(s)
func doWork(s: Shape)
func doWork(c: Circle)
```
The doWork call in process cannot be determined at compile time. unless we only have one doWork function.
problem is, a variable can have a lot of types which are implicit.
`var c: Circle = ...`
c has a Circle, Shape, Drawable, Comparable, Equalitable, Iterable, Object and anything type.
So if we make a call to `process` function which can accept all of above types, it might not be resolved at compile time.
maybe we also have `BigCircle` type and type of c becomes BigCircle at runtime.
what about zero size types?
```
func process(x: anything)
func process(x: Comparable)
func process(x: Iterable)
func process(x: Drawable)
type Comparable
type Iterable
type Drawable
type Circle := (r: Radius)
var c: Circle = (r=12)
process(c)
```
1. functions with named empty types are superior to unnamed (anything).
Still we have 3 candidates: Comparable, Iterable and Drawable.
There is no way we can prioritize these three.
-> Compiler error. unless we cast
`process(Drawable(c))`
`var s: Shape = Shape(circle)` still keeps original circle
`process(Shape(circle))` keeps original data
`var s: Shape = Shape(@circle)` clones circle into a shape.
`process(Shape(@circle))`

Y - What happens with this?
```
type A := (x:int, y: int)
type B := (x: int)
var t = A(x=10,y=20)
var y: B = (@A)
```
It should fail because we are providing extra input to y variable initialization.
But if we cast, it should be ok, because that is the point of casting:
`var y: B = B(@A)`

N - How do you write this?
```
ParseResult<V> VParser::parse_impl(ParseState state)
{
    ParseResult<A> a = a_parser.parse(state);
    if (ParseSuccess<A> * success = a.get_success())
        return ParseSuccess<V>{{std::move(success->value)}, success->new_state};
    ParseResult<B> b = b_parser.parse(state);
    if (ParseSuccess<B> * success = b.get_success())
        return ParseSuccess<V>{{std::move(success->value)}, success->new_state};
    ParseResult<C> c = c_parser.parse(state);
    if (ParseSuccess<C> * success = c.get_success())
        return ParseSuccess<V>{{std::move(success->value)}, success->new_state};
    ParseResult<D> d = d_parser.parse(state);
    if (ParseSuccess<D> * success = d.get_success())
        return ParseSuccess<V>{{std::move(success->value)}, success->new_state};
    return select_parse_error(*a.get_error(), *b.get_error(), *c.get_error(), *d.get_error());
}
```
```
loop(x: [a_parser, b_parser, c_parser, d_parser]) {
    var ok, result = @parse(x)
    if ( ok ) return process(result)
}
```

N - shouldn't we support multiple return?
`var ok, result = @parse(x)`
vs
`var ok, result = parse(x)`

Y - remove `.@`
We can also have:
`var ok, result = parse(x).@` which is weird.
this operator is used to clone a tuple, array or hash.
So it is useful.
The `.@` notation is used to convert a tuple to an array in the ref which is not really needed.
`var ok, result = (x=10, y=12)` this should be fine.
`var ok, result = (10, 12)`
so we can remove `.@` notation.

N - How can we do type specialization with nested data types?
The parameter should be defined in the main type too.
```
type Array := !T[]
type Stack := Array[] ;you cannot specialize this
type Stack := Array{ T := !T }[]
type IntStack := Stack { T := int }
```

N - in order to reduce complexity can we state parameter in a type must be anything to be specializable?
You can specialize type A if it has `anything` or `!T` types. You can replace them with the type you want.
We already have subtyping rules in place.
If we have `type Array := !T[]` and `type Stack := Array[]` then we can specialize Stack with another type which is subtype of `Array`.
Some more exaplanation about type re-write.
`TYPE{ A := B, C := D}` will be replaced by compiler, with definition of `TYPE` type and it will apply given transformations to the definition.

N - alternative for subtyping: group function definition redirection (whatever on circle, redirect to shape)
at runtime, dispatch will be only done for dynamic type of the inputs. no complicated algorithms. 
But user can define a group of functions at the same time to redirect some of those cases.
```
func process(s: Shape)
func draw(s: Shape)
func process(c: Circle)
funcs *(*Circle*) -> *(*Shape(Circle)*)
```
what if these mass rules have conflict? 
Then we can handle it at compile time?
```
func process(s: Shape, t: Color)
func process(c: Circle, t: Color)
func process(s: Shape, t: SolidColor)
...
process(myCircle, mySolidColor)
```
I don't think it will add any clarity. Will just make things more complicated and add a new notation.

N - example of method dispatch with multiple candidates:
```
Shape -> Circle -> GoodCircle
Shape -> Rect -> Square
GoodCircle + Square -> Sprite
var mySprite : anything = createSprite
process(mySprite)
;if we have function for Sprite or one of Square/GoodCircle it would be fine.
```

Y - if a function needs a parameter which must have fields from two types, it can be defined like this:
`func process(x: (TypeA, TypeB))` this is an in-place definition of a tuple which inherits from two other tuples.

Y - How do we solve diamond problem?
`type MyType := (A, T, B)`
`func process(x: (A,T))`
`func process(x: (T, B))`
```
func process(int, int, int, int, int)
func process(string)
type C := (string, int, int, int, int, int)
process(c) ?
```
there is ambiguity here in both cases. what if fields are not same?
in this case shall we match with the one which covers most arguments? 
I think a better solution which makes method dispatch simpler and more understandable is to issue a compiler error.
Only if there is ambiguity. 
For example for Shape-Circle case, if a method is not defined for Circle but for Shape, a call with Circle instance which is not ambiguous can be redirected to Shape.

Y - multiple dispatch
maybe we should only care to the dynamic type and function should specify dynamic types they can accept.
`Drawable >> Shape >> Polygon >> Rectangle`
`func process(x: Drawable, y: Drawable, z: Drawable)`
`func process(x: Polygon, y: Shape, z: Drawable)`
`func process(x: Rectangle, y: Drawable, z: Shape)`
`func process(x: Rectangle, y: Shape, z: Shape)`
`func process(x: Polygon, y: Polygon, z: Shape)`
`func process(x: Drawable, y: Shape, z: Polygon)`
suppose that we have these methods. when we make a call `process(a,b,c)` if dynamic types are R,R,R then no function will match the type completely. There are three options:
1. Provide an algorithm to select best match
2. Issue compiler/runtime error
3. Provide a mechanism to select default method
We can combine 2 and 3: You can define default method using `anything` type! and redirect to a good method. Else there will be error.
But how can we cast `anything` to `Shape`? This may not work all the time.
`func process(x: anything, y: anything, z: anything) -> process(...`

Y - When we have this:
`func process(x: Circle) -> process(Shape(x))`
the runtime type of x is not changed. It will still be Circle, so how can we force calling process(Shape)?
similarly:
`var t = Shape(myCircle) - process(t)`
which pocess should be called? Process(Circle) or process(Shape)?
static type of t is Shape (specified in the code), but dynamic type is Circle.
We dispatch a method call based on dynamic type of a variable so how can we ever redirect to another version?
Maybe we should keep 3 types: static, dynamic and dispatch.
`var t = createCircle - process(t)`
What about this? If static type of t is Shape and dynamic is Circle? Definitely process(Circle) should be called.
So, `Shape(t)` will change both static and dynamic type of the data. So, any further call to methods with this value, will consider it's static and dynamic type as Shape.
As a result, inside a method we are sure that static and dynamic type of input are the same.
But: `var cir: Shape = createCircle(); process(cir)`
if there is no `process(Circle)` this should call `process(Shape)` automatically! No.
If Circle inherit from Shape and Drawable and we have methods for both of these types, it will cause ambiguity.
Now that we are allowing multiple dispatch and multiple inheritance, things in the call-side are getting very confusing and ambiguous.
We should try to make this part as simple and stratight forward as possible.
If there is ambiguity (more than one choice) we should throw error.
User can cast a variable to a supertype, to remove ambiguity. `process(Shape(circle))` or `process(Drawable(circle))`.
We can say, in a function with more than one input, dispatch method can travel in hierarchy for the first argument but other arguments should exactly match dynamic type of the input. But this is too complicated.
User can easily write catch-all function:
`func process(x: Shape*, y: Drawable*, z: Drawable*) -> ...`
this will catch any call where x is Shape or it's subtypes, y is Drawable or subtypes and ...
Of course any other function with this name and 3 inputs must not have conflict with this.
Or maybe we can cover this in a sum type: `type ShapeOrChildren := Shape | Circle | Square | ...` No.
multiple inheritance with single dispatch
single inheritance with multiple dispatch
multiple inheritance with multiple dispatch!
another way: when calling a function, indicate which argument can be casted to parent type if needed.
automatic casting of an argument to higher type (Circle to Shape) means loosing data. And this should only be done with explicit permission of the caller/developer.
For a function with just one argument, polymorphism is easy. Just traverse inheritance graph upward. If multiple choices found, issue an error.
```
Drawable + Shape -> Circle
func process(Drawable)
func process(Shape)
process(myCircle) -> error : two candidates found
```
- If function argument type must match the dynamic type of the argument, how can we earn flexibility? 
1. By changing function signature `func process(x: Shape*)`
2. By changing at call site: `process(~x)`
"There are not that many real cases where double or triple dispatch is used" - http://hpac.rwth-aachen.de/teaching/sem-lsc-12/MultipleDispatching.pdf
So why make it so complicated?
- In case of single argument, we can look in type graph.
- More than one: what if second arg is an integer?
For each method call, first find candidates (same name and number of inputs).
Filter out the ones that type of argument conflicts with type of parameter being sent.
Remaining: If one -> call, if multiple and they differe on only one type -> traverse type graph
else error.

Y - If f is for Shape and we call it with Circle, it will be called with Shape.
If `f` calls another function which is for both Circle and Shape, which one will be called?
The one for Shape? Circle?
Option: If a parameter is named `this`, it can match with subtypes of that type. Else type must match.
`func process(this: Shape, x: Drawable, c: Color)`
here x and c must match given types. but `this` can be Shape or Circle or ... .
So when you call another method `method1(this, ...)` it be resolved to find best match.
So if we have `method1(Shape)` and `method1(Circle)` and `this` is not Shape but a Circle, it will call `method1(Circle)`.
This makes method dispatch simpler and more explicit and naturally prevents multiple dispatch problems.

Y - `&process(myCircle,$_,$_)(10)(20)` - does this make sense?

N - assign by value/reference
```
type AB := int | float | Customer
var t: AB = 9
var h: AB = x ;is this assignment done by value or reference? should be by value.
var g: int = int(x) ;is this possible? no. you should cast (and check type before).
var g:int = x :: int -> int(x), anything -> 0
```
Assignment of sum types, is based on type. if primitive is being assigned: copy else, reference.

Y - provide a mechanism to put multiple commands in the same line
`var x =10;x++`?
`a&b&c if (x>0)`
`loop(5) a&b&c`
`&`?

Y - shall we differentiate the notation for tuple? it is similar to function call.
what about type specialization?
```
type Point := (x: int, y: int)
var p: Point = Point(x=10, y=20)
var p: Point{int} = (x=10, y=20)
```
`[]` is used for hash and array
`()` for function call
`{}` for block and type specialization
if we use `{}` for tuple definition and literals (then we can use `a:b` notation) what about type specialization?
`%` and `^` are not used.
`a=b` notation implies a variable definition in tuple assignment.
```
type Point := {x: int, y: int}
var p: Point = Point{x:10, y:20}
@p ==> x:10,y:20
{@p} ==> {x:10,y:20}
var t,u = @p -> t=10, u=20
var p: Point(int) = {x:10, y:20}
process(x:10, y:20)
process(@p)
var p: Point(int) = {x=10, y=20}
type Record := { Vector(V:int), Stack(int), Map(int, string) }
```
What about calling a function with named input?
`process(x:10, y:20)`
we should use `a=b` when a is a defined variable.
summary of changes proposed:
- Type specialization: `T(A:X, B:Y, C:Z)` or `T(X, Y, Z)`
- Casting with specialization: `var p: Point(int) = Point(int){x=10, y=20}`
- Tuple definition: `type Point := {x: int, y: int}`
- Tuple literal: `var p: Point = Point{x:10, y:20}`
- Function call: `process(x:10, y:20)`
- Call by explode: `process(@p)` ==> `process(x: 10, y:20)`
- Explode operator: `@p ==> x:10,y:20`
- Cloning: `var x = {@p}`
- Explode tuple: `var t,u = @p -> t=10, u=20`

N - what if function output is a tuple?
`func process(x:int) -> {y:int} { ... }`

N - what is the syntax for casting? primitive, tuple, string, sum, func, ...
`int(x)` `x.(int)` `x:int` 
`Point(x:10, y:20)` or `Point(var)`
`Point(int)(x:10, y:20)`
`string(x)`
`OptionalInt(x)`
Maybe using `type(x)` notation is not really good because it's confusing with function call.
`x.(type)`
`x%type`
`{x:10, y:20}%Point(int)`
`Point(int)({x:10, y:20})`
`{x:10, y:20}.(Point(int)).process...`
`{x:10, y:20}.(Point(int)).process...`

Y - What if user wants to write a function with the same name as a type?
`func bool(x:int)`
`func string(t: Record)`
We can disallow that.
What about casting to string? maybe `toString` function.
There is no built-in casting. If you want to cast MyRecord to Customer, write a custom function for that.

Y - What about `%` for casting to make it super explicit?
`%int(x)`
`%string(x)`
`%OptionalInt(x)`
`%Point(var)`
`%Point({x:10, y:20})` --cast a tuple literal
`%Point(x:10, y:20)` -- cast an exploded tuple
`%Point(@t)` same as `Point(t)`
`%Point(int)(x:10, y:20)` -- casting combined with type specialization
then user can write functions with name of built-in types. one less rule.

Y - The notation for specialization is a bit weird. Can we make it more readable?
`type ShapeTree := Tree(Shape)`
`push(int)(intStack, intVar)`
`Stack(int)`
Alternatives:
`Stack<int>` - more intuitive and familiar
`push<int>(...)`

Y - Note: you cannot use `<>` notation when declaring a function or a type.
Only when you call a function or refer to a type.
`func push(Stack<!T>)` is refering to a type.

Y - Can we do the same specialization that we have for tuple, for functions?
```
type Stack := !A[]
func pop(x: Stack<!A>)->!A ;notation is same as type definition, we use !X or any type name.
;when we want to call push, we can specify value:
var t = pop<A:int>(intStack)
var t = pop<int>(intStack)
;for push
func push(x: Stack<!A>, y: !A)
push<int>(intStack, intVar)
push(intStack, 10)
```
This needs more typing but is more readable. And provides some level of generics.
What is exact explanation about this? 
What changes does it mean?
1. Function declaration is like type declaration. You can use any type and also use `!T` notation to simplify.
2. Function call: `functionName<A:B, C:D>(input1, input2)`
This will re-create the function and replace types with given types and make the call.
It depends on the implementation, maybe compiler just adds type checking.
question: How do we define a length function for Stack? (supposed to work with all stacks)
solution 1: use this, so parameter can actually be subtypes.
`func length(this: Stack)->int`
solution 2: define it as generic. user needs to specify type
`func length(x: Stack<!T>)->int`
`var y:int = length<int>(intStack)`
what if we have this?
`func push(x: Stack<!A>, y: !A)`
`func push(x: Stack<int>, y:int)`
if we call `push(a,6)` and `a` is `Stack<int>` which one will be called? Of course second one because there is full match.
if we call `stack<int>(a, b)`? still the second one should be called. because compiler wants to re-create `stack` using `int` but notices it is already defined. So just makes the call to the existing one.

Y - in order to increase readability, limit templating only to arguments marked with `!`.
Then, how can we limit it?
`!X` where type is single capital letter is reserved for general generics.
Other than that you can write `!MyType` to mean that this parameter must inherit from mytype.
```
type Map<K,V> := K => V
type Stack<T: Customer> := T[]
func push<T>(s: Stack<T>, x: T)
func push<int>(s: Stack<int>, x: int) ;specialization
func pop<T>(s: Stack<T>) -> T
func len<T>(s: Stack<T>) -> int   ;general function for all instances
var t : Stack<int>
var h : Map<int, string>
push(t, 10)
var y = pop(t)
x = len(t)
```

Y - is it possible to define a generic function which has no generic input?
`func process<T>(x: int) -> T`

Y - explain full method dispatch flow.
steps, options and choices.
named arguments, ref parameters, multiple hierarchies, generics, this parameters, primitives, array, hash.
first option: full match with dynamic
second option: this parameter, subtype match
last option: static type full match.
- Suppose that there is a call to function `f` with 3 input arguments. Here is the method dispatch process:
1. CL := find all functions with name `f` which have 3 inputs.
2. If inputs are named: remove from CL where there is name mismatch.
3. If there are `ref` inputs: remove from CL where there is ref mismatch.
4. DT1, DT2, DT3 = dynamic type of 3 arguments specified in the call.
5. find x in CL where type of parameters are DT1, DT2, DT3
6. If found one, call `x` and finish. If found more than one -> Error and finish.
7. for x: CL where name of one of parameters is `this`:
    7.1. T := type of this parameter
    7.2. AT := type of corresponding argument
    7.3. if AT is T or T's child, add `x` as a final candidate.
8. If there is only one final candidate -> call, if there is more than one -> Error
9. ST1, ST2, ST3 := Static types of 3 arguments
10. find x in CL where type of parameters is exactly ST1, ST2 and ST3
11. If found one -> call, if not found or more than one found -> Error

N - With new method dispatch mechanism, how does it affect subtyping rules that we have?
for function, tuple, sum, ...

Y - If we assume named and unnamed types are different, `Stack<int>` and `int[]` will be two different things.
- Named types are types defined with `type` statement.
- Underlying type of unnamed types is themselves.
- Underlying type of a named type is the underlying type of what comes on right side of `:=` in its declaration.
- two variables declared with the same named/unnamed type have the same type. 
- Two variables declared with two similar looking named types have different types.
- Assignment of variables with similar looking named types to each other is forbidden.
- Assignment of variables with same named/unnamed types is allowed.
- Assignment of variables of same unnamed and named type is allowed.

Y - simplify subtyping rules. it makes no sense to define subtyping for a hash or array or function.
And how does it related to anything/nothing.
`func process(x: int|string|float)`
You can pass int or string or float or `int|string` or `int|float` or `string|float` variables to it.
We have 7 kinds of type: tuple, union, array, hash, primitive, function.
Subtyping is only defined for tuple and sum types.
- Tuple: C=(C1,...,Cn) and S=(S1,...,Sm) if Ci<:Si and n>=m and if both have named fields, they must match

N - We need to add built-in concurrency (goroutine and channels)
Added to ToDo

N - Can we simplify subtyping even more?
Can we write `var x: Shape = createShape`?
Assignment: `a=b` is valid if b is subtype of a. -> not in go
for non-tuple this is not applicable.

N - The important thing about Go is that you cannot assign a type to one of it's anonymouse embedded types. You can only cast to the interface type.

Y - In Go you cannot cast from child (Circle) to Shape.
If a method needs parent, you cannot send child. You must write `method(child.ParentType)`
But you can access the embedded field.
You cannot have: `var x: Shape = Circle{...}` if Shape is not interface.
If Shape is interface you can have: `var x: Shape = createCircle`
if you call method `show` on Circle and there is no such method, `show(Shape)` will be called.
If Child embeds Parent and Parent has methods, child will have them too. So it can cause child to implement an interface.
When a method call on child is redirected to parent method, all subsequent calls will be made will consider parent instance and not child.
And a struct inherits from another one by embedding them. Not by including their fields. This should be explicit.
(Note that golang is single dispatch. For multi-dispatch, we still have to deal with type graph).
So only two things can happen transparently and automatically:
- Cast a type to it's interface
- Redirect a call to a type to it's embedded field
- A type cannot embed empty types.
Maybe we should change empty type to another name to be more explicit. Like `protocol`. Because it is fundamentally different.
How can we simplify this to the most?
```
interface Hobby<T> := 
{
    Speak(T)
}

func Speak(Hobby) ;base definition for Speak function. must be empty function, and arg name is not needed
type Human    ; this is different from interface. 
func Speak(x: Human) ;So Human implements Hobby
type Man := (Human, name: string)
function Speak(x: Man)  ;customize Speak for Man, if not defined, Human implementation would be called
type Woman := (Human, age: int)
function Speak(x: Woman)
type Dog 
function Speak(x: Dog) ;Dog also implements Hobby
var all: ??Hobby??[] = [%Human, %Man, %Woman, %Dog]
for(Hobby h: all) Speak(h)
```
Problem: Matching Speak with Speak defined in Hobby is difficult! In golang it has receiver which we don't have here.
Haskell does not have subtyping.
We want a powerful, expressive and simple mechanism.
Maybe now that we have generics, we can work on this.
- Purpose 1: To be able to treat different types the same. So: `loop(x: shapes) draw(x)`
- Purpose 2: Reuse existing code. So `process(myCircle)` will call process for Shape if circle does not have it.
- Purpose 3: Implicit casting, `var x: Shape = createCircle` or `var x: Shape[] = ...`
p2 can be achieved rather easily. If each type specifies it's parent type (anonymous field in tuple), this redirection will be done automatically. Nothing else needs to be done. Issues like diamond or ... should be handled with error. If function has multiple inputs, this will be handled according to current dispatch policy: dynamic match, this, static match.
P1 can be achieved using generics? Not simply. `draw(x)` where `x` is Shape but contains a Circle!
If we include notation for embedding and rule of redirect (if argument name is this), we can achieve p1 and p2.
The protocol means it will have children. So it must be a `this` argument in functions. 
We do not need a protocol when we define data types. We just need it when we define variables or call functions. 
Haskell way: `type Shape := Circle | Square | ...`
But this does not let us extend supported types.
Maybe we can have: `func draw<T: Shape> (...)` but we dont need generics for this.
we want to have a variable, static type=Shape, dynamic type=Circle, call `draw` and `draw(Circle)` gets called.
This is Ok I think.
We want to have a variable of type `Shape` and assign it a variable of type `Circle`. or return type of function is Shape and we want to return a Circle.
We can simuate return and call with variable assignment. `return x` where return type is T, means `var t: T = x; return t` same for function call and argument passing.
- In Go, you cannot extend or change an already defined interface.
question is: How can we define and use common behavior of a set of types?
BUT we are not supposed to attach behavior to data! This is what attracts us to OOP.
This contradicts with method dispatch, multiple inheritance and typing because it is against every other things.
What's wrong with empty type? Problem is we want to bind type with behavior. 
Type is one thing, behavios is multiple things. Creating this bind is ambiguous and confusing.
```
type Shape
func Draw(Shape, int)->int

type BasicShape := (x: string)              ; a normal type
func Draw(x: BasicShape, y:int)             ; Now BasicShape is compatible with Shape type

type Circle := (BasicShape, name: string)   ; another type which inherits from BasicShape
func Draw(x: Circle, y:int)                 ; It has it's own impl of Draw which hides Human implementation

type Square := (BasicShape, age: int)       ; another type which embeds BasicShape.
;calling Draw on Square will call BasicShape version

type OtherShape
function Draw(x: OtherShape, y:int)         ; OtherShape also implements Hobby

var all: Shape[] = [myBasicShape, myCircle, mySquare, myOtherShape]
for(Hobby h: all) Draw(h,1,"")
;implicit casting to empty type. Compiler can check if this is a valid casting or no.
;compiler can make sure that all currently defined empty functions which accept Shape
;are defined for MyOtherShape too
var t: Shape = myOtherShape
var r: BasicShape = myCircle ;automatic casting - because Circle inherits from BasicShape
```

Y - What does `%X` without paren mean?
It can be used to create a default instance (every field 0 or empty)

Y - If we can write: `var x: BasicShape = myCircle` then we should be able to call a function like:
`func process(x: BasicShape)` -> `process(x)`
But we have said that only `this` parameter can be dynamic typed.
I think it's ok. Because assignment is one thing, but function call is different.
When I assign, I am changing static type which affects method dispatch.
```
func process(x: BasicShape)
func store(this: BasicShape)
var myCircle: Circle = ...
process(myCircle) ;this will fail
store(myCircle) ;will work
var bShape: BasicShape = %BasicShape(myCircle) ;you need to cast
process(bShape) ;this will work because static type has changed
```
Its better to force explicit cast everywhere. Is is more straight forward and readable. 
When we cast a variable to an empty type or a super-type we change it's static type. But it's dynamic type does not change. 
NO! this was supposed to change both static and dynamic type!
A cast, will change both static and dynamic type of a variable.
I argument name is `this`, you don't need a cast if type is subtype of expected input.
So how is this supposed to work?
```
var all: Shape[] = [myBasicShape, myCircle, mySquare, myOtherShape]
for(Hobby h: all) Draw(h,1,"")
```
We can say, dynamic cast to empty should be possible. Empty type is similar to golang's interface to which you can cast implicitly. 
Can this replace `this` rule? No. Because it enforces a single dynamically typed argument.
`this` rule is kind of an exception. Let's remove this exception and instead make method dispatch process more complete.
1. Dynamic full match
2. Static full match
So, suppose `Circle` embeds `Shape` and we have `draw(Shape)`. calling `draw(myCircle)` should redirect to draw for Shape. 
Problem is we have multiple dispatch and multiple inheritance and we do not want to have multi-methods.
Why bother to provide support for implicit cast? If you want to call draw for Shape and you have a Circle, just call `draw(x.Shape)`. But this won't be easy to change: If we later add support for Circle draw, we need to update this code. 
But instead language will be more readable and understandable.
Can we totally remove the concept of dynamic type?
No. We already have `anything` and empty types.
I think the most important issue is escaping from multiple dispatch.
- Seems the best solution is to have function for exact parameter combination that we have. 
- This means no big change about empty types. They specify required implementations that a type has to satisfy.
- But the implicit conversion of Circle to Shape (embedded type) when it is being used as a function argument won't work. There must be a function for Circle (even if it redirects to another function).
`func draw(x: Circle) -> draw(x.Shape)`
`func paint(x: Circle, y: Color, z: BaseProcessManager) -> paint(x.Shape, y, z)`
We need to manually write rules of method dispatch. But this means a lot of task.
Two options are available to reduce it:
1. `this` argument
2. Generics methods
Both of these need change the original function which may not be possible.
`type Circle := (Shape, r: float)`
`func draw(s: Circle, c: Color)`
`func draw(s: Shape, c: SolidColor)`
`draw(myCircle, mySolidColor)`
- **We can say we have single inheritance for implicit dispatch. The first field of a tuple (if anonymous) is it's implicit parent**  .
- So in method dispatch, the function which matches with max number of argument dynamic types is selected, if there is only one.
- NO! **The only variables that their static and dynamic can be different are empty types.**
- When Circle embeds Shape, all functions defined for Shape are implicitly re-defined for Circle unless they are dfined by developer. These re-defined functions, just cast Circle to Shape and call Shape function. So, in those functions, they receive something which has static type and dynamic type set as Shape. So if we had `draw(x: Shape, y: Color, z: Canvas)` the compiler will add `draw(x: Circle, y: Color, z: Canvas) -> draw(x.Shape, y, z)`.
- If Circle implements methods of two interfaces, you can assign a circle to any of them. They will keep track of their dynamic type.
- The last level in the hierarchy is set of interfaces that the type is implementing.
`Square -> Polygon -> Drawable -> Shape -> Object -> Comparable, Iterable, Serializable`
when there is a call `process(x)` and `x` is a Square, it embeds Polygon, Drawable, Shape and Object.
So these candidates will be looked up: `process(Square) -> process(Polygon) -> process(Drawable) -> process(Shape) -> process(Object)`. If none of them is defined, we will look for process functions which accept any of interfaces. If found one-> make the call, else error.
Same for multiple arguments. The candidate which matches with max number of dynamic types will be chosen. If multiple candidates-> error.
- But: how can we re-write those methods if they will have multiple inputs of different types? There cannot be an implicit re-write of methods. So seems that this dynamic/static type separation should exist in all other types. Because if a method is not defined for Circle, but best choice is the one for Shape, we implicitly do the cast but the dynamic type will be Circle. So all variables of type tuple, can potentitally have separate dynamic type.
when we see a call like `process(myCircle, myColor, myCanvas, myPen)` first choice is a function that has support for dynamic type of all of these. If not, we look for all other `process` functions with 4 parameters.
if there is only one that covers 3 dynamic types -> call
if there is more than one -> error
if there is only one that covers 2 dynamic types -> call
if there is more than one -> error
if there is only one that covers 1 dynamic types -> call
if there is more than one -> error
if there is no function that covers any of dynamic types, we look for interfaces.
Each of 4 arguments has a set of interfaces that it supports. so we have 4 sets of interfaces each set for each argument.
if there is a single function that has support for I1, I2, I3, I4 where each Ii is a member of corresponding set created before, then we will call it.
if there is more than one -> error
if there is no function -> look for a function to have full match with static types, if found -> call (there cannot be multiple functions with same full match).
if not found -> error
- If you write `var s: Shape = myCircle.Shape` then static and dyn type of s will be Shape. 
- If you write `var s: Shape = myCircle` it will give error. Non-empty type cannot be dynamic.
- if you write `var s : Shape = %Shape(myCircle)` st=dt=Shape
- It is better if only empty types have different dynamic type. Because in case of Circle->Shape, maybe the Shape type has some methods that are overriden in Circle. And when Shape methods call eachother, they expect their own methods to be executed. Otherwise unexpected things may happen.

Y - Can't we just use functions instead of empty types?
What is the purpose of an interface? To define a set of behavior -> To define a set of functions.
So why not use functions themselves instad of adding this new interface concept.
example:
```
//java code
public interface Logger { public void log() }
public class FileLogger implements Logger ...
...source code
public void save(string file, lg logger) {
    lg.log("Start saving")
}
;now instead of passing the interface, here we pass a function!
func save(f: string, lg: func(string)) -> {
    lg("Start saving")
}
```
Interface is only a set of empty functions.
`type Logger := func(logText: string)`
`type Comparable := func(x: anything, y: anything)->bool`
`In other words, the signature of the function is the interface.`
What is implication of this?
No need to have dynamic/static type. 
No empty types.
But still we need embedding. But single inheritance.
And the other result: Every variable has same static and dynamic type.
So how can I have an array of shapes?
Ad-hoc polymorphism: We can have same function accept different types? No. But with single inheritance and function promotion, this can be achieved.
But to have subtyping and polymorphism, we need dynamic type!
Maybe not. Maybe there is a way.
Maybe with each different type we can have a converter function. which converts it to type we want.
`myCircle, { return $0.Shape }`
`type Shape := (name: string)`
`type dShape<T> := (x: T, converter: func(T)->Shape)` 
OR
`type dShape<T> := (x: T, getName: func(T)->string)` 
Now everything has same structure.
`var g: dShape<?>[] = [getCircle(), getSquare(), getTriangle()]`
`func getCircle() -> dShape<Circle> { return ... }`
`var s: Shape = g[0].convert()`
`process(g[0].x)` this will call `process(Circle)`
type of first element of the above array is `Shape<Circle>`.
`Shape<?>` can be used as a mechanism to have dynamic type. Because we don't know type of `?` until possibly at runtime.
Can we have `type Y : =?[]` this will be same as anything.
So we cannot deal with polymorphic data alone? They should be in a collection like array.
No! We can have this: `var x: Shape<?> = getCircle()`
This will give us single inheritance or better say, dynamic single inheritance. Circle inherits from Shape but in a limited yet flexible way. But we can write a code that gives you `Drawable<?>` based on a circle. So we define the inheritance. 
The only problem is that we are binding data and behavior again.
What if `Shape<?>` can only contain types that embed Shape in their first field?
```
type Shape := (name: string)
type SArr<T: Shape> := (x:T)
var s: SArr<?>[] = [getCircle(), getSquare(), getRect()]
var r: Shape = s[0].x
;this will call process(Circle)
;conversion will be handled behind the scene
process(s[0].data)
;normal way with dynamic type
type Shape := (name: string)
var s: Shape[] = [getCircle(), getSquare(), getRect()]
var r: Shape = s[0]
;this will call process(Circle)
;conversion will be handled behind the scene
process(s[0])
```
How can we implement `Account` interface which provides withdraw and deposit functionality?
```
func process(x: Account, wd: func(Account, int), dp: func(Accout, int))
```
Or if we have dynamic type, we can simply write appropriate functions and just call them in process.
what `process` receives can be a subtype of Account and calling methods on it, will invoke subtype methods.
problem: we cannot pass a subtype of account to process function without loosing its dynamic type.
When using `dShape<?>` what is the guarantee that the type inside `<>` embeds Shape?
`type dShape<T:Shape> := (x:T)` this is a structure which can store different types as long as they embed Shape
`var item: dShape<?>` this variable is a tuple containing `x`. Type of `x` is unknown at declaration time but we know it will embed a Shape.
`process(item.x)` this will call a process based on the actual type item.x which can be anything embedding a Shape. 
On the declaration side, methods for Circle have been automatically defined to re-route to Shape if they are not already defined.
Can't we have `var item: ?: Shape`? No! of course not. There is no `?` type in our system. `?` is just a wildcard used in templates to simulate polymorphism in a static way. 
We use this notation to define polymorphic generic: `type SA<T: Shape> := (x:T)`
Can't we use this notation in normal code to achieve same thing?
`var t: Shape = getCircle()`
We can but it will need dynamic/static type management. While generic one does not need that.
Can we simplify the generic method?
```
type Shape := (name: string)
type SA<T: Shape> := (x:T)
var s: SA<?>[] = [getCircle(), getSquare(), getRect()]
var r: Shape = s[0] ;implicit cast to Shape. st=dt=Shape
;this will call process(Circle), conversion will be handled behind the scene
process(s[0])
```
What happens next? If we call `save(x)` inside process, will it call `save(Circle)` or `save(Shape)`? Depends on the implementation.
But note that inside `process` we will not know the real type of input. It can be anything inheriting from Shape.
No! `process(s[0])` will definitely call `process(Circle)` which will receive a Circle and nothing else. 
If and only if there is not `process(Circle)` then `process(Shape)` will be called with a Shape variable and not a Circle.
So: There is no dynamic type, but in case of wildcard generic, we won't know the real type of the data. We can just call a function and the best implementation will be chosen.
And this is not extensive unless we extend it to functions.
```
func create<T: Shape>(type: string) -> T
type Shape := (name: string)
type SA<T: Shape> := (x:T)
var s: SA<?>[] = [create<Circle>("c"), create<Square>("s"), create<Rect>("r")]
var t: SA<Circle> = create<Circle>("c")
var s: SA<?>[] = [create("c"), create("s"), create("r")]
```
Can this let us remove dynamic type completely? What about other arguments?
```
type Shape := (name: string)
type SA<T: Shape> := (x:T)
var s: SA<?>[] = [getCircle(), getSquare(), getRect()]
;this will call process(Circle), conversion will be handled behind the scene
process(s[0], mySolidColor, myPen, myCanvas)
;what if we only have this: color does not match
func process(Circle, Color, Pen, Canvas)
;as soon as we define the type SolidColor embeding color, compiler creates this for us
func process(Circle, SolidColor, Pen, Canvas) -> process(Circle, %Color, Pen, Canvas)
```
I think we can completely eliminate dynamic type if compiler generates appropriate functions for us. 
1. The first field of tuple, if anonymous, is it's base type.
2. When declaring a tuple (T) with base type (B), compiler will do this code generation:
2.a. For each function f such that `func f(...x: B...)` generate `func f(...x:T...)->f(...x.B...)`
3. Each function call will need full type match.
4. Each variable has a single type: Static type. 
5. This static type can be a variable itself. This can be achieved using wildcard in generics.
5.a. `type Element<T: Shape> := (x:T)` we define a polymorphic type which can represent any type which embeds a Shape
5.b. `func create<T: Shape>(type: string) -> T` items can also be generated using generic functions
5.c. `var s: Element<?>[] = [create("c"), create("s"), create("r")]` we use `?` to represent any type.
5.d. When you call a generic function which only needs generic type for output type, you can eliminate type literal, if output is bound (`<T: Shape>`).
`var t = create("c")` what will be type of t? it will be Shape.
`var c: Circle = create("c")` this will give compiler error because compiler cannot verify output if Circle.
`var c: Element<?> = create("c")` type of c will be `SA<Circle>`.
This notation is not intuitive. Specially item 5.c. Right side is not compatible with left side.
`var s: <T: Shape>[] = [create("c"), create("s"), create("r")]` 
then can we write:
`var t: Shape = create("c")`?
Yes because create output is compatible with shape. But you will have a shape not a circle.
If you want to keep the actual type:
`var s: <?: Shape> = create("c")`
This is confusing. Let's have dynamic/static type everywhere and base it upon single inheritance.
1. The first field of tuple, if anonymous, is it's base type.
2. When declaring a tuple (T) with base type (B), a variable of type T can be implicitly treated like a B.
3. When a function call like `f` is done with 3 arguments, best match is full dynamic type match.
4. If that is not found, runtime will look for a candidate which supports maximum number of dynamic types.
5. If failed, full static type match will be checked.
And if we call Shape-bound functions with circle instance, they will receive a variable with static type of shape and dynamic type of circle. So if code makes another call, dynamic type will be circle.
Fragile base class exists because data and behavior are bound together. Here they are not. So there is no "base" "class".
`var c: Shape = createCircle` st=Shape, dt=Circle
Now that it must be first field and must be unnamed, can we remove these rules implicitly?
`type Circle := (Shape, ...)`

N - SOLID
S: single responsibility
O: open for extension closed for modification
L: ubstitution
I: interface segregation
D: dependency inversion

N - This is in F#. Can we have it?
`[1..10] |> List.map add2 |> printfn "%A"`
`[1..10] |> map(add2, $_)  |> printf("%A", $_)`
`[1..10].map(add2, $_).printf("%A", $_)`
`sort.map(array)` -> map(sort(array))
`array.map.sort` -> sort(map(array))

Y - Force that a generic function must use its type in inputs.

Y - Let's remove bound limit for generic type. Because if we allow, we should allowe generic bounds which makes things so complicated.

N - Even if you need an interface with a lot of methods, you can define a type to contain those functions rather than passing each function separately to the code.

N - maybe we should remove the rule for `a.f(x)` ~ `f(a,x)`

N - So what do we do with anything and nothing?

Y - Nothing has not value. So how can we assign value to a variable of type nothing?
The only way to get nothing, is to run a block which does not throw exception: `{}`
`var g: nothing = {}`

N - Is this correct?
`func printName(x: (name: string))...`
Any data type that contains a string name can be passed to `printName`.
No! This will mean multiple inheritance.
`func process(x: (name: string))...`
`func process(x: (age: int))...`
`type Data := (name: string, age: int)`
`process(myData)` which one should we call?

N - implement an account withdraw and deposit and another account of type supporting overdraft.
```
type Account := (balance: int)
func withdraw(x: Account)
func deposit(x: Account)
type OverdraftAccount := (Account, limit: int)
func withdraw(o: OverdraftAccount)
func deposit(o: OA)
func process(Account, Employee)
;I want to make sure that any function for Account, is supported for ODA too
func process(OverdraftAccount, Employee)
```

Y - How can we define map function?
`func map<T, S>(arr: T[], f: func(T) -> S) -> S[]`

N - Another type of subtyping: a tuple with original type and some convert functions.
```
type Shape := (name: string)
type Circle := (r: float, s: Shape, aaa: func(Circle)->Shape $0.s)
```
You can define any number of "implicit conversion" functions. This is very flexible. 
We can have multiple inheritance and ....
How does it make method dispatch simpler?
How does it enable us to create an array of shapes with circles and squares?


N - How can we define strategy design pattern? using a function pointer.

Y - if a function expects `f: func()->Shape` can I send a function which returns a cat? Yes.
if a function expects `x: Stack<Shape>` can I send `Stack<Circle>`? No

N - The bad thing about empty type is that we are using it as a way to describe behavior using data. 
pure data -> types
behavior+data -> functions
pur behavior -> empty functions.
The idea of empty type seems logical and orth and gen.
Maybe we should clarify it or make it more explicit.
`type Countable := anything`
`func count(Countable)->`
`type Array := int[]`
`func count(x: Array) -> len(x)`
`func process(f: Countable)`
`process(myArray)`

N - Is this possible to replacec vowels with x in a string? yes
`var t: string = 'this is a test'`
`var g: string = loop(c: t) if ( isVowel(c) ) then 'x' else c`

N - Adding built-in linked-list. This can be used to create stack, queue and other data structures
`var t:[int] = [1, 2, 3]`
Like D we can use `~` opertor to concat two arrays. 
Methods that we need:
- Set at specific index: t[0] = 112
- Clone: explode
- Clear: t=[]
- Add to beginning: `t = [1, @t]`
- Add to end: `t = [@t, 10]`
- ToArray -> clone to array: `var y: int[] = [@t]`
- From array: `var t: [int] = [@arr]`
- Other using functions: sort, search, delete, insert at index, length
- No slice, because this is a linked-list.
It will be confusing with array. 

N - How to get a list of word count per line of a text?
`lineLengths = map (length . words) . lines -- Haskell`
`var length = loop(l: text.lines()) l.words().length()`

N - A lazy load variable (scalar, array, hash)
For example an array which is bound to a file. 
When you iterate through array, it reads from file.
It won't read until you really need to.
Or a hash which is bound to a DB table. key is mapped to PK and value to something else.
Advantage: Being lazy. We don't need to read all the data.
We have a function which calculates something on the input array. We want to do the calculation on a file.
But we don't want to read it all into an array.
Can this be solved with custom `[]` operators? It is one way but it won't be a native array.
`func process(x: int[])`
I want to pass a function instead of `int[]`, but it will behave like an int array.
- Can I define a tuple which embeds an anonymous int array? 
`type MyArr := (int[], fileHandle: int)`
`type MyH := (int=>string, ...)`
`func getIndex(x: MyArr, y: int) -> int`
`func setIndex(x: MyArr, y: int, z:int)`
`func getValue(x: MyH, key: int)->string`
`func setValue(x: MyH, key: int, value: string)`
Also I can embed primitives:
`type MyInt := (int)`
But there should be not such thing for set/get values. Because it will make things complicated.
Can't I just write:
`type MyArr := int[]`
`type MyH := int=>string`
`func getIndex(x: MyArr, y: int) -> int`
`func setIndex(x: MyArr, y: int, z:int)`
`func getValue(x: MyH, key: int)->string`
`func setValue(x: MyH, key: int, value: string)`
But if a function expects int array: `func avg(x: int[])` can I send MyArr instance to it?
I think the named-type solution makes more sense than embedding.
We should state embedding is only possible for tuples.
What if what I have is not an array? What if is it a file handle and array elements are supposed to be file lines?
We want to keep behavior separate from data. 
This seems to only be applicable to hash and array. But to keep gen and orth we may want to extend it to "ALL" types.
What about using functions? `func s()->int` can be used instead of an int. But here it is different.
The `type MyArr := int[]` solution does not make sense. Because we will need some additional data.
We need embedding to be enabled for non-tuple types.
`type MyArr := (int[], fileHandle: int)`
`var t: MyArr = [1,2,3]`? It will be confusing if we embed non-tuple fields.
`var FH := (fileHandle: int)`
Example: A lexer, we want to treat a file as an array/list of tokens. But actually each token is parsed from file bytes. 
But this example is not good. Array is supposed to provide random access. This is not compatible with tokenizer.
We want an array/hash which is a proxy in front of a set of functions.
`type MyArr := (int[], fileHandle: int)`
`type MyH := (int=>string, ...)`
`func readValue(x: MyArr, y: int) -> int`
`func writeValue(x: MyArr, y: int, z:int)`
`func getValue(x: MyH, key: int)->string`
`func setValue(x: MyH, key: int, value: string)`
What if user writes `getIndex(x: int[] ,y:int)`? Can he re-write built-in features?
He shouldn't be able to.
`var t: MyArr = [1,2,3]` this will call setIndex 3 times?
Array is a fixed length data structure.
Can we combine two functions and make the result seem like an array?
`var x: int[] = (^set, ^get)`
There are two approaches to this problem: Handling at type level (define a specific type and functions for that), Handling at data level (define a normal variable then bind it to functions).
The type-level is more complicated but more consistent with current notation, more readable
Data-level: We possibly need to add new notation, but it will be simpler, more difficult to debug and trace.
Data-Level: When you declare an array, hash or ... you can tie it to a set of lambdas. These lambdas will be invoked when data is read/written from/to the variable.
But how can we force this syntax to only be applicable to array and hash and at the same time, keep language gen and orth?
Embedding an anonymous array or hash, is totally complicated and unreadable.
`type MyA := (arr:int[], fileHandle: int)`
`type MyH := (h:int=>string, ...)`
`func readValue(x: MyArr, y: int) -> int`
`func writeValue(x: MyArr, y: int, z:int)`
`func getValue(x: MyH, key: int)->string`
`func setValue(x: MyH, key: int, value: string)`
We need 3 things:
1. Define type X
2. Declare that type X should be treated like `int[]`
3. Define code to handle get/set operations
If we do it in data-level, we need these:
1. Define a variable of type `int[]`
2. Assign lambdas to that variable
All of these can be done with normal types. Without need for special notation.
Java/Go don't have this. C# has only `this[]` function which acts like indexer.
This will make things more complicated: Things like slicing? or ref?
Advantage:
- Proxy read/write data and change the behavior
- Watch for change in a variable: observer design pattern
The only use case for this is when we have a very large volume of data.
We can simulate iteartion by writing custom iterator data and functions (e.g. reading tokens from a file).
read/write behavior can also be simulated using custom data types. 
The only problem is interoperability. If an existing function expects an `int[]` but we have `DataList` type.
Can we simply write `var t:int[] = %int[](myDataList)`?
This is data-level, not type level but has minimum effect on the notation. Actually no new notation is needed.
The only requirement is in convension. To be able to cast some data to `int[]` there must be a set of functions defined for that data type.
Advantage: min change in notation, general and orth, you can do it for any other type.
`var t: int=>string = %int=>string(myCollection)`
Now you can write any data type you want, provide required functions and simply cast it to an array.
What about primitives?
`var t: int = %int(myInteger)`
Disadvantage: This will affect the runtime performance. We no longer can cast `arr[0]=1` to a simple memory copy function.
If we are able to determine data type at compile time, it will make us generate more efficient machine code. 

N - If we can replace `int` with `func()->int` then what about `ref int`?
You can only `ref` a variable which is not func. 

N - A hash is simiar to a function. 
`int => string`
`func get(key:int) -> string`
`func set(key:int, value: string)`

N - Missing features: Cost of not having, alternative
- Lazy evaluation: Provided to some extent using lambdas more is not necessary
- Built-in linked-list: Use a normal type
- Virtual array/hash: iterator, use custom types.
Virtual hash/array: If they are read-only they can be simulated with a function:
`string[]` ~ `func getData(index:int)->string`
`int=>string` ~ `func getData(key: int) -> string`
And to process a large volume of data, we usually need only to read these data.
Instead of any new notation, the function can accept a func.
Simulating an array using a function makes language more complicated and less orth.

N - Maybe we can use `implicit` to support caching. No.

N - Data and behavior should not be bound together. This means either we should specify data we need (`func work(x:int)`) or we should specify the behavior we need (`func isInArray(x:Eq!T, y:Eq!T[])`).

N - Shall we have `var` and `val` like in scala?

Y - Maybe we should allow any name for generics. As we extend their usage, we will need more meaningful names for them.

Y - State that embedding is not limited to one. But the first embedded field is parent type.

N - Like Scala use `[]` for generics?
It is already used for array and hash.

N - should we mark implicit definition function with something?
Scala: `implicit object NumberLikeDouble extends NumberLike[Double]`

N - In scala even array and hash are classes, not built-in.
Disadvantage: more typing
Advantage: We can use `[]` for other purposes.
But we cannot make array and hash, not built-in because it will make syntax complicated.

N - What about `ThisOrThat` naming for functions?

Y - Go has interface, Clojure has protocol. Haskell has type class.
Advantage: Compile time check that the type satisfies some pre-requisites.
Go: You can write an interface which an existing complies with that.
Actually interface is a set of functions.
So the method can accept those functions.
For example an interface to a car (start, stop):
```
type CarInterface := (start: func(), stop: func())
```
Advantage: This can be combined with templates
Usage:
```
func drive(x: CarInterface) -> ...
...
drive({start: ^start, stop: ^stop})
```
We really dont need duck typing for interfaces as Go offers them. Because we do not have objects and interfaces.
```
type Eq<T> := func(a:T, b:T)->bool
type Point := {x:int, y:int}
func ???(a: Point, b: Point)->bool { ... }
func isIn<T>(a: Eq<T>, b:T[])->bool
```
A type that embeds another type, inerits from that.
A type that embeds another type-class, supports provided functions.
type-class is a type itself. When we define type of an argument as a type-class, it means that type supports those functions.
There are these things:
1. Declare a type-class type with it's functions (e.g. Eq)
2. Implement a type-class for a normal type (e.g. Point)
3. Define argument/variables of a type-class
Actually there are two ways to specify argument type: Data type (it has to have these fields), or type-class (it has to support these functions). For example when I want to check if an item exists in an array, I just need support for equality check. I don't care about what data it has. So type-class is something simiar to type but separate from them. Each variable can have a type and a type-class.
type-class defines the behavior that a type should have. But why we cannot mix data members and behaviors?
In Haskell you can define functions of a type class based on each other (== and !=). And they can inherit from each other.
For example in loop (if it was a function) iteration, it would expect a type that supports iterator.
In Haskell, either we need a specific data type, or we need a generic data type with specific behavior.
If we use a specific data type, the behavior is already specified. So this can apply to generics.
This is similar to constraints in Generics: 
`public class MyGenericClass<T> where T:IComparable { }  `
It is used to constraint a generics type to support a specific set of functions.
A type class is a special type which describes behavior on it rather than date.
```
;Also we can initialize tuple members, we have embedding
;Note that if T is a sum type, each function here can be multiple implemented functions
type Eq<T> := {
    equals: func(x: T, y:T)->bool
    notEquals: func(x: Eq, y:Eq) -> bool = !equals(x,y)
    ;if any expected function does not have an input, we can declare it as a data field
    constValue: T ;will be calculated by calling constValue<T>()->int
}
;we can also use non-generic types to be later used as implicit but its not very useful
;it can be used to have a handle to a function without method dispatch rules (exact match)
type Writer := {
    write: func(x: int, y:string)
    PI: func()->double
}

type Point := {x:int, y:int}
func equals(x: Point, y: Point)->bool { ... }
func notEquals(x: Point, y: Point)->bool { ... }

func isInArray<T>(x:T, y:T[], implicit z: Eq<T>, implicit g: Writer) -> bool {
    if ( z.equals(x, y[0])...
}
---
type Ord<T> := {
    compare: func(x:T, y:T)->int
}
func sort<T>(x:T[], implicit z: Ord<T>)
---
type Stringer<T> := {
    toString :func(x:T)->string
}
func dump<T>(x:T, implicit z: Stringer<T>)->string
---
type Serializer<T> := {
    serialize: func(x:T)->string
    deserialize: func(x:string)->T
}
func process<T>(x: T, implicit z: Serializer<T>) -> ...
---
type Adder<S,T,X> := {
    add: func(x: S, y:T)->X
}
func process<S,T,X>(x: S, y:T, implicit z: Adder<S,T,X>)->X { return z.add(x,y) }
---
type Failable<T, U> := {
    oops: T<U>,
    pick: func(T<U>, T<U>)->T<U>,
    win: func(U)->T<U>
}
type Maybe<U> := U | Nothing
func oops<U>()->Maybe<U> { return Nothing }
func pick<U>(x: Maybe<U>, y: Maybe<U>)-> Maybe<U>
func win<U>(x: U) -> Maybe<U> { return U }

func safeDiv<T>(x: double, y: double, implicit z: Failable<T, double>) -> T<double> {
    if ( y == 0 ) return z.oops
    return z.win(x/y)
}
;when calling above function, you must provide type of function
var t = safeDiv<Maybe>(x, y)

---
type Factory<T> := {
    create: func()->T
}
func create()->int { return 5 }
func create()->string { return "A" }
func generalCreate<T>(implicit z: Factory<T>) { return z.create() }
;here we have to specify type because it cannot be inferred
var r = generalCreate<string>()
var y = generalCreate<int>()
---
;you can define primitives as implicit
func process(implicit item: int)
this will invoke `func item()->int` to provide value for this argument.
```

How does it affect subtyping and ref? and method dispatch?
- `ref` cannot be combined with `implicit`.
- implicit arguments are just like normal arguments but optional. You cannot have `func(x, implicit y)` and `func(x)`
- implicit must be at the end of argument list: We can say whtever comes after implicit is implicit.

N - Scala has `sealed` to signal a trait cannot be extended.
Similarly, we may have a function which we don't want to be defined elsewhere with children types.
Which means, children are not supposed to override this function.
`func process(x: Shape, y: Color)`
But what does `children` mean here?
Suppose that `process` is doing some important things. 
It does not make sense to stop this. Because functions are free out there. Everyone can write or call a function.

N - How can we provide set for hash using generics?
`func put<K,V>(x: K, y: V, m: Map<K,V>)`

N - Make sum type similar to Haskell?
`data ToyOrd = Smol | Large Int`
`data Maybe a = Just a | Nothing`
`type Maybe<T> := T | Nothing`
For example returning U when type is `Maybe<U>`:
`func win<U>(x: U) -> Maybe<U> { return x }`
In Haskell we write: `win a = Just a`

N - If I have `func process(Circle)` and inside I want to call `func process(Shape)` can I do it without casting?
`var s: Shape = myCircle;`
`process(s)`
You have to cast: `process(%Shape(myCircle))`

Y - If you need to point to parent function:
`func process(Shape)`
`func process(Circle)`
inside second we want to invoke the first one. 
we can write `process(%Shape(myCircle))`
What if dont want to cast? We may want to pass a circle.
Solution: Get a function pointer to that specific function.
`var fp: func(Shape) = ^process`
`fp(myCircle)` this will invoke process(Shape) with a circle.
Maybe we can replace implicit with this?
But then it won't be explicit enough and compiler cannot check it.

N - How can we remove `ref`?
We can define specific types and mark those types as mutable. So anyone that receives them can edit them.
`type MutableIntArray = int[]`
Maybe if we can have Monads, we can have mutable data?

N - implicit and ref are not consistent with each-other and they are not orth.
Can we make them orth? Merge them or change them in a way they can be compatible.
Maybe we can replace mutable data structures with built-ins? Like Scala `scala.collection.mutable`.
`ref` is not a big deal for primitives. Its only important for array/hash and tuples (stack, set, ...).
So if type of function input is `MutableArray` then it can be mutated.
Maybe it is worth. In return we can get rid of `ref` keyword.
We can even provide built-in features for this.
Scala solves this by defining a class with `var` fields. But we don't have classes.
We can provide a magic built-in function which gives a mutable reference to a function argument.
`func process(x: int[])`
If everything becomes a type, we can have mut and imm types.
Then it will be a matter of specifying types.
```
type Array<T> := native
type Map<K,V> := native
type MutableArray<T> := native
```
It would be good if we could provide this with functions.
This could be a use case of `implicit`. We are expecting to have types that have getter/setter functions.
`type editor := { set: func(x: int[], i: int, v: int) }`
`func processList(x: int[], implicit z: editor)`
If no parameter is passed for `z` compiler will generate these methods? to set array element.
But what dictates the behavior of this function?
- the parameter which is named `this` can be mutated.
`func processList(this: int[])`
types of mutation: `x=10`, `x[0]=5`, `x[5] = "A"`, `x.f=???`, ...

N - Mutability?
```
func createMap(k: int, v: string) -> int=>string { ... }
...
var y = 10
var t = createMap(y, "A")
y++ ;will this change key inside the map?
```

N - Maybe we should push some things which are expected to be done in code to declaration scope.
For example `assert T :: Num`.
Or maybe we should push them to code, if compiler can check them at compile time.
`func process<T>(k: T, ref v: T[], implicit z: Eq<T>) -> int=>string`

N - Should we explicitly indicate what protocols does a type cover?
`type Customer := {name: string} deriving Show`
`type Show<T> := { func toString(T)->string }`
But what if I want to write my own protocol and an existing type?

N - Both in Haskell and F# they have label syntax for sum type.
```
;Haskell
data DivisionResult = DivisionByZero | Success Double
safeDivide x y =
    if y == 0
    then DivisionByZero
    else Success (x / y)
;pattern matching
divisionToString r = case r of
    DivisionByZero -> "Division failed"
    Success x      -> show x
```
```
;F#
type IntOrBool = I of int | B of bool
let i  = I 99 
let b  = B true
match x with
  | Tup (x,y) -> 
        printfn "Tuple matched with %i %i" x y
  | P {first=f; last=l} -> 
        printfn "Person matched with %s %s" f l
```
```
type DivResult := Error | x:double
func div ... 
...return DivResult.Error 
...return DivResult.x{12.1}
x :: {
    DivResult.Error -> ... ,
    DivResult.x
}
```

Y - replace implicit with auto.
It is easier to read and shorter and still makes sense.

N - Scala has `view`. Can we use it to make `CustomersList` look like an array?
Or maybe to make something mutable.
When we create a view (maybe choose a better name), we provide a set of functions to work with it. These function can be used to read/write data.
Use cases:
- Provide mutable function argument
- Providing a normal array which behind the scene works with a file to provide data
- Property
Objetives:
- To provide a live conversion of a type to another (bi-directional)
- Make something modifiable
- Make something look like something else 
- Provide read-only/read-write access to some inner variable
we can use `$$` to refer to the inner variable.
Other names: wrapper, package, envelope, box
- We need a keyword for this.
For: primitives, array, hash, tuple, union, function
```
var t: int = 12
type box<T> := T
;result of `box a:b` is box<b>
;if a is a local variable or a boxed argument, you can read/write
;but if a is a normal argument, you can only read

;s is a reference to a string. You can use it just like a normal string or a &string.
var t:int
var s: box<string>
;you can make s a proxy with a set of pre-defined closures to simulate read/write
s = box t >> string {
    ;these are closures and have read-access to local variables + read/write acces to the variable t
    get: func() -> string  { return toString(t) },
    set: func(s: string)   { t = toInt(s) }
}
s="12"  ;calls set("12") which will make t=12
s+="1"  ;will make t=121
s="A"   ;throw error
func process(x: &int) { x+= 10 }

var y: string = s ;valid, but type of y is string not string&
process(y)   ;process will have read-only access like a normal argument 
process2(s)  ;process2 will have read-write access if argument is defined as string&
;you can pass int or int& when an int is expected. but if int& is expected, you cannot pass an int.

;if a function refers to argument as string& it can write to it like a local string variable
;or it can use string and just read from it.
;we can use a string& just like a normal string
s = "12"
print s + "Hello" ;prints 12Hello
print t ;prints 12

var s: &string[] =  
{
    get: func(i: index) -> string ...,
    set: func(i: index, s: string) ...
}
?????
var s: string=>int = 
{
    get: func(key: string) -> int ...,
    set: func(key: string, value: int) ...
    ;maybe clear?
}
type Customer := {name: string, age:int, data: int[]}
var s: &Customer = 
{
    name: { get: func() -> string ..., set: func(value: string) ...},
    age: { get: func() -> int ..., set: func(value: int) ...},
    data: { get: func(i: index)->int ..., set: func(i:index, value: int)...,
}
var s: box<int|string> = box t:int|string 
{
    get: func() -> int|string  ...
    set: func(s: int|string)   ...
}
var s: box<func(int)->string> = box t:func(int)->string
{
    invoke: func(int)->string 
}
;as a shortcut, when type of source and target are the same, writing functions is optional
var tt: int = box t:int  
var tt: box<int> = box t:int  
```
- What if the structure is complex (tuple containing arrays and other tuples and ...)?
- If we refer to something fixed in the get/set functions (box functions), we can re-use functions.
- To make this more consistent and orth, we must make the value of box, more similar to normal variables. It should be a simple tuple literal.
- Maybe replace `t:int` with `t>>int` to make distinction with argument definition
- Even if a function receives a normal var, it can use `::` to check if it is boxed.
You can omit `set` functions if you assign result of box command to a normal variable and plan to use variable as normal type and provide read-only access to outside world. But if you assign the 
- Can we define box in tuple? yes. `type Customer := (name: string, age: box<int>)` So age is read/write.
- Maybe we should replace `box` with a notation like `&int`?
- Key in a map cannot be reference type.
- if we use `&` prefix how can we handle hash and sum?
- What if a function returns a reference? It does not make sense but banning this will add a new exception!
- There are two main applications for this change:
1. Provide mutable function argument
2. Simulate another type (simulate array of int with a customer list or a very big list with a disk-file)
```
var f = fopen("large_file")
;we want to simulate a string[] so the first element will point to the first line in the file
;but how is caching handled
```
- The prefix `&` notation is not good. 
- We can achieve the second item by embedding another type (like an array) and implementing appropriate methods (if "everything is a type" is implemented, so `x[0]` will call `get(x,0)`).
- So the only concern is the `ref` keyword and it being inconsistent and not orth with `auto/implicit`?
Seems there is not an easy way to remove this. So lets keep both of them.

Y - Define array and hash as normal built-in types and provide syntax sugar for them.
This makes things more unified.
And with introduction of box, we need unification.
`type Array<T> := native`
`type Map<K,V> := native`
`T[]` => `Array<T>`
`K=>V` => `Map<K,V>`
`y[x]` => `get(y, x)`/`set(y,x)`
array and hash literals will be converted to corresponding calls to get/set

N - Can we extend notation to support dependent types to make type system more expressive?
```
type DepValue<T> := (value:T)
func magic<T>(that: DepValue<T>)->T that.value
var x = %DepValue<int>(1) ;x is int
var y = %DepValue<string>("a") ;y is string
var xx: int = magic(x)
var yy: string = magic(y)
```
```
type EApply<T> := func(T)
func applyEither<T, F, G>(t:T, f: F, g: G, implicit z: EApply<T>) -> 
```

N - Can we add Monads?
No. Mostly useful where we want to keep everything pure.
Maybe we add them at a later stage.

Y - Scala has Phantom types, so does Haskell. Also Rust and F#!
Can we add them?
For example, a Person type, and two methods: play and rest. We want to make sure there is no play calls after each-other. Person should rest before being able to play again.
Phantom are compile-time label/state attached to a type. You can use these labels to do some compile-time checks and validations.
For example we have a string which is result of md5 hash and another for sha-1. We should not be comparing these two although they are both strings. So how can we mark them?
```
type HashType := MD5 | SHA1
 ;when generic type is not used on the right side, it will be only for compile time check
type HashStr<T> := string     
type Md5Hash := HashStr<MD5> 
;Md5Hash type can be easily cast to string, but if in the code a string
;is expected to be of type Sha1Hash you cannot pass Md5Hash
type Sha1Hash := HashStr<SHA1>
func md5(s: string)->Md5Hash {
    var result: string = "ddsadsadsad"
    return %Md5Hash(result)  ;create a new string of type md5-hash
}
func sha1(s: string)->Sha1Hash
var t: Md5Hash  = sha1("A")  ;will give compiler error because output of sha1 is Sha1Hash
func testMd5(s: string, t: Md5Hash) -> md5(s) == t

;if there is only one case, you can simply use named type
type SafeString := string
func processString(s: string)->SafeString
func work(s: SafeString)

;another example: expressions
type ExpType := INT | STR
type Expression<T> := (token: string)
func readIntExpression(...) -> Expression<INT>
func plus(left: Expression<INT>, right: Expression<INT>)...
func concat(left: Expression<STR>, right: Expression<STR>)...
```

Y - When a function expects a named type (`type SafeInt := int`), you have to pass a named type.
But when a function expects an unnamed type, you can either pass named or unnamed type.

Y - As a step toward more modularity, prevent importing multiple modules.

N - Module name of form `core.st.general` is better?
rust: `std::core::aaa`
go: `core/pkg/std`
scala: `p.x.a`

Y - Can we remove rules regarding ref and auto?
1. `ref` cannot be combined with `auto`.
2. You cannot have `func(x, auto y)` and `func(x)`
3. auto arguments must be at the end of function arguments.
solution:
1. remove `ref` and replace it with something which does not make sense to be combined with auto.
2. for example: `_` for auto, `&` for ref.
`func sort<T>(x:T[], &t : int, _z : Ord<T>)`
with this notation, item 1 is solved. but what happens in the call site?
`sort(a,&b)`
if we have:
`func sort<T>(x:T[], &t : int, _z : Ord<T>)`
and
`func sort<T>(x:T[], &t : int)`
what should happen? Maybe we should re-introduce the notation of optional arguments.
Java and go and Rust don't have it. 
Let's have these two rules.

Y - change `&` used for continue expression.
You can just use `{}`. It will evaluate to the last expression. This makes sense and is used in scala
But then we will have problem with exception handling.
Each block evaluates to it's last statement of exception. If the last statement does not have a type, it can be nothing. But you don't need to worry about the type:
```
var g = {
  func1()
  func2()
  func3()
}
return 100 if ( g :: exception)
```

Y - underscore for implicit is not good. What if a struct field name has underscore?
It is confusing.
`func sort<T>(x:T[], &t : int, !z : Ord<T>)`

N - Find most popular libraries for Go and Java and Scala and C.
- Spring
- Grails
- JSF

Y - Can we simplify?
- remove `uint` and `float`?
- Everything is a type? No. unification has an extreme.

N - Now that notations are types, can we use `[int]` for list/sequence?
- Set at specific index. This is not possible, because this is a linked-list.
- Clone: explode
- Clear: t=[]
- Add to beginning: `t = [1, @t]`
- Add to end: `t = [@t, 10]`
- ToArray -> clone to array: `var y: int[] = [@t]`
- From array: `var t: [int] = [@arr]`
- Other using functions: sort, search, delete, insert at index, length
- No slice, because this is a linked-list.
Almost everything needs to be done via functions. So why use a special notation?

N - In ocaml, a function which takes two ints and returns a float is actually a function which takes an int and returns a function which takes an int and returns a float.
Haskell: `add                     :: Integer -> Integer -> Integer`
`add x y                 =  x + y`
Ocaml: `let average a b`
F#: `let add x y = x + y`
calling `add(1)` will return a functin which takes a single input and returns an integer.
`func add(x:int->y:int->int)`
`func add(x:int,y:int,{r:int}) := x+y` lets not use `:=` and keep it only for types
Advantage of `->` notation is that we can simply write an expression instead of result type
`func add(x:int, y:int) -> 3`
`func process(int, int, int, int, int)->float`
`var g = ^process($_, 5, $_, $_, $_)`

N - Is it possible that any variable can hold an exception? How can this be explained with this type system?
We can say that exception type is subtype of all other types. 
`type exception := (anything, code: int)`
No. we dont need to have this subtyping. Output of every function of type T is T|exception.
so `var g:int = process()` this will just miss the exception.

Y - When doing cast to a generic type, you can ignore type if it can be deduced.
`var t = %ref(t)`

Y - Still `!` notation does not seem beautiful.
`+`?
Another solution: Do not use any notation. When calling the function, pass either a valid data or a notation there.
This will solve the 2 remaining exceptions: They must be at the end, and no `func(x,auto y)` and `func(x)`
```
func isInArray<T>(x:T, y:T[], &tt: int, +z: Eq<T>, +g: Writer) -> bool {
    if ( z.equals(x, y[0])...
}
...
isInArray(10, intArray, &tt, !, !)
isInArray(10, intArray, &tt, \, \)
isInArray(10, intArray, &tt, /, /)
isInArray(10, intArray, &tt, $_, $_)
isInArray(10, intArray, &tt, _, _)
```

Y - can we get rid of ref?
We need pass-by-ref but maybe we can implement it using existing tools.
```
func process(&x:int) { x++ }
...
var t = 12
process(&t)
```
Maybe by using a special container with generics: `ref`
`type mut<T> := (value: T)`
`mut<T>` behaves exacty like T but you can change it.
you cannot pass a variable of type `ref<T>` when `T` is needed (you should use `.value`).
```
func process(x:mut<int>) { x.value++ }
...
var t = 12
process(%mut(t))   ;you can omit <int>
```
Problem: can I now use `ref` inside function body?
You should be able to do that.
`var g: mut<int> = %ref(x) ;g++ will increase value of x`
`var t: int = g.value`
Maybe compiler can help with literal and ref: `var f: mut<int> = 12` will create a temp variable and ref to it.
`var t:int = refVar.value`
q: What happens if a tuple embeds a ref?
`type Point := (mut<int>, x: int)`
Point has a `.value` of type int which is a reference to an integer.
q: What does `mut<mut<t>>` mean? it is same as `ref<t>`
`mut<int>[]` vs `mut<int[]>`
`mut<int=>string>` vs `int=>mut<string>`

N - can we implement `loop` using functions?
`loop(10)` -> special case of second item
`loop(2..20)` -> `loop(2..20, lambda)`
`loop(x>5)` -> `loop`
loop over array or hash -> map
problem 1: implementing break and continue
problem 2: loop with condition
problem 3: if it is closure, it cannot modify local variables

Y - `ref<t>` is a big exception. maybe we should get rid of it.
it is not a big deal for primitives and tuple. 
The only important use case is for array and hash.
I believe it is possible to implement efficient immutable hash.
only array which we can accept.

Y - allow usage of `!` in every case that compiler can deduce value.
e.g. `func process(x: int, create: func()->int)...`
`process(12, !)`

Y - what if a tuple embeds anything or nothing?
for anything its ok. its implied anyway.
for nothing, it should not be allowed.
why do we need nothing? to support functions that dont return anything.
and blocks. 

Y - if a tuple has a function pointer, we can write: `point.process(...)`.
This will be ambiguous with chaining!
And by adding function pointers inside tuple, we can have minified oop.
maybe we should set composition operator to `<<` and `>>`
or `\` `/`. 

Y - embedding a dummy array does not seem intuitive. Here, the goal is to simulate an array. we can define a normal tuple, write appropriate functions, then in the target function declare `x:T, arrayAccessor: Arr<T>` meaning that we expect T to be treated like an array. As a result, tuples can only embed other tuples. no primitive or hash or array or sum-type.

Y - Maybe we should disallow embedding primitives (char, int, float) because suppose there is a function which accepts char.
Then, what should I pop from stack? 1 byte (normal char) or 4 bytes (a pointer to a tuple which embeds char)?

N - places of special behavior: array and map. anything and nothing.
where we have exceptions.

Y - now that `x[y]` calls `opIndex` let's remove another exception:
`x=[7, 8, 9]` will make 3 calls: `opIndex(x,0, 7)` to `opIndex(x, 2, 9)`
`x=[1=>'a', 2=>'b', 3=>'c']` calls: `opIndex(x, 1, 'a')`, ...

Y - When generating redirection functions, how can we write it?
`func process(Circle, SolidColor) -> process(Shape, Color)`
`func process(Circle, SolidColor) -> %func(Shape,color)(^process)(x, y)`
This is like: `getAdder(1,2)("Hello")(4)` because if output of an expression if a fp you can call it directly.
The only thing that we can do to make this more readable is to change casting syntax.

Y - The syntax for casting can become messy. Can we enhance it?
`%func(Shape,color)(^process)`
`%Point({x:10, y:20})`
We do not allow the developer to write custom casting functions. So the syntax should not be similar to function call.
`%Point{x:10, y:20}`
`%func(Shape,color){^process}`
`%Storage<int, Circle>{x,y,z}`

Y - Nothing is a sum type with only one value: `nothing`.

Y - remove `anything`.
With having templates, we don't need `anything`. It just makes method dispatch more complicated.
What about matching?
Rust -> `_`
Haskell -> `_`
F# -> `_` same as Scala

Y - a function pointer should not participate in dynamic method resolution. We should use it's type to find the exact methods we need to call.

N - Maybe we can change casting syntax:
`var x = int{{y}}`
`var x = Point{{x:10, y:20}}`
`var x = func(Shape,color){{^process}}`
But I think, `%` notation is more readable.

N - research: unified type system
- how to interact with DB
- How to itneract with C/C++ other libraries
- `+-*/` operations

Y - data types: int8, int16, int32, int64.
int16 can be removed. 
unsigned can be removed.
int8 -> byte
int32 -> int
int64 -> long
what about everything long and providing core functions to support multi-byte tuple to process images or do bitwise operations?
Also when dealing with externals, we can provide function to convert int to 8-16-32 bit integers using a byte array.
Also based on https://stackoverflow.com/questions/4584637/double-or-float-which-is-faster and https://stackoverflow.com/questions/417568/float-vs-double-performance it seems that at least on x86, they are the same. So let's just keep float which has a more meaningful name and remove double.
Maybe we don't need to deal with arb precision int. a 64 bit integer is sufficient for 99% of cases. The other 1% can use builtin bigInt types.

Y - Can a function pointer be assigned to functions with different parameter names?
If so, can we call the function pointer with named arguments?
Do we need to specify parameter name in function pointer?
`type adder := (x: int, y:int) -> int`
`func process(a: int, b:int) ...`
`var t: adder = ^process;`
`t(x: 10, y:12)`

Y - We should state that argument name is not part of a type. So any function with same types can be assigned to a lambda variable.

Y - remove call by Named arguments: 
Cons: it can result in style war, there should be preferraly only one way to do it.
But with this, a function with 3 inputs will have 3! ways to be called.
- One way to do it: Other than 3! we also can use a tuple with named fields.
Alternative: Using an unnamed tuple.
Disadvantage: If we use unnamed tuple, method dispatch won't work. Because it cannot go inside a tuple fields.
`func process(x: {s: Shape})`
what if we call process like: `process({s: myCircle})`?
Also there is a confusion regarding lambdas. If the type indicates argument names, can we use/change it in the literal?
`type adder := (x: int, y:int) -> int`
`var rr: adder = (a:int, b:int) -> { a + b }`
`var rr: adder = func { x + y }`
Problem is we use multiple dispatch but Go does not have this. Forcing developer to use unnamed tuples, will stop him from using multiple dispatch.
if we have multiple functions with same name and arg count but different arg name, can named arguments force runtime to pick a specific function?
`func process(s: Shape, c:Color)`
`func process(cr: Circle, sc: SolidColor)`
`process(c: ..., s:...)`
what if I want to use named arg and keep method dispatch?
There is a chance that I miss method dispatch by providing argument names. 
So:
cons: may loose method dispatch, useless style war, many ways to do something, encourages functions with large number of arguments, it won't help readability if its not mandatory. 

N - What syntax sugar do we have? Is it worth the cost (notation, learning curve, complexity)? What is the alternative?
- Array and Map
- string and concat
- `$_`
- `_`

Y - How do we solve the problem of string concatenation?
Will it remain an exception? 
we dont need operator for regex.
So maybe we can make use of `~` operator. 
Just like `x[y]` which calls opIndex, we can define `~` to call `opConcat`.
And user can define this method for any other type he wants.
So `+` doesn't need to be changed.

Y - Provide a simple comparison with C, Go, Scala.

N - we might be able to eliminate heap fragmentation by double referencing.
So a heap pointer does not point to actual memory address but points to an index inside an array whose values are memory pointers. 
double and int are both 8 bytes. Byte is 1 byte. string is a byte array. 
Because variety of storage size is not much (1 and 8 bytes) it will help reduce heap fragmentation.
Also we should do stack allocation as much as we can.

N - implementation: Instead of confusing things like perl's INC or Go's GOPATH, we should do like Go's new `vendor` dir or node system, to store dependencies.

Y - The duality of function call and function pointer call may make things confusing.
We should have same rules.
For example:
`&process(myCircle,$_,$_)(10, 20)` ~ `process(myCircle, 10, 20)`
Which `process` function will this code call?
What is type of this expression: `process(myCircle, $_, $_)`?
Suppose that we have:
`func pocess(Circle, int, int)`
`func process(Circle float, float)`
`var x = &process(myCircle, $_, $_)`
`var x = func(x: int, y:int) -> { process(myCircle, x, y) }`
We shall remove `$_` but what about `&`?
If we have to follow `var t = &func` it does not make much sense to have `&`.

Y - So: How can we write fwd function:
`func process(c: Circle) -> process(?)`
we want to redirect to process which accepts a Shape.
If we want to change the type, we can easily cast. But sometimes this is not what we want.
`var x = func(x: int, y:int) -> { process(myCircle, x, y) }`
`var x: func(s:Shape) = process` this will match with exact type name.
`func process(c: Circle) -> %func(s:Shape){process}(c)`
Maybe we should ban this!
What if inside a function working with Circle, I want to call the same function for Shape?
Go: `process(c.Shape)`. This will loose the dynamic type.
But if we want to call a function based on parent, maybe we should really change the type of the argument.
`func process(c: Circle) -> process(c.Shape)`
Maybe we should just avoid all virtual functions. Children types can hide functions of the parent with their own implementation. But if a function on the parent calls another function, it will be called for parent.
But having virtual functions will give us better runtime polymorphism. And if `area` for Circle is re-defined, anywhere we call `area` for a Circle, it should call the re-defined function.
Similarly, we may want to call a method for parent's parent. 
`process(myCircle)` - when we call a function normally, a full method dispatch process takes place.
If we ignore that, we need to specify exactly what function to call.
Option 1: Have normal method call or static-typed method call -> what if that method for static type does not exist?
Option 2: Change type of the argument.
Option 3: Invoke method dispatch to fetch result as a function pointer. Then call it.
We can define casting on a function name, as a result of method dispatch. 
Because a function pointer, cannot be used in method dispatch.
`func process(c: Circle) -> %func(s:Shape){process}(c)`

? - In method dispatch, there should be an exception for methods with one arg. if we have `f(Shape)` and we call `f(myCircle)` although there is no func that covers at least one argument's dynamic type, but we should call f-Shape because it makes sense. but what if we have another argument which is int?
`func process(s: Shape, len: int)`
`process(myCircle, 10)`?

? - for method dispatch, we can consider any type like a range.
anything will be -inf,+inf
shape will be: -100,100
circle will be: -20,+20
each type will be inside it's parent.
each argument's dynamic type is like a ball which is being dropped on the surface. The first level which it hit by the ball is the actual type used for function call.
So for any non-tuple field, we use explicit match to remove excess candidates. Remaining candidates will be tested with their tuple parameters, against tuple arguments. So we can think of the problem as matching a set of tuple arguments with tuple parameters belonging to candidates.
if we auto-generate all required forwarding functions, then we will have:
`f(Circle)-->f(Shape)`
or
`f(Shape)-->f(anything)`
`-->` is a notation to show auto-generated forwarding methods.
on the right side is a method call for an already defined function by developer.
on the left is a new function definition for a subtype and that function is not defined by the developer.
suppose we have a type hierarchy and compiler wants to compile.
`anything -> Shape -> Circle -> Oval`
`anything -> Color -> SolidColor`
`anything -> Person -> Employee -> Manager`
then compiler will generate a set of forwarding functions as below:
- for each function like f which has at least one argument of a non-anything type:
`func f(Color, Shape, Person)` ?
Let's do this: For functions with single tuple argument, generate forwarding functions.
For any other function (with 2+ tuple inputs), there must be either a function with exact match or exact match with static type or a function with anything input.
full dynamic match -> full static match -> anything
what about named types? named type is a completely new type. it has no relation to the unnamed type except for underlying storage.
what about functions with some anything and some other tuple types?
do we need anything type? no.
`process(Shape, Color)`
`process(Shape, SolidColor)`
we define Circle and SolidColor.
`process(Circle, Color) -> process(Shape, Color)`
`process(Circle, SolidColor)` -> process(Shape, SolidColor)`
Suppose that we have:
`Shape -> Circle -> Oval`
`Color -> SolidColor`
`Person -> Employee -> Manager`
Then, for each function (f) which has a tuple type (T) in it's inputs:
For each Ci in the list of T's children:
`f(...Ci...) --> f(...T...)` unless function already exists
if two same functions are created as a result of above forwardings, it will give a compiler error.
If we have:
`process(Circle, Color)`
`process(Shape, SolidColor)`
Then:
`process(Circle, SolidColor) --> process(Circle, Color)`
this will be generated for both function declarations -> compiler error.
if we have:
`Obj -> Shape -> Circle`
`Paint -> Color -> SolidColor`
f(Obj, Paint)
f(Circle, SolidColor)
then compiler will generate:
f(Shape, Paint)->f(Obj, Paint)
f(Circle, Paint)->f(Obj, Paint)
f(Circle, 
If we consider a 2-D matrix where rows are hierarchy levels and columns are function input types, this can be created for each function name/input count. Each function will represent a set of marked cells, each of them in a separate column.
This can be seen as a curve. If no two curves, intersect with each other, it is fine and compiler can generate fwd functions.
But if they collid, there will be ambiguities. This algorithm also covers the case with a single tuple input.
When generating these fwd functions, compiler can save space and time by using sum types:
`func process(x: Triangle|Circle,...)->process(Shape,...)`
But this is not a matrix, this will be a set of trees. But I think the same rule can be applied using child/parent relation.
So by using sum types, number of fwd functions will be equal or less than number of developer defined functions.
Equivalently, we can say for each definition like `type Circle := { Shape...` we need to scan functions that have Shape input. and write fwd from Circle to Shape with same name and other inputs.
for each `func process(T,U,V)` add another function:
`func process(x:T1|T2|..., y:U1|U2|U3..., z:V1|V2|V3...) -> %func(T,U,V){process}(x, y, z)`
where Ti, Ui, Vi are child types for T, U and V.

? - Do we really need redirection in code? 
If we do, it does not make sense to redirect to a specific function. 
Normally we should want to redirect/call the next function in method dispatch order.
Lisp and Dylan have: call-next-method and next-method.
Another solution: Give functions unique nicknames.
Or add a separate notation: `var t = &(c,h,y,u)` where `&` means the next function. You dont need to specify function name because it will be current function's name.
`func process(x: Circle) -> &(x)`
or maybe we should add a keyword for this. Note that we should be able to call it in the middle of a normal function.is
this will look like a function. I think we should use a symbol instead of keyword. Because when reading the code, it will be perceived that it is a normal function call.
What problems will this solve:
1. We no longer need the nasty notation of casting a function name to `func(Circle)`
2. fwd methods
3. in a method for Circle, you can call same method for Shape which will receive an instance of Circle.
How fwd methods can use this notation? If developer is using them it would be fine.
But what about runtime?
Maybe we should force developer to write fwd methods so at runtime we just need to check dynamic type.
But, when we see this `func process(T, U,V)->&(x,y,z)` which function should we call?
This does not solve the dispatch algorithm.

? - I think we should not stop at static type when resolving a method call.
`Obj -> Shape -> Circle`
`var c: Shape = createCircle()`
`process(c)`
If we don't have process defined for Circle and Shape, then the one for Obj should be called.
So when we have `f(x,y,z)` we have 3 dynamic types and go up in the hierarchy.
first we go up for the `x` type. When reacing first function `g` we do the same for rest arguments.
If as a result, we come to the same function for x,y and z then it should be called.
C++ also does not stop at static type and goes up in hierarchy to find appropriate function to cal. 
This fwd can be done by compiler (not by the developer). and hence it can detect ambiguity at compile time.
if we have a function for types A,B,C which are parents of T,U,V compiler will generate:
`func f(x: T,y: U,z: V) -> f/A/B/C(x,y,z)`
`f/A/B/C` is a unique name for a function which does not need method resolution. This fwd can be extended with sum types to make number of function defined by compiler less.
This fwd functions are extremely lightweight (no pushall/popall, just another assembly call instruction).


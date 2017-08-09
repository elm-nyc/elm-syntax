# A quick intro to elm syntax and some "functional" concepts
## Basic Data
### Numbers
Numbers can be an Int or a Float, and standard math operations are available in the core library [basics](http://package.elm-lang.org/packages/elm-lang/core/5.1.1/Basics). Note that integer division and floating point division are defined separately (`//` vs `/`)

``` 
four = 
  2 + 2

five = 
  26 // 5

fivePointOh = 
  25.0 / 5.0
```

### Strings and characters
A string literal is defined with `"`, a character with `'`. Strings are not simply lists of characters. Combine strings with `++`.

```
aString =
  "Hello, World"

aCharacter =
  'h'
```

### List
An ordered sequence of values. List literals are written with `[ , , ]`. All values in a list must be of the *same type* (more on that later).
```
okList = [ 'b', 'f', 'g' ]
notOkList = [ 'b', 'f', "g" ]
```

**STYLE NOTE** style-wise, leading commas are preferred if your list is written on multiple lines. This might seem weird at first, but give it a shot and I bet you'll like it the next time you have to remove an item from the end of a List literal and don't have to deal with a trailing comma error

```
elmStyle = 
  [ "thingOne"
  , "thingTwo"
  , "thingThree"
  ]
```

**FUNCTIONAL CONCEPT: immutability and `=`**

in elm, `=` may be a bit different than in languages that you've worked with. While `=` commonly means "gets", as in, a variable "gets" a value, in elm it's closer to the way one might think of it in math. Therefore, when I write `four = 4` I'm saying that `four` is _defined to be_ `4`. I can not, elsewhere in the same scope, define it to be something else - `four` is "immutable". In this way, I can be confident that anywhere in my code where I see `4` I can replace it with `four`, and vice versa, without changing the meaning of my program. This may take some time to get used to, but it often has the effect of making programs easier to understand and reason about.

### Tuple
Pairs of values defined with `( , )`. Values in a tuple do not need to be of the same type.
```
coordinate = 
  ('x', 'y')

heterogeneous = 
  ("One", 2)
```

### Record
A record is a set of key-value pairs and have a lot of similarities to objects in JavaScript. Record literals are written as `{ key = "value" }` and values can be accessed using dot notation as in javascript.
```
person = 
 { name = "Kara"
 , favoriteLanguage = "elm"
 }

person.name
.name person

```
Updating values in records cannot, however, be achieved by mutating a value, as in javascript. ie `object = { key = "value" }; object.key = "otherValue"` will not fly here. Instead, there is a completely different syntax that takes a record and returns a *new* record with updated values. 
```
{ person | name = "Noah }
```
this will give us a new record `{ name = "Noah", favoriteLanguage = "elm" }`

### Booleans
Bools in elm are `True` and `False`, and have all the operations you're probably already familiar with - `not`, `&&`, `||`. `xor` or "exclusive or" is also available and evaluates to true if exactly one input is true (`True xor True` is `False`).

### Maybe
Well discuss Maybe a bit later when we talk about types, but for now you can think of a Maybe as a value that may or may not exist. When it exists it's a `Just`, from which you can extract a value, and when it doesn't, it's a `Nothing`. Elm doesn't use a null, nil, or undefined value in the same way that many other languages do, so Maybe lets us perform operations like, for example, pulling a value from a dict, when we have no guarantee that the value is actually there.

``` 
justFour = Just 4
nothing = Nothing
```

## Type annotation
When definining a value, it is also a good idea to specify its type. The elm compiler can infer types, but you can get better error messages and make your code more readable by explicitly declaring types. 

```
four : Int
four = 4
```

# Function Application
OK. Now we know a bunch of great ways to enter data into our program, but how do we work with it? Apply functions. Function application takes the form of the function name followed by each of its arguments without parenthesis or commas.
```
doSomething thing1 thing2
```
we can see use this style with some of the infix operators we've already seen - note that 
```
1 + 1
```
is really just "syntactic sugar" applied to 
```
(+) 1 1
```

**FUNCTIONAL CONCEPT: no side effects**
The only thing that a function does is calculate return values based on its arguments - it cannot mutate data elsewhere in the program. For example, in javascript we might have something like this:
```
var foo = 0;

function(arg) {
  foo += 1;
  return arg + foo;
}
```
where calling the function both updates the value of `foo` and returns a value that depends on both `foo` and the passed argument. The "side effect" here is the change in the value of `foo` and this sort of thing is not possible in elm.

**FUNCTIONAL CONCEPT: determinism**
Becase there is no mutation or side effects, any function, given an identical set of inputs, will always return the same thing. Thus, anywhere we see `1 + 1` in our code, we can replace it with `2`. This seems simple, but consider the javascript example above - the first time we called our function and passed it `1`, it would return `2`, but we cannot assume that it will always return 2 when passed 1, because the value of foo will change each time it is called.

## <| and |>
What if we want to apply multiple functions to some arguments? We can do so with parentheses 
```
(+) 1 ((-) 2 1)
```
is the same as 1 + (2 - 1). However, all those parens can get a bit confusing, so elm provides us backwards and forwards function application. From the [docs](http://package.elm-lang.org/packages/elm-lang/core/5.1.1/Basics#|>)

>**Backward function application** f <| x == f x. This function is useful for avoiding parentheses. Consider the following code to create a text element:
```
leftAligned (monospace (fromString "code"))
```
>This can also be written as:
```
leftAligned <| monospace <| fromString "code"
```

and

>**Forward function application** x |> f == f x. This function is useful for avoiding parentheses and writing code in a more natural way. Consider the following code to create a pentagon:
```
scale 2 (move (10,10) (filled blue (ngon 5 30)))
```
>This can also be written as:
```
ngon 5 30
  |> filled blue
  |> move (10,10)
  |> scale 2
```

# Function Definition
```
isItOne x = 
  if x == 1 then True else False

doMath x = 
  let
    twentyFour =
      3 * 8

    sixteen =
      4 ^ 2
  in
    twentyFour + sixteen + x
```

## Type Annotation
Functions also have types that can be inferred by the compiler, but it's good practice to write them out. Function types are the types of their arguments followed by the return type, joined with `->`

```
backwardsDifference : Float -> Float -> Float
backwardsDifference x y = y - x
```

# Types
Ok - now we can use a bunch of great data types that come with elm core and apply functions, it's time to define our own types...

## Alias
The simplest way to define a type is to give a more descriptive name to an existing type; this is called a 'type alias'. For example, cartesian coordinates are frequently modeled as a tuple of floats, so let's create a coordinate type.
```
type alias Coordinate = (Float, Float)
```
I can now define a function that takes a Coordinate, as opposed to just (Float, Float)
```
moveUpOne : Coordinate -> Coordinate
moveUpOne (x, y) = (x, (y + 1))
```

## Union
You can define a type as a set of other, existing types

# Control Flow
```
if key == 40 then
  n + 1
else if key == 38 then
  n - 1
else
  n
 ```

```
case maybe of 
  Just x -> x
  Nothing -> []
```

## Helpful links
[core library docs](http://package.elm-lang.org/packages/elm-lang/core/5.1.1/)

[syntax intro](http://elm-lang.org/docs/syntax)

[ellie online compiler](https://ellie-app.com/new)

[a nice intro to some functional concepts (it's haskell tho)](http://www.seas.upenn.edu/~cis194/spring13/lectures/01-intro.html)


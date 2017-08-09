###a quick intro to elm syntax and some "functional" concepts
##basic data
#Numbers
Numbers can be an Int or a Float, and standard math operations are available in the core library [basics](http://package.elm-lang.org/packages/elm-lang/core/5.1.1/Basics). Note that integer division and floating point division are defined separately (`//` vs `/`)

``` 
four = 
  2 + 2

five = 
  26 // 5

fivePointOh = 
  25.0 / 5.0
```

#Strings and characters
A string literal is defined with `"`, a character with `'`. Strings are not simply lists of characters. Combine strings with `++`.

```
aString =
  "Hello, World"

aCharacter =
  'h'
```

#List
An ordered sequence of values. List literals are written with `[ , , ]`. All values in a list must be of the *same type* (more on that later).
```
okList = [ 'b', 'f', 'g' ]
notOkList = [ 'b', 'f', "g" ]
```

*STYLE NOTE* style-wise, leading commas are preferred if your list is written on multiple lines. This might seem weird at first, but give it a shot and I bet you'll like it the next time you have to remove an item from the end of a List literal and don't have to deal with a trailing comma error

```
elmStyle = 
  [ "thingOne"
  , "thingTwo"
  , "thingThree"
  ]
```

#Tuple
Pairs of values defined with `( , )`. Values in a tuple do not need to be of the same type.
```
coordinate = 
  ('x', 'y')

heterogeneous = 
  ("One", 2)
```

Record

Dict

Maybe

*FUNCTIONAL CONCEPT: immutability and `=`*
in elm, `=` may be a bit different than in languages that you've worked with. While `=` commonly means "gets", as in, a variable "gets" a value, in elm it's closer to the way one might think of it in math. Therefore, when I write `four = 4` I'm saying that `four` is _defined to be_ `4`. I can not, elsewhere in the same scope, define it to be something else - `four` is "immutable". In this way, I can be confident that anywhere in my code where I see `4` I can replace it with `four`, and vice versa, without changing the meaning of my program. This may take some time to get used to, but it often has the effect of making programs easier to understand and reason about.


#function application and composition

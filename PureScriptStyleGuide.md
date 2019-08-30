# PureScript Style Guide

This is a short document that presents some code style guidelines for the PureScript language. Along with formatting options, we also discuss some better practices that looks good and also bring performance.

## 1. General formatting

### 1.1. Max line length should be 80 characters

The lines in your source code should never cross the max length of 80 characters.

This is because most of us use vertical splits (especially us UI devs use left split for controller and right for view) and exceeding 80 characters will need us scroll a lot.

URLs, however, must not be split over multiple lines.

### 1.2. Say no to tabs

> PureScript compiler from version 0.13 will strictly fail to compile if tabs are used anyways. This gives the reason, and it is best to keep the code compatible with future compiler versions too.

Tabs actually bring more harm than good, especially in languages like PureScript where indentation has a syntactic meaning. So to be on safe side, always use spaces to indent the code.

People might argue that tabs will allow the user to set the size of the indentation and everyone can be happy etc., so I'll give an example: (`>>` represents a tab, and `.` represents a space)

~~~purescript
f x = case x of
........Just y ->
>>      ..case y of
>>      ....Just z  -> z
>>      ....Nothing -> defaultValue
........Nothing -> defaultValue
~~~

The major issue comes when people align code. Aligning makes the code look good in their own environment, and they can use nothing but spaces to do that. Now coming back to the example, this is how it looks for the users who configured their tab to be 2-column wide.

~~~purescript
f x = case x of
........Just y ->
>>..case y of
>>....Just z  -> z
>>....Nothing -> defaultValue
........Nothing -> defaultValue
~~~

Now the program is the same, compiles fine, but looks like a syntactically invalid program. This is because PureScript treats a _tab_ character as indentation that aligns with the next column that is divisible by 8 spaces.

This gives a problem when copying code that is indented with spaces into code indented by tabs. Always ensuring that you use spaces will make the code look correct no-matter what is the indentation size of the environment.

Another problem that arises when using tabs is line length. Assume when someone who is having 2-column wide tabs used tabs to indent and wrote a line under 80 visual columns. For people using other widths for tabs, the lines are no longer under 80 columns, forcing them to scan horizontally and scroll.

### 1.3. Set indent size to 2 columns

Code blocks should be indented with 2 spaces, to account for readability.

In PureScript, we will be writing more functions that look like mathematical functions to account for purity. Because of this, 2 column indentation looks more better and keeps the function body close.

When having more columns for indentation, we get less space for the horizontal space, which redirects us back to rule 1.1.

Exceptions are present for `let` bindings, nested literals etc., and will be discussed in their own section.

### 1.4. No trailing spaces

Spaces at the end of the lines have no use in PureScript, and they only take up excess space on the disk.

However, the main issue is faced by other developers who are editing your code. Imagine them trying to go to next line at the end of the last word by pressing right-arrow and seeing the cursor going to right instead of next line.

## 2. Arrays, Records & Row-Lists

### 2.1. Commas should precede the items

PureScript doesn't allow trailing commas in any of it's structures, so preceding the items with commas will result in nice diffs and also make it easy to add and remove elements.

~~~purescript
foldl (<>) ""
  [ "Hello "
  , "world! "
  , "This is another "
  , "long array!!"
  ]
~~~

The same should be followed with the records, import lists and row-lists. For example, records should look like this.

~~~purescript
let post = Post
      { title: "Welcome to PureScript!"
      , url: "/blog/welcome-to-purescript/"
      }
~~~

### 2.2. Write items in separate lines if cannot fit in one line

For places where the items fit in a single line, it is good enough to keep them in a single line, especially if they do not change often.

~~~purescript
sumNatural5 :: Int
sumNatural5 = foldl (+) 0 [ 1, 2, 3, 4, 5 ]

eval :: Action -> State -> State
eval action state = case action of
  Increment -> { count: state.count + 1 }
  Decrement -> { count: state.count - 1 }
~~~

However, if the items are too long to fit in a single line, keep the items on separate lines. This achieves readability.

~~~purescript
blogPosts :: Array Post
blogPosts =
  [ { title: "Welcome to PureScript!"
    , url: "/blog/welcome-to-purescript/"
    }
  , { title: "Higher order functions in PureScript"
    , url: "/blog/higher-order-functions-in-purescript/"
    }
  ]
~~~

### 2.3. Prefer record update syntax

When you want to return a record, which is mostly the same as the input with only a few fields changed, instead of copying all the values, try using the record update syntax.

~~~purescript
initialState :: State
initialState =
  { buttonText: "Click me!"
  , clicksMade: 0
  }

eval :: Action -> State -> State
eval action state = case action of
  ButtonClick -> state { clicksMade = state.clicksMade + 1 }
~~~

This is better when you do not want to change the other fields of the record. The same old values will be copied over from previous record, ensuring no manual copy error. Also is a lot less code to read.

### 2.4. Doubly indent nested record or array literals

When nesting a record or array literals, the nested literal should be doubly indented (this is, indented 4 spaces).

~~~purescript
{ bounds:
    { x: 0
    , y: 0
    , width: 400
    , height: 300
    }
, color: Red
, children:
    [ component1
    , component2
    , component3
    ]
}
~~~

## 3. Naming Rules

### 3.1. Use camel case for function names

Functions in PureScript must begin with a lower-case letter syntactically. So use the widely established camel case for the function names.

  * `add`
  * `fromMaybe`
  * `isJust`

Apart from function names, names for constant value bindings will also follow this rule.

### 3.2. Use upper camel case for type names

Data types and constructors must be written in upper camel case. Some examples are:

  * `Maybe`
  * `Functor`
  * `CommutativeRing`

This is because syntactically all the data types and constructors must begin with a capital letter.

### 3.3. Use all capitals for effect names

The names of effects (if you are still using PureScript < 0.12) should all be written using all-caps.

  * `CONSOLE`
  * `CANVAS`
  * `DOM`
  * `RANDOM`

From PureScript 0.12 onwards, there is no need to name effects, as all the effects are now categorized with just a simple `Effect` type.

### 3.4. Acronyms shouldn't be all-caps

Acronyms in PureScript do not follow the all-caps, with the exception of two-letter and three-letter acronyms. For example:

  * `HtmlParser`
  * `Rgb` or `RGB`
  * `Hsl` or `HSL`
  * `rgbToHsl`
  * `ST`
  * `DOM`

However, the three-letter acronym exception will only apply to type names or data constructors and never to functions.

### 3.5. Use the singular for module names

If you observe the standard library, all the modules are named in singular.

  * `Data.String` instead of `Data.Strings`
  * `Data.Object` instead of `Data.Objects`
  * `Data.Newtype` instead of `Data.Newtypes`

You should also be following the same convention as the standard libraries.

## 4. Type Signatures

### 4.1. Give types to all top-level definitions

All top-level definitions in a module should be explicitly given a type. This helps in finding where the definition is, and enables using the _Go to Definition_ feature provided by the IDE server.

~~~purescript
eval :: Action -> State -> State
eval action state =
  ...

view :: forall i w. State -> (Action -> Props i) -> PrestoDOM i w
view state push =
  ...

overrides :: forall i. Override -> State -> Props i
overrides element state =
  ...
~~~

### 4.2. Split type signatures over multiple-lines if long

If a type signature is going too long, then break it up and write it over multiple-lines.

~~~purescript
view
  :: forall i w r a
   . Eq a
  => Props i
  -> (a -> Props i)
  -> (a -> { offer :: String
           , imageUrl :: String
           , actionButton :: ActionButton
           | r
           })
  -> (a -> Int)
  -> RadioSelected a
  -> a
  -> PrestoDOM i w
~~~

This style allows the user to easily identify the details of the function they are seeing the code of.

### 4.3. Use type-aliases to shorten the long types

Creating type-aliases will help the user easily remember what is what when the type is going to be too long. For example, we have the following aliases in a project using Presto and BackTrack libraries.

~~~purescript
type Flow a = Free FlowWrapper a
type FlowE e a = ExceptT e (Flow a)
type FlowBT e a = BackT (FlowE e a)
~~~

Now we will be saying `FlowBT ScreenFailure ApiResponse` for a function, which when expanded completely gives

~~~purescript
BackT (ExceptT ScreenFailure (Free FlowWrapper ApiResponse))
~~~

However, care has to be taken that you only shorten the long types where the shortened name makes more sense. Do this only when the type is too long to write and also you use it repeatedly.

## 5. Imports

### 5.1. Group the imports based on the origin

The first import should always be `Prelude`. Group the imports in the following order.

  * The Prelude
  * Any third-party libraries
  * Application modules

Make sure that you import `Prelude` as a whole without any qualification. This prevents you from accidentally naming a function which can collide with standard library.

### 5.2. Separate import groups with a blank line

Each import group should be separated with a blank line. This makes it clear for people to identify where a function is coming from.

### 5.3. Sort import lists in alphabetic order

The import lists should be imported in alphabetic order, and operator imports should precede functions.

~~~purescript
import Data.Array ((..), (!!), cons, init, snoc)
import Data.Maybe (Maybe(..), fromMaybe, isJust)
~~~

Among the lists, the following sub-rules should be followed:

  * Importing kinds should be the first in the list.
  * Importing classes follow the kinds.
  * Importing effects follow after kinds.
  * Types should be imported after kinds.
  * When importing data constructors in a type, import all the constructors.
  * Importing operator functions should be followed by any data constructors.
  * Functions being imported should follow at the end of the list.

### 5.4. Prefer to use Qualified imports if the list is large

If you are importing a large set of functions, prefer to import them using qualified import syntax.

~~~purescript
import PrestoDOM.Core as P
import PrestoDOM.Elements.Elements as P
import PrestoDOM.Events as P
import PrestoDOM.Properties as P
~~~

This way, the imports doesn't change too often and won't be creating pretty large diffs after every commit.

### 5.5. Prefer to import from modules declaring Type-Classes

Unless `Prelude` re-exports a module, prefer to import a function from a module containing type-class and not from modules implementing the type-class.

~~~purescript
-- Prefer this
import Data.Newtype (class Newtype, wrap, unwrap)
import MyLibrary (MyType(..))

-- Rather than this
import MyLibrary (MyType(..), wrapMyType, unwrapMyType)
~~~

By importing functions in this way, people can use the standard functions that they are used for, and makes it easy to understand what the instance does because we know the laws of that type-class.

## 6. Exports

### 6.1. Always have export lists for library modules

All the library modules should have explicit export lists, stating which functions, classes, modules etc., to export.

~~~purescript
module MyLibrary (MyType(..), (*>), myFunc) where
~~~

This ensures that the users who use your library will not be able to use internal functions, and they will only be using the public interface of your library module.

### 6.2. Format long export lists like arrays

If the export list is going too long, and your module is having too many functions, it is better to format your export list like an array.

~~~purescript
module Data.Array
  ( Array
  , empty
  , singleton
  , sort
  ) where
~~~

Make sure you have the `where` keyword on the same ending line of the list. The ordering rules within the export list follows the same ordering of the import list.

### 6.3. Re-export modules only when they are useful

In some cases, like a library which manages the entire application lifecycle, you can have a convenience module that re-exports all the other modules. This allows people to be able to use the library with a single import and not worrying about which function came from which module.

~~~purescript
module PrestoDOM
  ( module PrestoDOM.Core
  , module PrestoDOM.Elements.Elements
  , module PrestoDOM.Events
  , module PrestoDOM.Properties
  , module PrestoDOM.Types.Core
  , module PrestoDOM.Utils
  ) where
~~~

However, make sure you export only the public modules in this form. Otherwise, there is a chance that your user accidentally starts using internal functions.

## 7. Case statements

### 7.1. Indent all the matchers one step

All the matchers in a `case...of` statement should be indented a step further. This will make the case statement stand out in the code.

~~~purescript
case head myArray of
  Just elem -> doSomething elem
  _         -> doSomethingElse
~~~

Now we know just by looking at this piece of code, that this is a case statement because it clearly stands out.

### 7.2. Case-Of Arrow indentation

Indenting the arrows can be done if that truly gives better look and makes the bindings clearly legible. If they are too distracting, it is better to not indenting the arrows.

~~~purescript
case action of
  CloseButtonClick               -> exit BackToScreen
  Button.Action Button.Click     -> continue $ state { count = state.count + 1 }
  Button.Action Button.LongPress -> continue $ state { showPopup = true }
  RefreshAction                  -> continue $ makeApiCall req state >>= \res -> state { data = res }
  _                              -> continue state
~~~

In the above example, it really hurts to indent each and every matcher arrow. Only that if they give better readability. There are cases where indentation gives good eye-candy results too, such as this one.

~~~purescript
case action of
  IncrementButtonClick -> continue $ state { clicks = state.clicks + 1 }
  DecrementButtonClick -> continue $ state { clicks = state.clicks - 1 }
  _                    -> continue state
~~~

As a general rule, if there is any case that requires more than 10 whitespaces before the arrow, don't indent the arrows. Otherwise indent the arrows. The above case of bad example can be dealt with in two ways:

  1. Split the cases into blocks and keep in the `where` clause.
  2. Do the indentation for the short cases together and separate the long ones by an empty line.

For example, the above bad example can be made good by refactoring it such as:

~~~purescript
eval :: Action -> State -> Eval State Return
eval action state =
    case action of
      CloseButtonClick -> exit BackToScreen
      Button.Action b  -> buttonActions b
      RefreshAction    -> continue $ makeApiCall req state >>= \res -> state { data = res }
      _                -> continue state
  where
    buttonActions = case _ of
      Button.Click     -> continue $ state { count = state.count + 1 }
      Button.LongPress -> continue $ state { showPopup = true }
~~~

Prefer this style for increased readability. When it is not possible to use the rule 1, go with rule 2, by giving an empty line around the longer cases and indent the rest.

And do not indent the arrows for multi-line matcher bodies.

### 7.3. Keep case matchers simple

Try to keep the bodies of the matchers to a single line if at all possible, or go upto 2 lines. If the matcher body goes more than 3 lines, do the following:

  1. Prefer to separate that out into the `where` clause and give it an apt name.
  2. If that is not possible, separate that matcher from others by surrounding it with blank lines.

One should strive to keep the case-of matcher bodies really simple. Keep in mind that case-of is an expression and not a function in itself. Keeping them simple increases readability.

There is, however, an exception to this rule:

If all the matchers in the case-of statement has more than 2 lines, go with rule 2 instead and separate them with blank spaces. However, try to refactor so that you won't hit this exception as much as possible. Only use it for some inevitable places.
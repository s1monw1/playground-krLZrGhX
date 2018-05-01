## Sealed Classes in Kotlin 


_Disclaimer: My articles are published under 
<a href="https://creativecommons.org/licenses/by-nc-nd/4.0/legalcode" target="_blank">"Attribution-NonCommercial-NoDerivatives 4.0 International (CC BY-NC-ND 4.0)"</a>._

© Copyright: Simon Wirtz, 2017 <a href="https://kotlinexpertise.com/sealed-classes-kotlin-explanation/" target="_blank">https://kotlinexpertise.com/sealed-classes-kotlin-explanation/</a>

Feel free to share.

### Intro

'Sealed Classes' is a feature of the <b>Kotlin</b> programming language, which is also available in Scala for example. Although many people haven't heard of it yet, it's a quite simple feature worth knowing, which I'm going to explain in the following.

### Feature Explanation

A sealed class can be subclassed and may include abstract methods which means sealed classes are abstract implicitly, although the documentation doesn't clearly say so. To actually make a class "sealed" we have to put the sealed modifier before its name, as we can see here:

```kotlin
sealed class MyClass
```
#### Restriction
The important thing about sealed classes is that its subclasses must be declared in the same file as the sealed class itself.

### Benefit

The feature allows us to define class hierarchies that are restricted in its' types, i.e. subclasses. Since all subclasses need to be defined inside the file of the sealed class, there's no chance of unknown subclasses which the compiler doesn't know about.

_Wait... Isn't this what an enum actually is?_

Kotlin's sealed classes are some kind of "extension" of plain enums: As opposed to enums, subclasses of sealed classes can be _instantiated multiple types_ and can actually _contain state_.

### Use Case

The main advantage of sealed classes reveals itself if it's used in `when` expressions. Let's compare a normal class hierarchy to one of a sealed class handled in a when. First, we'll create a hierarchy of `Mammal`s and then put it into a method using a ``when`` statement:

```kotlin runnable
fun main(args: Array<String>) {

    greetMammal(Cat("Lucy")).toConsole()
    greetMammal(Human("Luke", "Plumper")).toConsole()
    greetMammal(object : Mammal("unknownMammal") {}).toConsole()

}

open class Mammal(val name: String)

class Cat(catName: String) : Mammal(catName)

class Human(humanName: String, val job: String) : Mammal(humanName)

fun greetMammal(mammal: Mammal): String = when (mammal) {
    is Human -> "Hello ${mammal.name}; You're working as a ${mammal.job}"
    is Cat -> "Hello ${mammal.name}"
    else -> "Hello unknown"

}

//For debugging only
fun String.toConsole() = println(this)
```

The `else` is mandatory, otherwise the compiler would complain with _"`when` expression must be exhaustive, add necessary `else` branch"_. This is because it cannot verify that all possible cases, i.e. subclasses, are covered here. It may be possible that a subclass `Dog` is available at any time that is still unknown at compile time.

### Sealed "to the rescue"

What if we knew there wouldn't be other ``Mammal``s in our application? We'd want to leave out that ``else` branch.

The problem of unknown subclasses can be avoided by `sealed` classes. Let's modify the base class `Mammal`, its' subclasses can remain the same.

```kotlin
sealed class Mammal(val name: String)
```

The code example from above now looks as follows:

```kotlin runnable
fun main(args: Array<String>) {

    greetMammal(Cat("Lucy")).toConsole()
    greetMammal(Human("Luke", "Plumper")).toConsole()

}

sealed class Mammal(val name: String)

class Cat(catName: String) : Mammal(catName)

class Human(humanName: String, val job: String) : Mammal(humanName)

fun greetMammal(mammal: Mammal): String {
    when (mammal) {
        is Human -> return "Hello ${mammal.name}; You're working as a ${mammal.job}"
        is Cat -> return "Hello ${mammal.name}"
        // `else` clause not required
    }
}

//For debugging only
fun String.toConsole() = println(this)
````

We can simply omit the `else` branch since the compiler can verify that all possible cases are covered. Only the subclasses defined _in the file_ of the `sealed` class exist, without exception. 

#### What if a branch for at least on subsclass is being omitted?

Let's see what happens if we don't add a `when` case for every subclass of our sealed class, e.g. do not have a `Cat` case: 

```kotlin runnable
sealed class Mammal(val name: String)
class Cat(catName: String) : Mammal(catName)
class Human(humanName: String, val job: String) : Mammal(humanName)

fun greetMammal(mammal: Mammal): String {
    when (mammal) {
        is Human -> return "Hello ${mammal.name}; You're working as a ${mammal.job}"
    }
    return "will not compile"
}

````

As one might expect, the compiler complains with a comprehensive error message: "Error: 'when' expression must be exhaustive, add necessary 'is Cat' branch or 'else' branch instead".

That's it. In conclusion really simple and handy, isn't it? Have fun trying it yourself!

Finally, if you want to read about sealed classes or any other feature in more detail I recommend the book <a target="_blank" href="https://www.amazon.de/gp/product/1617293296/ref=as_li_tl?ie=UTF8&camp=1638&creative=6742&creativeASIN=1617293296&linkCode=as2&tag=simonwirtzde-21&linkId=7a0cd03461f738a8c428fb9b7a3af77a">Kotlin in Action</a>.<img src="//ir-de.amazon-adsystem.com/e/ir?t=simonwirtzde-21&l=am2&o=3&a=1617293296" width="1" height="1" border="0" alt="" style="border:none !important; margin:0px !important;" />

Cheers, Simon


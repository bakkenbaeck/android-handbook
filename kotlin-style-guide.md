# Kotlin Style Guide

The intention of this guide is to provide a set of conventions that encourage good code that is specific to Kotlin. To avoid repetition this playbook is entirely Kotlin specific but there is a lot of the advice in the [Java Style Guide](java-style-guide.md) that can also be applied in Kotlin.

## In General

You should strive to make code that is **easy to read**, **easy to understand**, and **easy to maintain**. That is why it is important when reviewing code to ensure these guidelines are met no matter how small, or nit-picky a request for a change may seem.

## Formatting

#### Don't use semicolons

They are not required so only add noise.

#### When possible use data classes

You get a lot for very little effort. They are easy to understand. They encourage having immutable model classes.

#### Use String templates

    val i = 10
    val s = "abc"
    // Good
    val str = "Val is $i and length is ${s.length}"
    // Bad
    val str = "Val is " + i + " and length is " + s.length

#### Simplify functions

Drop the return type if it can be inferred. Drop the braces and return statement if the function is a one liner.

    // Good
    fun double(x: Int) = x * 2


#### Use lambdas wherever possible

They reduce boilerplate code and are easier to read and understand. They also allow for tighter coupling of code.

#### Drop the left part of the lambda expression when possible

If a lambda has zero parameters, unused parameters our only a single parameter the left part of the lambda can be removed.

    // Bad
    { view -> toast("Click")}
    // Good
    { toast("Click")}


#### Use `it` in lambda when possible

If a lambda only has a single argument that is referenced use `it` instead.

    // Bad
    { view -> view.doSomething()}
    // Good
    { it.doSomething}

#### Controversial: Keep lambda inside function parentheses

If a lambda is the last parameter it is possible to move it outside of the parameters. Do not do that.

    // Bad
    view.setSomething(123) { toast("Something") }
    // Good
    view.setSomething(123, { toast("Something") })

#### If lambda is only parameter remove parentheses

    // Bad
    view.setSomething({ toast("Something")})
    // Good
    view.setSomething { toast("Something")}

#### Use single underscore for unused lambda parameters

If a lambda has an unused parameter, replace it with a single underscore. This tells both developers and Kotlin that it is unused.

    // Bad
    { oldValue, newValue -> delete(oldValue)}
    // Good
    { oldValue, _ -> delete(oldValue)}

#### Keep related extension functions in the same file

Group together extension functions that make sense to group together in one file.
Keep extension functions that do not logically belong together in seperate files.

#### Use default values when it makes sense to do so

The fewer parameters a function has, the better. So If you can provide a sensible default value for a parameter - do so.

    fun toast(message: String, length: Int = Toast.LENGTH_SHORT) { ... }

#### Prefer naming function parameters if there is a chance of confusion

If there are two similar parameters that could cause a bug then consider naming arguments to avoid confusion. This is most likely indicative of a bigger issue that a refactor may fix (i.e. consider creating a containing data class)

    // Bad
    setMovement(0, 5, 4, 2)
    // Good
    setMovement(xPos = 0, yPos = 5, velocityX = 4, velocityY = 2)

## Logic

#### Always use val unless you can't

Immutability by default. Use `val` expect in the situations where you must use `var`.

#### Don't explicitly define object type unless you need to

Kotlin can define the type so you don't have to; unless you need the more generic type.

    // Bad
    val a: Int = 5
    // Good
    val a = 5
    val c: Context = activity // Get the more generic type

#### Don't use !!

This can lead to NullPointerExceptions. If you know that an object can not be null then reassess why it is nullable. Consider using `lateinit` or `lazy` to initialise objects that can't be nullable but require special initialisation. It may be the case you still need to use `!!` when dealing with the Android framework, at which point you are advised to use caution.

#### Use lazy initialisation to avoid having a null object

If you know that an object can not be `null` but it also can't be initialised at the same time the parent class is initialised (this can often happen in Android when waiting for `onCreate` or similar to be called); use `by lazy` to delay the initialisation. This allows for the object to be defined as non-nullable and immutable.

#### Use `LazyThreadSafetyMode.NONE` if you can

If you don't need thread safety then `LazyThreadSafetyMode.NONE` will give a performance boost for lazy properties.

#### Prefer `lazy` to `lateinit`

`lateinit` doesn't work on `val` and thus doesn't give immutable values. Where possible use `lazy` and in the situations where you need mutability use `lateinit`.

#### Use companion objects for static data

If you have data that can be shared between instances of the same class - use companion objects.

#### Avoid running logic in custom getters and setters

Code should not have unforeseen consequences. Getters and setters should not be doing magic things.
If you are certain that it makes sense to override logic in a certain situation do so with caution.

#### Avoid operator overriding unless you know what you are doing

Code should not have unforeseen consequences. Overriding operators is a great opportunity for causing lots of confusion. Avoid, unless you really know what you are doing.

#### Inline functions where it makes sense to do so

Higher-order functions have a performance cost. To avoid that cost you can `inline` the function -- which tells the compiler to replace all calls to that method with the actual code. This can improve performance at the cost of number of lines of code. There is no hard and fast rule on when to inline and when to not as it needs to be judged on a case by case basis. Generally if the code is small it should be ok to inline.

## Field, class, and method declarations

#### Accessibility modifiers

Restrict access as much as possible. Make things `private`, then `protected`, then `internal`, then default.
The default is public, you do not need to write `public`.

That said, if you have public `val`s (i.e. immutable) they can remain public even if they could have been `private` to reduce noise in the code.

#### Keep classes closed

Open add `open` or `abstact` to a class if it makes sense to do so.

## Best practices

#### Use Anko for view lookups

No need for databinding, butterknife, or `findViewById`. Kotlin with Anko can get the views for you.

Beware, if you are using Anko in a RecyclerView you will need to cache the views yourself as Anko won't cache them for you.

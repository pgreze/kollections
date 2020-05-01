# Kounter

Project hosting an easy-to-use Counter class
or everything in order to create your custom one.

## Counter / MutableCounter

A specialization of the Map class allowing to count.

Usage:

```kotlin
val lints = mutableCounterOf("warnings" to 20)

lints["warnings"] += 3
lints["errors"] += 1

println(lints) // {warnings=23, errors=1}
```

Helpers:

```kotlin
counterOf("aabbbcc")                    // {a=2, b=3, c=2}
counterOf(arrayOf('a', 'b', 'b', 'c'))  // {a=1, b=2, c=1}
counterOf(listOf('a', 'a', 'b', 'c'))   // {a=2, b=1, c=1}
counterOf(setOf('a', 'b', 'c'))         // {a=1, b=1, c=1}
```

To perform mathematical operations on counters, use following methods:

```kotlin
val c1 = counterOf("ab")
val c2 = counterOf("ccd")
c1.plusAll(c2) // {a=2, b=3, c=2}

val chars = mutableCounterOf("ab")
chars.addAll(counterOf("ccd")) // {a=2, b=3, c=2}
```

Port of [Python Counter](https://docs.python.org/3.8/library/collections.html#collections.Counter),
inspired by [older references](https://github.com/python/cpython/blob/ec007cb43faf5f33d06efbc28152c7fdcb2edb9c/Lib/collections/__init__.py#L516)
like [Smalltalk Bag class](http://www.gnu.org/software/smalltalk/manual-base/html_node/Bag.html).

*Known limitation:* Only support Int for now.

## MapWithDefault / MutableMapWithDefault

Alternative of [withDefault](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/with-default.html),
the memory efficient way to specify a default value for a Map.<br/>
Sadly, this change is not reflected to the type system,
forcing users to use an alternative [getValue](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/get-value.html) method,
which is not the [most intuitive way of using a Map](https://discuss.kotlinlang.org/t/map-withdefault-not-defaulting/7691).

Proposed alternative is slightly changing the original Map interface:

```kotlin
interface MapWithDefault<K, V> : Map<K, V> {
    override fun get(key: K): V
}
```

Allowing to unlock a better syntax:

```kotlin
val money = mutableMapOf("Alice" to 10)
    .setDefault { 0 }

money["Alice"] += 10
money["Bob"] += 10

println(money) // {Alice=20, Bob=10}
```

*Known limitation:* previous syntax is not working with Set/List/Map:

```kotlin
val sets = mutableMapOf<String, MutableSet<String>>()
    .setDefault { mutableSetOf() }

sets["A1"] += "R1" // Assignment operators ambiguity:
// public operator fun <T> Set<String>.plus(element: String): Set <String> defined In kotlin.collections
// public Inline operator fun <T> MutableCollection<in String>.plusAssign(element: String): Unit defined in kotlin.collections

// Solution:
sets["A1"].plusAssign("R1")
```

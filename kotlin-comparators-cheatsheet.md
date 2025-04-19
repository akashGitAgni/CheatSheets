# Kotlin Comparators Cheat Sheet

## Basic Comparison Operators

| Operator | Description | Example |
|----------|-------------|---------|
| `==` | Structural equality (calls `equals()`) | `if (a == b)` |
| `===` | Referential equality (same object) | `if (a === b)` |
| `!=` | Structural inequality | `if (a != b)` |
| `!==` | Referential inequality | `if (a !== b)` |

## Comparable Interface

```kotlin
// Implementing Comparable for natural ordering
class Person(val name: String, val age: Int) : Comparable<Person> {
    override fun compareTo(other: Person): Int {
        // Return negative if this < other, 0 if equal, positive if this > other
        return this.age - other.age
    }
}

// Using comparable objects
val people = listOf(Person("Alice", 30), Person("Bob", 25))
val sorted = people.sorted() // Uses compareTo
val reversed = people.sortedDescending()
```

## Comparator Functions

| Function | Description | Example |
|----------|-------------|---------|
| `compareBy()` | Creates comparator by selector function | `compareBy<Person> { it.age }` |
| `compareByDescending()` | Creates descending comparator | `compareByDescending<Person> { it.age }` |
| `thenBy()` | Adds secondary sort criteria | `compareBy<Person> { it.age }.thenBy { it.name }` |
| `thenByDescending()` | Adds secondary descending criteria | `compareBy<Person> { it.age }.thenByDescending { it.name }` |
| `reversed()` | Reverses a comparator's ordering | `comparator.reversed()` |
| `nullsFirst()` | Places nulls first in ordering | `compareBy<Person?> { it?.age }.nullsFirst()` |
| `nullsLast()` | Places nulls last in ordering | `compareBy<Person?> { it?.age }.nullsLast()` |

## Collection Sorting Methods

| Method | Description | Example |
|--------|-------------|---------|
| `sorted()` | Sorts by natural order (Comparable) | `list.sorted()` |
| `sortedDescending()` | Sorts by natural order in reverse | `list.sortedDescending()` |
| `sortedBy()` | Sorts by specified property | `list.sortedBy { it.age }` |
| `sortedByDescending()` | Sorts by property in reverse | `list.sortedByDescending { it.age }` |
| `sortedWith()` | Sorts with explicit comparator | `list.sortedWith(comparator)` |

## Creating Comparators

### Simple Comparators

```kotlin
// For simple property comparison
val ageComparator = compareBy<Person> { it.age }
val nameComparator = compareBy<Person> { it.name }

// For descending order
val ageDescending = compareByDescending<Person> { it.age }
```

### Composite Comparators

```kotlin
// Multi-property comparator (first by name, then by age)
val nameAgeComparator = compareBy<Person>({ it.name }, { it.age })

// Chain comparison criteria
val customComparator = compareBy<Person> { it.name }
    .thenBy { it.age }
    .thenByDescending { it.salary }
```

### Custom Implementation

```kotlin
val customComparator = Comparator<Person> { p1, p2 ->
    when {
        p1.name != p2.name -> p1.name.compareTo(p2.name)
        p1.age != p2.age -> p1.age - p2.age
        else -> 0
    }
}
```

## Common Use Cases

### Sorting Collections

```kotlin
// Sort mutable list in place
val mutableList = mutableListOf(person1, person2, person3)
mutableList.sortWith(ageComparator)

// Create new sorted list
val sortedList = listOf(person1, person2, person3).sortedWith(ageComparator)

// Sort by property
val sortedByAge = people.sortedBy { it.age }
val sortedByNameDesc = people.sortedByDescending { it.name }
```

### Using max/min functions

```kotlin
// Find maximum/minimum by comparator
val oldest = people.maxWithOrNull(ageComparator)
val youngest = people.minWithOrNull(ageComparator)

// Find maximum/minimum by property
val oldest = people.maxByOrNull { it.age }
val youngest = people.minByOrNull { it.age }
```

### Handling Null Values

```kotlin
// Create a comparator that handles nulls
val nullSafeComparator = compareBy<Person?> { it?.age }.nullsLast()

// Sort with nulls at the end
val withNulls = listOf(person1, null, person2)
val sorted = withNulls.sortedWith(nullSafeComparator)
```

### Working with Maps

```kotlin
// Sort map by keys
val sortedMap = map.toSortedMap(compareBy<String> { it })

// Sort map entries by values
val sortedByValue = map.entries.sortedWith(compareBy { it.value })
```

## Data Classes

```kotlin
// Data classes automatically implement equals(), hashCode(), and toString()
data class Product(val name: String, val price: Double)

// But they don't implement Comparable by default
// You can manually implement Comparable or use comparators
```

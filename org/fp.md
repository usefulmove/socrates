Functional programming directly addresses the fact that 75% of programming language conferences are focused on validation, and pure functional programming (coming from the category theory of mathematics) guarantees validation is possible.

# Common Functions

| Operation                 | Scala                                                | Python                                               | JavaScript            | RamdaJS                       | Rust                                                     |
|---------------------------|------------------------------------------------------|------------------------------------------------------|-----------------------|-------------------------------|----------------------------------------------------------|
| map                       | map                                                  | map ( f(a) for a in iter )                           | map                   | R.map                         | iter.map                                                 |
| filter                    | filter                                               | filter ( a for a in iter if predicate(a) )           | filter                | R.filter                      | iter.filter                                              |
| fold                      | foldLeft / reduce                                    | functools.reduce                                     | reduce                | R.reduce                      | iter.fold / iter.reduce                                  |
| flatmap                   | collect / flatMap                                    | -                                                    | flatMap               | R.chain                       | iter.flat<sub>map</sub>                                  |
| flatten                   | flatten                                              | ( a for subseq in seq for a in subseq )              | flat()                | R.flatten                     | iter.flatten                                             |
| curry                     |                                                      | toolz.curry                                          | -                                                     | R.curry                       |                                                          |
| scan                      | scanLeft                                             | itertools.accumulate                                 | -                                                     | R.scan                        | iter.scan                                                |
| sum                       | sum                                                  | sum                                                  | reduce                | R.sum                         | iter.sum                                                 |
| count                     | size/count                                           | len                                                  | length                | R.count                       | iter.count/len                                           |
| max                       | max                                                  | max                                                  | reduce                | R.max                         | iter.max                                                 |
| min                       | min                                                  | min                                                  | reduce                | R.min                         | iter.min                                                 |
| sort                      | sorted                                               | sorted                                               | sort((a,b) => a-b)* | R.sort                        | slice::sort*                                             |
| reverse                   | reverse                                              | ::-1 / reversed                                      | reverse*              | R.reverse                     | iter.rev                                                 |
| compose                   |                                                      | toolz.compose/toolz.compose<sub>left</sub> as pipe   | -                                                     | R.compose/R.pipe              |                                                          |
| drop                      | drop                                                 | ::-1                                                 | slice                 | R.drop                        | skip                                                     |
| take                      | take                                                 | ::1                                                  | slice                 | R.take                        | take                                                     |
| any                       | exists                                               | any                                                  | some()                | R.any                         | iter.any                                                 |
| all                       | forall                                               | all                                                  | every()               | R.all                         | iter.all                                                 |
| zip/inner product         | zip                                                  | zip                                                  |                       | R.zip                         | iter.zip                                                 |
| outer product (Cartesian) |                                                      | itertools.product ( (a, b) for a in as for b in bs ) |                       | -                             | itertools::iproduct                                      |
| chain                     | ++                                                   | itertools.chain                                      | concat                | R.concat                      | iter.chain                                               |
| rotate                    | (o takeRight 1) ::: (o dropRight 1)/o.tail :+ o.head | o[n:] + o[:n] / o[-n:] + o[:-n] / numpy.roll |                       | R.move(-1)(0) / R.move(0)(-1) | slice.rotate<sub>right</sub>/slice.rotate<sub>left</sub> |
| unique                    | distinct                                             | set(seq)                                             | -                                                     | R.uniq                        | dedup                                                    |

["Idiomatic Rust favors functional programming. It's a better fit for its ownership model."](https://kerkour.com/rust-functional-programming) - Sylvain Kerkour

# Concepts ([reference](https://www.baeldung.com/scala/functional-programming))

- Immutability
- Recursion ( Tail Recursion )
- Functions as first-class objects
- Pure Functions
- Function Composition ( Category Theory )
- Higher-Order Functions
- Anonymous Functions
- Closures
- Partial Functions
- Monads
- Currying

# Languages

## Rust

### Iterators in Rust

[Iterator in std::iter](https://doc.rust-lang.md/std/iter/trait.Iterator.html)

iter (by reference) into<sub>iter</sub> (owned)

`Iter.inspect` can be used to inspect values flowing through an iterator.

## Scala

### Tail Recursion

`import scala.annotation.tailrec`

`@tailrec` decorator before recursive function definition

## Python

### Caching

`from functools import cache`

`@cache` decorator before function definition
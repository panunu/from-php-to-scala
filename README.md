# From PHP to Scala

**For those who are not yet experienced with functional programming (or static typing or Scala)**

When wandering from a non-functional way of programming into the world of functional programming one has to admit that the first steps may seem a bit steep and hard to comprehend. Usually one might bump into mystics with theoretical examples which seem to scare people away. And I think the people who get scared would be the ones to benefit the most from the functional ideologies.

People have different backgrounds and points of view: we have programmers who call themselves “self-educated hacker rebels”, “disciplined software engineers”, “academic software scientists”, “code artisans” or “poets”. But still, regardless of your background, most of the code we write (at least what I write) aims to model our client’s business. Maybe we should not be trying to prove that our code is pure or mathematically valid (or showcasing as clever usage of internal memory pointers as possible). Instead we could try writing code which reflects the real world. So we are the examples on how to do that? (Of course some exist e.g. http://fsharpforfunandprofit.com has some great examples)

I am no expert in functional programming. But I shall try to introduce some simple examples where functional programming with Scala (and static typing) might give you an edge. And maybe later we explore the patterns behind the code (the scary monads and so on). So let’s carry on to the examples.

## Null checks

If commenting is the only mechanism to indicate possible null values you have an extra mental load to carry with you: remember to check for nulls. And docblocks tend to (accidentally) lie.

```php
/**
 * @return int|null
 */
function find() { … }

$result = find();
$wasFound = !!$result; // Bug alert! What if the $result === 0?
$wasFound = $res !== null; // Now we are safe.
```

``` scala
def find: Option[Int] = … // No need for a docblock.
val result = find()
val wasFound = result.isDefined // Clear distinction between a value and it’s existence.
```

And by the way, Option is a monad.

## Static types

In addition to having always up-to-date documentation for humans, the computer also understands what you are trying to do and notifies if you happen to make a mistake. Of course, we never do mistakes.

```php
/**
 * These might not be up-to-date.
 *
 * @param  string $id
 * @return int[]|null
 */
function getFromDatabase($id) { … }
```

```scala
def getFromDatabase(id: String): Option[List[Int]] = …
```

## Model your data

We can easily create a type-safe structure instead of arbitrary and undocumented arrays. One usually ends up with an array because writing a simple data-containing class is such a tedious task thanks to the verbosity.

```php
public class Entity {
    private $id;
    private $names = [];
    
    /**
     * @param int $id
     * @param string[] $names
     */
    public function __construct($id, array $names = []) {
        $this->id = $id;
        $this->names = $names;
    }

    /**
     * @return int
     */
    public function getId() {
        return $this->id;
    }

    /**
     * @return string[]
     */
    public function getNames() {
        return $this->names;
    }
}
```

```scala
case class Entity(val id: Int, val names: List[String])
```

TODO: Examples about type design (e.g. using union types)?

## Pattern matching

TODO

## Try-catch

```php
$thingies = [];

try {
    $thingies = getThingies();
} catch (\Exception $e) {}
```

```scala
val thingies = Try(getThingies()).getOrElse(List())
```

TODO: Another example.

## Replacing ifs and christmas trees

TODO: Better title and some nice intro here. Intention for this "chapter" is to explore different kind of mechanisms for logical flows.

```php
$result = null;

if (($a = $service->getFromDatabase()) !== null) {
    if (($b = $anotherService->getSomething($a)) !== null) {
        $result = $yetAnotherService->getSomethingElse($b);
    }
}
```

```scala
val result = for {
  a <- service.getFromDatabase() // These methods return e.g. Options
  b <- anotherService.getSomething(a)
  c <- yetAnotherService.getSomethingElse(b)
} yield c
```

```scala
val result = service.getFromDatabase()
  .flatMap(anotherService.getSomething)
  .flatMap(yetAnotherService.getSomethingElse)
```

FlatMap might seem a bit strange but for now you can imagine it as a unix pipe operation.

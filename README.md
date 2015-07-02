# From PHP to Scala

**For those who are not yet experienced with Scala, static typing or functional programming**

When wandering from a non-functional way of programming into the world of functional programming one has to admit that the first steps may seem a bit steep and hard to comprehend. Usually one might bump into mystics with theoretical examples which seem to scare people away. And I think the people who get scared would be the ones to benefit the most from the functional ideologies. With Scala one can start writing code "the same way it has always been written" but slowly start to map uncharted territories.

People have different backgrounds and points of view: we have programmers who call themselves “self-educated hacker rebels”, “disciplined software engineers”, “academic software scientists”, “code artisans” or “poets”. But still, regardless of your background, most of the code we write (at least which I write) aims to model our client’s business. Maybe we should not be trying to prove that our code is pure or mathematically valid (or showcasing as clever usage of pointers as possible). Instead we could try writing code which reflects the real world. So we are the examples on how to do that? (Of course some exist e.g. http://fsharpforfunandprofit.com has some great examples)

I shall try to introduce some simple and concrete real-life examples where Scala (and functional programming and static typing) might give you an edge. And maybe later we explore the patterns behind the code (the scary monads and so on). So let’s carry on to the examples.

## Immutability

Mutability is often a source of nasty bugs. Scala's mechanisms (e.g. immutable collections and `val`) makes it easier to follow the preference of immutability.

TODO: Some nasty example.

## Null checks

If commenting is the only mechanism to indicate possible null values you have an extra mental load to carry with you: remember to check for nulls. And docblocks tend to (accidentally) lie.

```php
/**
 * @return int|null
 */
function findPriceMultiplier() { … }

$multiplier = findPriceMultiplier();
$wasFound = !!$multiplier; // Bug alert! What if the $result === 0?
$wasFound = $multiplier !== null; // Now we are safe.
```

``` scala
def findPriceMultiplier: Option[Int] = … // No need for a docblock.
val multiplier = findPriceMultiplier
val wasFound = multiplier.isDefined // Clear distinction between a value and it’s existence.
```

And by the way, Option is a monad.

## Static types

Static typing is a bit like having an always up-to-date documentation for yourself and your colleagues and even for the poor fellow that will maintain your legacy some year from now. Additionally the computer understands what you are trying to do (on a limited level but with an amazing attention to detail). So the compiler will inform if one happens to e.g. pass the wrong type of a parameter to a method *before* the flawed application is deployed, trashing production. But of course, we never do mistakes so why bother :-)

Static typing seems to lessen the need for excessive testing<b>*</b> and answering the question "did I break something when refactoring". IDEs also tend to like static typing and therefore can help you a bit better.

<small><b>*</b> Based on my non-scientific hunch.<small>

```php
/**
 * These might not be up-to-date.
 *
 * @param string $id
 * @return int[]|null
 */
function getSomethingFromDatabase($id) { … }
```

```scala
def getSomethingFromDatabase(id: String): Option[List[Int]] = …
```

## Model your data

We can easily create a type-safe structure instead of arbitrary and undocumented arrays. One usually ends up with an array because writing a simple data-containing class is such a tedious task thanks to the verbosity.

```php
class Entity
{
    private $id;
    private $names = [];
    
    /**
     * @param int $id
     * @param string[] $names
     */
    public function __construct($id, array $names = [])
    {
        $this->id = $id;
        $this->names = $names;
    }

    /**
     * @return int
     */
    public function getId()
    {
        return $this->id;
    }

    /**
     * @return string[]
     */
    public function getNames()
    {
        return $this->names;
    }
}
```

```scala
case class Entity(id: Int, names: List[String])
```

TODO: Examples about type design (e.g. using union types)?

## Collections

I have not heard a lot of praise for PHP's collection manipulation functions like `array_map` or `array_filter` or `array_pop` which all work a bit differently<b>*</b>. Therefore Scala's collections (although complex below the surface) feel refreshing.

```php
$xs = array_map(function ($x) { return (int) $x; }, ['1', '2']);
$xs = array_filter($xs, function ($x) { return $x === 1; }); // Both non-mutating functions but with varying parameter order
$x = array_pop($xs); // Mutating, referencing
```

```scala
val x = List("1", "2").map(_.toInt).filter(_ == 1).head
```

<small><b>*</b>  Luckily at least the related language mechanisms have improved. And of course there are some collection-related userland libraries for PHP like https://github.com/xi-project/xi-collections or https://github.com/Anahkiasen/underscore-php</small>

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

## Logical flows: replacing ifs and christmas trees

TODO: Better title and some nice intro here. Intention for this "chapter" is to explore different kind of mechanisms for logical flows.

```php
$result = null;

if (($a = $service->getSomething()) !== null) {
    if (($b = $anotherService->getSomethingElse($a)) !== null) {
        if (($c = $yetAnotherService->getSomethingElse($b)) !== null) {
            $result = $service->getFinalResult($c);
        }
    }
}
```

```scala
val result = for {
  a <- service.getSomething() // These methods return e.g. Options
  b <- anotherService.getSomethingElse(a)
  c <- yetAnotherService.getSomethingElse(b)
} yield service.getFinalResult(c)

// An alternative implementation.
val result = service.getSomething()
  .flatMap(anotherService.getSomethingElse)
  .flatMap(yetAnotherService.getSomethingElse)
  .flatMap(service.getFinalResult)
```

FlatMap might seem a bit strange but for now you can imagine it as a unix pipe operation.

#### Another example: controller action which throws often

```php
if (!$auth->hasIdentity()) {
    throw new AccessDeniedHttpException();
}

$entry = $service->findObjectById($id);
if (!$entry) {
    throw new NotFoundHttpException();
}
```

```scala
if (!auth.hasIdentity) throw new AccessDeniedException
val entry = service.findObjectById(id).getOrElse { throw new NotFoundException }
```

## JSON

TODO: Example. Hard to beat PHP ;-)

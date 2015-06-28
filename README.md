# From PHP to Scala

**For those who are not yet experienced with functional programming**

When wandering from a non-functional way of programming into the world of functional programming one has to admit that the first steps may seem a bit steep and hard to comprehend. Usually one might bump into mystics with theoretical examples which seem to scare people away. And I think the people who get scared would be the ones to benefit the most from the functional mindset.

People have different backgrounds and points of view: we have programmers who call themselves “self-educated hacker rebels”, “disciplined software engineers”, “academic software scientists”, “code artisans” or “poets”. But still, nevertheless your background, most of the code we write (at least what I write) aims to model our client’s business. We should not be trying to prove that the code is mathematically valid (or showcasing as clever usage of internal memory pointers as possible). Instead we should be writing code which reflects the real world. So we are the examples on how to do that? (Of course there are some. E.g. http://fsharpforfunandprofit.com has some great examples)

I am no expert in functional programming. But I shall try to introduce some simple examples where functional programming with Scala (and static typing) can give you an edge. And maybe later we explore the patterns behind the code (the scary monads and so on). So let’s carry on to the examples.

## Null checks

If a comment is the only mechanism to indicate possible null values you have an extra mental load to carry with you: remember to check for nulls. And docblocks tend to (accidentally) lie.

/**
   * @return int|null
 */
function find() { … }

$result = find();
$wasFound = !!$result; // Bug alert! What if the $result === 0?
$wasFound = $res !== null; // Now we are safe.

def find: Option[Int] = … // No need for a docblock.
val result = find()
val wasFound = result.isDefined // Clear distinction between a value and it’s existence.

And by the way, Option is a monad.

## Static types

In addition to having always up-to-date documentation for humans, the computer also understands what you are trying to do and notifies if you happen to make a mistake. Of course, we never do mistakes.

/**
   * These might not be up-to-date.
 *
   * @param  string $id
   * @return int[]|null
 */
function getFromDatabase($id) { … }

def getFromDatabase(id: String): Option[List[Int]] = …

## Easily model your data

We can easily create a type-safe structure instead of arbitrary and undocumented arrays. One usually ends up with an array because writing a simple data-containing class is such a tedious task thanks to the verbosity.

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

case class Entity(val id: Int, val names: List[String])

TODO: Examples about type design (e.g. using union types)?

## Pattern matching

TODO

## Try-catch

$thingies = [];

try {
    $thingies = getThingies();
} catch (\Exception $e) {}

val thingies = Try(getThingies()).getOrElse(List())

TODO: Another example.

## Replacing ifs and christmas trees

TODO: Better title and some nice intro here.

$result = null;

if (($a = $service->getFromDatabase()) !== null) {
		if (($b = $anotherService->getSomething($a)) !== null) {
        $result = $yaService->getSomethingElse($b);
		}
}

val result = for {
  a <- service.getFromDatabase() // These methods return e.g. Options
  b <- anotherService.getSomething(a)
  c <- yetAnotherService.getSomethingElse(b)
} yield c

OR 

val result = service.getFromDatabase()
	.flatMap(anotherService.getSomething)
	.flatMap(yetAnotherService.getSomethingElse)

FlatMap might seem a bit strange but for now you can imagine it as a unix pipe operation.

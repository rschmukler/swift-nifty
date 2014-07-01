# swift-nifty

A collection of brief summaries of nifty functionalities in Apple's swift. This
repo can serve as a way to get familiar with some of swift's more unique
features.

# Table of Contents

- [Named Parameters](#named-arguments)
- [Custom Operators](#custom-operators)
- [Closures as Parameters](#closures-as-parameters)
- [Namespaced Classes](#namespaced-classes)
- [Inferred Enumerators](#inferred-enumerators)


## Named Parameters

In swift, by default, a function takes anonymous parameters:

```swift
func printTwoStrings(strA: String, strB: String, yay: String) {
  println("\(strA) \(strB)")
}

printTwoStrings("Hello", "World")
```

Sometimes, it can be more readable if you specify a named parameter. You can do this by specifying the parameter name before declaring the local variable name.

```swift
func sayHello(to name: String) {
  println("Hello, \(name)")
}

sayHello(to: "Bob")
```

If you want to expose an external name, with the same name as your local variable, you can do so by prefixing the variable with a `#`.

```swift
func sayHello(#to: String) {
  println("Hello, \(to)")
}
sayHello(to: "Bob")
```

#### Default values and Named Parameters

If you specify a default value, swift will add an external name to the parameter for you.

```swift
var counter = 0

func increment(by: Int = 1) {
  counter += by
}

increment()
println(counter) // prints 1
increment(by: 5)
println(counter) // prints 6
```

#### Methods and Named Parameters

To make swift look more "Objective-C like", Apple decided to have all but the first parameter be named by default on instance methods. For example:

```swift
class Person {
  var name: String
  
  init(name: String) {
    self.name = name
  }
  
  func say(message: String, to: String) {
    println("\(self.name) says: \(message) to \(to)")
  }
}

var bob = Person(name: "Bob")
bob.say("Hello", to: "Paul")
```

If you prefer to define a differently named local variable, you can do so by explicitly setting it:

```swift
class Person {
  var name: String
  
  init(name: String) {
    self.name = name
  }
  
  func say(message: String, to otherPerson: String) {
    // We now have named the variable otherPerson
    println("\(self.name) says: \(message) to \(otherPerson)")
  }
}

var bob = Person(name: "Bob")
bob.say("Hello", to: "Paul")
```

You can also specify that you want the parameter to be anonymous by using `_` for the external name.

```swift
class Person {
  var name: String
  
  init(name: String) {
    self.name = name
  }
  
  func say(message: String, _ otherPerson: String) {
    // We now have named the variable otherPerson
    println("\(self.name) says: \(message) to \(otherPerson)")
  }
}

var bob = Person(name: "Bob")
bob.say("Hello", "Paul")
```

Named parameters are an interesting addition that allow for something of a hybrid between Ruby's splat operator and more traditional anonymous parameters found in other lanagues like C, Javascript, etc. 

## Custom Operators

Swift allows you to define operators for any type. Since it is statically typed,
the compiler is able to use this information to determine when to apply which
operator. For example, you could add a comparison operator for `NSDate` with the
following:

```swift
@infix func > (left: NSDate, right: NSDate) -> Bool {
  return left.compare(right) == NSComparisonResult.OrderedDescending
}

@infix func >= (left: NSDate, right: NSDate) -> Bool {
  var comparisonResult = left.compare(right)
  return comparisonResult == NSComparisonResult.OrderedDescending ||
    comparisonResult == NSComparisonResult.OrderedSame
}

@infix func < (left: NSDate, right: NSDate) -> Bool {
  return left.compare(right) == NSComparisonResult.OrderedAscending
}

@infix func <= (left: NSDate, right: NSDate) -> Bool {
  var comparisonResult = left.compare(right)
  return comparisonResult == NSComparisonResult.OrderedAscending ||
    comparisonResult == NSComparisonResult.OrderedSame
}
```

`@infix` specifies that the function name goes **between** two things. Other possible
keywords are `@prefix` and `@postfix`.

Here is an example of a `@prefix` operator:

```swift
class Account {
  var balance = 0.0

  func init(bal: Float) {
    balance = bal
  }

}
@prefix func - (acc: Account) -> Account {
  return Account(bal: -acc.balance)
}

var acc = Account(bal: 3000)
println(-acc)
```

And a `@postfix` operator:

```swift
class Account {
  var balance = 0.0

  func init(bal: Float) {
    balance = bal
  }

}
@postifx func ++ (acc: Account) -> () {
  acc.balance++
}

var acc = Account(bal: 3000)
acc++
```

## Closures as Parameters

Let's say that we have a function that takes a function as a parameter. It might
look like this:

```swift
function myFunction(done: (String[], NSError?) -> ()) {
  done(["Hello"], nil)
}
```

Then, explicitly, calling the function might look like this:

```swift
myFunction((results: String[], err: NSError?) -> () {
  // do something with results
})
```

Swift, however, has a few nicer ways of passing anonymous closures...

You can shorten that big function to something smaller using the `in` keyword.

```swift
myFunction({ results: String[], err: NSError? in 
  // do something with results
})
```

Since swift knows the type of closure `myFunction` is expecting, you can
actually omit the types and let the compiler infer them:

```swift
myFunction({ results, err in
  // do something with results
})
```

Lastly, if the last parameter of a function is a closure, you can actually pass
it after the closing parenthesis, for even nicer readability:

```swift
myFunction() { results, err in
  // do something with results
}
```

## Namespaced Classes

In Swift you can create nested structs, enums, and classes inside of classes and
structs. This lets you create namespaces.

```swift
class Farm {
  var pen = Array<Animal>()
  
  init() {
  }
  
  func addAnimal(animal: Animal) {
    pen.append(animal)
  }
  
  class Animal {
    var name: String
    
    init(name: String) {
      self.name = name
    }
  }
  
  class Horse: Animal {}
  
  class Dog: Animal {
    var breed: String
    
    init(name: String, breed: String) {
      self.breed = breed
      super.init(name: name)
    }
  }
}

var farm = Farm()

farm.addAnimal(Farm.Horse(name: "Bill"))
```

Inside the top-level class, you can access subclasses without the namespace distinction...

```swift
class Farm {
  // ... same definition as above

  convenience init(horseNamed name: String) {
    self.init()
    self.addAnimal(Horse(name: name))
  }
}

var horseFarm = Farm(horseNamed: 'Bob')
```swift

## Inferred Enumerators

Swift provides enums with the following syntax:

```swift
enum Color {
  case Red, Orange, Yellow, Green, Blue, Purple
}
```

Suppose you had a Person class that had a favorite color...

```swift
class Person {
  var favoriteColor: Color

  init(favoriteColor: Color) {
    self.favoriteColor = favoriteColor
  }
}
```

You could then initialize a person in the following way:

```swift
var bob = Person(favoriteColor: Color.Red)
```

Since swift is a typed language, the compiler will actually allow you to omit the enum's name and just infer it from the type....

```swift
var larry = Person(favoriteColor: .Blue)
```

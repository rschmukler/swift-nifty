# swift-nifty

A collection of brief summaries of nifty functionalities in Apple's swift. This
repo can serve as a way to get familiar with some of swift's more unique
features.

# Table of Contents

- [Named Parameters](#named-arguments)

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

Named parameters are an interesting addition that allow for something of a hybrid between Ruby's splat operator and more traditional anonymous parameters found in other lanagues like C, Javascript, etc. This concludes my first post on Swift. Check back for more language features soon.

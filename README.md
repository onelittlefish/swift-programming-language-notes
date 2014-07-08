# Basics
- `let`
- `var`
- `let foo: Type`
- `println`/`print`, `"string \(interpolation)"`
- nested multiline comments
- use `Int` unless you really need a different type of integer
- swift defaults to `Double` (over `Float`) when inferring
- numeric literals can have underscores (for readability)
- integer/floating point type conversions must be explicit (e.g. `Double(three)`)
- literals don't need to be converted
- `typealias NewType = ExistingType`
- `Bool` (`true`, `false`)
- tuples
```Swift
let tuple = (foo, bar)
let (foo, bar) = tuple
let (foo, _) = tuple
let tuple = (foo: "Foo", bar: "Bar")
```
- optionals
```Swift
if optional {
	println("We have an \(optional!)")
}
```
- more about optionals
  - forced unwrapped: `optional!`
  - `if let actualNumber = possibleNumber.toInt() {}`
  - `nil` = no value, can't use nil with non-optionals
  - implicitly unwrapped optionals
    - `let assumedString: String! = "foo"`
    - for when an optional is confirmed to have a value and can be assumed to continue having one, accessing one when it's nil causes runtime error
- `assert(expression, "Optional string with no interpolation")`

# Operators
- assignment does not return a value (can't do `if foo = bar {}`)
- can use `%` with floating point numbers
- use `++i` unless you really need `i++` behavior
- compound assignment (e.g. `+=`) does not return a value
- `===` and `!==` test whether objects are the same instance
- range: `1...4` (includes 4), `0..count` (does not include count)

# Strings
- `""` and `String()` are both empty strings
- mutability based on `var` vs. `let`
- `isEmpty`
- value type (copied when passed)

# Collections

## Arrays
- have a type: `Type[]` (also `Array<Type>`)
- `count`/`isEmpty`
- `+=`
- for in with index: `for (index, value) in enumerate(shoppingList) {}`
- `var ints = Int[]()`
- `var doubles = Array(count: 3, repeatedValue: 1.5)`

## Dictionaries
- `Dictionary<KeyType, ValueType>()`
- can remove by doing `dict["key"] = nil`
- empty dictionary if type is known: `dict = [:]`

## Immutability
- immutable dictionaries can't change size or values
- immutable arrays can't change size but you can change the value for an existing index

# Control Flow

## Switch
- must be exhaustive
- cases do not fall through
- each case body must contain >= 1 executable statement (`break` counts)
- can match multiple values (separated with commas)
- can match ranges
- can match tuples
- `_` = don't care
- can bind matched value
```objc
switch point {
	case (let x, 0):
		println("x is \(x) on x-axis")
	case let (x, y):
		println("\(x), \(y)")
}
```
- `where`
```objc
switch point {
	case let (x, y) where x == y:
		println("x is \(x) on line x == y")
	case let (x, y):
		println("\(x), \(y)")
}
```
- `continue`, `break`
- `fallthrough` (does not check next case condition)

## Other
- can label statements and use label with `break`/`continue`: `label: while foo == bar {}` to make it clear in nested statements

# Functions
- `func functionName(localParamName: Type) {}`
- `func functionName(externalParamName localParamName: Type) -> ReturnType {}`
- can return multiple values: `func functionName() -> (foo: ReturnType, bar: ReturnType)`
- same external and local names: `#paramName: Type`
- default value: `externalName localName: Type = defaultValue`
- params are constants by default, use `var` to make variable copy
	- changes to var only persist with the function body
- `inout`: changes persist after function call, cannot have a default value
- function types: `() -> ()`, `(ParamType, ParamType) -> ReturnType`
- can be nested

# Closures
- `reversed = sort(array, { (s1: String, s2: String) -> BOOL in return s1 > s2 })`
- with type inference: `reversed = sort(array, { s1, s2 in return s1 > s2 })`
- single-expression closures implicitly return: `reversed = sort(array, { s1, s2 in s1 > s2 } )`
- with shorthand arguments: `reversed = sort(names, { $0 > $1 })`
- with trailing closure: `reversed = sort(names) { $0 > $1 }`
```objc
let result = array.map {
	(var arrayItem) -> String in
	// do stuff
	return something
}
```
- closures can capture constants and variables from their context
  - values that are not modified are captured as a copy
  - values that are modified are captured as a reference

# Enumerations
```objc
enum EnumName {
	case Foo, Bar, Baz, Qux
}
```
- `var aThing = EnumName.Foo`
- if type can be inferred: `aThing = .Bar`

## associated values
```objc
enum EnumName {
	case Foo(Int, Int)
	case Bar(String)
}
```
- `var aThing = EnumName.Foo(42, 1701)`
- in a switch: `case .Foo(let firstNumber, let secondNumber)` or if you want all of the values: `case let .Foo(firstNumber, secondNumber)`

## raw values
```objc
enum ASCIIControlCharacter: Character {
	case Tab = "\t"
	case LineFeed = "\n"
	case CarriageReturn = "\r"
}

enum InnerPlanet: Int {
	case Mercury = 1, Venus, Earth, Mars
}
```
- toRaw: let earthOrder = InnerPlanet.Earth.toRaw() // 3
- fromRaw: let planet = InnerPlannet.fromRaw(2)

## misc
- raw values are set in the definition, associated values are created when you create a constant/variable based on the enum

# Classes and Structures
- things they both do: define properties, methods, subscripts, and initializers; be extended; conform to protocols
- things only classes do: inherit another class, type cast, define deinitializers, have more than one reference to an instance (structures are always copied when passed)
```objc
struct Thing {
	var width = 0
	var height = 0
}
```
```objc
class Foo {
	var bar = Bar()
	var baz = false
	var qux: String?
}
```
- structures have an automatically-generated memberwise initializer: `let aThing = Thing(width: 100, height: 200)`
- structures and enumerations are value types, always copied when passed
- classes are reference types
- when you might want a struct: encapsulating a few simple data values, encapsulated values will be copied when passed, properties are also value types, no inheritance
- when dictionaries are assigned/passed, the dictionary is copied
- arrays are only copied when actions that might modify the length occur
  - ensure a unique array (does a copy if necessary): `array.unshare()`
  - `===` and `!==` check if arrays have the same storage and elements
  - shallow copy: `var otherArray = array.copy()` (always makes a copy, use `unshare` if you can)

# Properties
- when an instance of a value type is constant, so are all its properties
- `@lazy var = Foo()`
  - initial value not calculated until first time it's used, must be a var
- computed property
```objc
var center: Point {
	get {
		// do math
		return Point(x: centerX, y: centerY)
	}
	set(newCenter) {
		origin.x = // do math
		origin.y = // do math
	}
}
```
- more about computed properties
  - if setter doesn't define a name for new value, defaults to newValue
  - read-only computed property
```objc
var volume: Double {
	return width * height * depth
}
```
- property observers: can be added to any stored properties except lazy ones, not called when first initialized
```objc
var totalFoos: Int = 0 {
	willSet {
		println("Gonna have \(newValue) foos")
	}
	didSet {
		println("Now have \(oldValue) foos")
	}
}
```
- type properties: can be stored or computed for value types (structs and enums), only computed for classes, must always have a default value
```objc
struct AStruct {
	static var storedTypeProperty = "Foo"
	static var computedTypeProperty: Int {
		// return Int
	}
}
class AClass {
	class var computedTypeProperty: Int {
		// return Int
	}
}
```

# Methods
- by default first parameter has a local name and subsequent have local and external, e.g. `func incrementBy(amount: Int, numberOfTimes: Int) {}` can be called with `foo.incrementBy(5, numberOfTimes: 3)`
- use `_` as external parameter name if you don't want one
- `mutating func`: if you want to modify a struct/enum's properties in a method, can't call mutating method on a constant struct, can assign self to a new instance
- type methods: `class func` or `static func`

# Subscripts
- let you use `foo[bar]` syntax
- can be read-only or write-only
```objc
subscript(index: Int) -> Int {
	get {
		// return an Int
	}
	set (newValue) {
		// do something
	}
}
```
- read-only
```objc
subscript(index: Int) -> Int {
	return multiplier * index
}
```
- can take any number of input params of any type
- can return any type
- can use variable params and variadic params
- cannot use in-out params or provide default param values
- subscript overloading: classes/structs can define multiple subscript implementations

# Inheritance
- `class Subclass: Superclass {}`
- during initialization subclasses can only modify variable properties of superclasses, not inherited constant properties
- `override func`
- can make an inherited read-only property read-write by providing getter and setter
- cannot make an inherited read-write property read-only
- if you provide a setter as part of a property override, you must also provide a getter
- cannot provide both an overriding setter and an overriding property observer
- `@final`, can apply to methods, properties, subscripts, and classes

# Initialization
- classes and structs must set all stored properties to an initial value (either in an initializer or with a default value in the definition)
- setting property via default value or initializer does not call property observers
- prefer default value if it's always the same
- params: `init(fromFoo foo: Double) {}`
- an external name is provided for every parameter even if you don't provide one, use `_` as an explicit external name if you don't want one, external names must always be used when calling an initializer
- optional properties are automatically initialized as `nil`
- constant properties can be modified during initialization by the class that introduces it (cannot be modified by subclass)
- default initializer for structure or base class that provides default values for all properties and does not provide an initializer, also a memberwise initializer for structs
- value types (structs/enums) can use `self.init` with an initializer to refer to other initializers
- initializer rules:
  - designated initializers must call a di from their immediate superclass
  - convenience initializers must call another initializer in the same class
  - convenience initializers must eventually call a di
- 2-phase initialization checks:
  - di must set all properties introduced by the class before calling superclass initializer
  - di must call superclass initializer before setting inherited properties
  - convenience initializer must call another initializer before assigning values to any properties
  - initializer cannot call instance methods, read values of instance properties, or refer to self until after phase 1
- subclasses do not inherit superclass initializers by default
- override keyword is not necessary on an initializer
- when you inherit initializers:
  - default values are provided for properties introduced in a subclass
  - subclass doesn't define any designated initializers -> inherits designated initializers
  - (or) subclass inherits or overrides all of superclass's designated initializers (even as a convenience initializer) -> inherits convenience initializers
- `init(params) {}`, `convenience init(params) {}`
- can set default property value with closure or function
```objc
let property: TheType = {
	return value;
}();
```
  - the instance does not exist at this point, can't use self/properties/methods

# Deinitilization
- `denit {}`
- deallocation occurs after denit, can access instance's properties

# Automatic Reference Counting
- resolving reference cycles
  - `weak`: reference may become nil, must be vars, must have optional type
  - `unowned`: reference will never be nil after it is initialized, must have non-optional type, will trigger a runtime error if accessed after deallocation
  - unowned in one class + implicitly unwrapped optional in the other: both properties will always have a value
- reference cycles for closures
  - can refer to self in property's default closure if it is lazy
  - can get into scenario where closure refers to self (capturing it) and property holds a strong reference to its closure
  - capture list
  ```objc
@lazy var aClosure: (Int, String) -> String = {
	[unowned self] (index: Int, stringToProcess: String) -> String in
	// closure body
}
@lazy var anotherClosure: () -> String = {
	[unowned self] in
	// closure body
}
```

# Optional Chaining
- triggers runtime error if `residence` is `nil`: `let roomCount = john.residence!.numberOfRooms`
- with optional chaining: `let roomCount = john.residence?.numberOfRooms`, `roomCount` is an `Int?`
- use optional chaining to access a property on an optional value and to check if access was successful, cannot use optional chaining to set property
- methods: `if john.residence?.printNumberOfRooms() {}`, returns `Void` or `nil` depending on whether method was called successfully
- subscripts: `if let firstRoomName = john.residence?[0].name {}`, cannot set subscript with chaining
- multiple levels: `if let johnsStreet = john.residence?.address?.street {}`
- methods with optional return values: `if let upper = john.residence?.address?.buildingIdentifier()?.uppercaseString() {}`

# Type Casting
- `if foo is Bar {}`
- `as?` returns optional of the type you are casting to, `as` force-unwraps the result (triggers runtime error)
  - `if let foobar = foo as? Bar {}`
- `AnyObject`: any class type
- `Any`: any type except function types
- using an `AnyObject[]`: `for movie in someObjects as Movie[] {}`
- using an `Any[]`:
```objc
case let someDouble as Double where someDouble > 0
case is Double
case let (x, y) as (Double, Double)
```
- `as` is safe in a switch case statement

# Nested Types
- irrelevant but you can define enums like this:
```objc
enum Rank: Int {
	case Two = 2, Three, Four, Five, Six, Seven, Eight, Nine, Ten
	case Jack, Queen, King, Ace
}
```
- referring to a nested type: `let heartsSymbol = BlackjackCard.Suit.Hearts.toRaw()`

# Extensions
- capabilities: add computed properties and computed static properties, define instance methods and type methods, provide new initializers, define subscripts, define and use new nested types, make an existing type conform to a protocol
- `extension SomeType {}`
- cannot add stored properties or property observers for existing properties
- can only add convenience initializers, cannot add designated initializers or denitializers (must be provided by original class impl)
  - if you add an initializer to a value type that provides default values for all stored properties and does not have any custom initializers, you can call the default initializer and memberwise initializer from the extension's initializer (defaults still apply)

# Protocols
```objc
protocol SomeProtocol {
	var settable: Int { get set }
	var doesNotNeedToBeSettable: Int { get }
}
protocol AnotherProtocol {
	class var typeProperty: Int { get set }
}
```
```objc
struct Person: FullyNamed {
	var fullName: String
}
```
- protocol methods cannot specify defaults for params
```objc
protocol MethodProtocol {
	class func someTypeMethod()
}
protocol AnotherMethodProtocol {
	func random() -> Double
}
```
- method can be mutating, do not need to write `mutating` in implementation for a class
- example of calling a delegate: `delegate?.gameDidStart(self)`
- adding protocol with extension
```objc
extension SomeClass: SomeProtocol {
	func requiredMethod() {
		// do stuff
	}
}
```
- inheritance: `protocol InheritingProtocol: SomeProtocol, AnotherProtocol {}`
- composition: `func wishHappyBirthday(celebrator: protocol<Named, Aged>) {}`
- can check for protocol conformance (`is`/`as`) only if protocol marked with `@objc`, `@objc` protocols can only be adopted by classes (no structs/enums)
- optional requirements
  - check for implementation: `optionalMethod?(param)`
  - optional properties/methods always return optionals
  - can only be used if @objc
```objc
@objc protocol FooProtocol {
	@optional func bar(baz: Int) -> Int
	@optional var qux: Int { get }
}
```

# Generics
- `func swapValues<T>(inout a: T, inout b: T) {}`
```objc
struct Stack<T> {
	var items = T[]()
	mutating func push(item: T) {
		items.append(item)
	}
	mutating func pop() -> T {
		return items.removeLast()
	}
}
```
- `var stringStack = Stack<String>()`
- type constraints: `func someFunction<T: SomeClass, U: SomeProtocol>(someT: T, someU: U) {}`
- associated types: used in protocols
```objc
protocol Container {
	typealias ItemType
	mutating func append(item: ItemType)
	subscript(i: Int) -> ItemType { get }
}
```
  - conforming to protocol with associated type: `typealias ItemType = Int`, could also be a generic like `T`, swift can frequently infer this though
- where clauses: `func allItemsMatch<C1: Container, C2: Container where C1.ItemType == C2.ItemType, C1.ItemType: Equatable> (c1: C1, c2: C2) -> Bool {}`

# Advanced Operators
- arithmetic operators to not overflow by default (it is reported as an error), opt into overflow behavior with `&` (e.g. `&+`)
- bitwise operators: `~ (not), &, |, ^ (xor), <<, >>`
- operator functions
  - `@infix func + (left: Vector2D, right: Vector2D) -> Vector2D {}`
  - `@prefix func - (vector: Vector2D) -> Vector2D {}`, unary
  - `@postfix`, unary
  - `@assignment func += (inout left: Vector2D, right: Vector2D) { left = left + right }`, `inout` is required
- custom operators
  - available characters: `/ = - + * % < > ! & | ^ . ~`
  - declared at global level with `operator`; can be `prefix`, `infix`, or `postfix` (e.g. `operator prefix +++ {}`)
  - custom infix operators can specify precedence and associativity, e.g. `operator infix +- { associativity left precedence 140 }`

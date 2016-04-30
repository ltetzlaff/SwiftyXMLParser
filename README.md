# SwiftyXMLParser
[![Carthage compatible](https://img.shields.io/badge/Carthage-compatible-4BC51D.svg?style=flat)](https://github.com/Carthage/Carthage) ![Cocoapods compatible](https://cocoapod-badges.herokuapp.com/v/SwiftyXMLParser/badge.png)


Simple XML Parser implemented in Swift

# What's this?
This is a XML parser inspired by [SwiftyJSON](https://github.com/SwiftyJSON/SwiftyJSON) and  [SWXMLHash](https://github.com/drmohundro/SWXMLHash).

[NSXMLParser](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/Foundation/Classes/NSXMLParser_Class/) in Foundation framework is a kind of "SAX" parser. It has enough performance but is a little inconvenient. So we have implemented "DOM" parser wrapping it. 

# Feature
- [x] access XML Document with "subscript".
- [x] access XML Document as Sequence.
- [x] easy debugging XML pathes.

# Requirement
+ iOS 7.0+
+ Swift 2.0+

# Installation

### Carthage
#### 1. create Cartfile

```ruby:Cartfile
github "https://github.com/yahoojapan/SwiftyXMLParser"

```
#### 2. install
```
> carthage update
```

### CocoaPods
#### 1. create Podfile
```ruby:Podfile
platform :ios, '8.0'
use_frameworks!

pod "SwiftyXMLParser", :git => 'https://github.com/yahoojapan/SwiftyXMLParser.git'
```

#### 2. install
```
> pod install
```

# Example

```swift
    let string = "<ResultSet><Result><Hit index=\"1\"><Name>Item1</Name></Hit><Hit index=\"2\"><Name>Item2</Name></Hit></Result></ResultSet>"
    
    // parse xml document
    xml = try! XML.parse(string) 
    
    // access xml element
    let accessor = xml["ResultSet"] 

    // access XML Text
    let text = xml["ResultSet", "Result", "Hit", 0, "Name"].text {
        print("exsists path & text in XML Element")
    }

    // access XML Attribute
    let index = xml["ResultSet", "Result", "Hit"].attributes?["index"] {
        print("exsists path & an attribute in XML Element")
    }

    // enumerate child Elements in the parent Element
    for hit in xml["ResultSet", "Result", "Hit"] {
        print("enumarate existing XML Elements")
    }

    // check if the XML path is wrong
    if case .Failure(let error) =  xml["ResultSet", "Result", "TypoKey"] {
        print(error)
    }
```

# Usage
### 1. Parse XML
+ from String
```swift
let string = "<ResultSet><Result><Hit index=\"1\"><Name>Item1</Name></Hit><Hit index=\"2\"><Name>Item2</Name></Hit></Result></ResultSet>"

xml = try! XML.parse(string) // -> XML.Accessor
```
+ from NSData
```swift
let string = "<ResultSet><Result><Hit index=\"1\"><Name>Item1</Name></Hit><Hit index=\"2\"><Name>Item2</Name></Hit></Result></ResultSet>"
let data = string.dataUsingEncoding(NSUTF8StringEncoding)

xml = XML.parse(data) // -> XML.Accessor
```

### 2. Access child Elements
```swift
let element = xml["ResultSet"] // -> XML.Accessor
```

### 3. Access grandchild Elements
+ with String
```swift
let element = xml["ResultSet"]["Result"] // -> <Result><Hit index=\"1\"><Name>Item1</Name></Hit><Hit index=\"2\"><Name>Item2</Name></Hit></Result>
```
+ with Array
```swift
let path = ["ResultSet", "Result"]
let element = xml[path] // -> <Result><Hit index=\"1\"><Name>Item1</Name></Hit><Hit index=\"2\"><Name>Item2</Name></Hit></Result>
```
+ with Variadic
```swift
let element = xml["ResultSet", "Result"] // -> <Result><Hit index=\"1\"><Name>Item1</Name></Hit><Hit index=\"2\"><Name>Item2</Name></Hit></Result>
```
### 4. Access specific grandchild Element
```swift
let element = xml["ResultSet", "Result", "Hit", 1] // -> <Hit index=\"2\"><Name>Item2</Name></Hit>
```
### 5. Access attribute in Element
```swift
if let attributeValue = xml["ResultSet", "Result", "Hit", 1].attributes?["index"] {
  print(attributeValue) // -> 2
}
```
### 6. Access text in Element
+ with optional binding
```swift
if let text = xml["ResultSet", "Result", "Hit", 1, "Name"].text {
    print(text) // -> Item2
} 
```
+ with custom operation
```swift
struct Entity {
  var name = ""
}
let entity = Entity()
entity.name ?= xml["ResultSet", "Result", "Hit", 1, "Name"].text // assign if it has text
```
+ convert Int and assign
```swift
struct Entity {
  var name: Int = 0
}
let entity = Entity()
entity.name ?= xml["ResultSet", "Result", "Hit", 1, "Name"].int // assign if it has Int
```
and there are other syntax sugers, bool, url and double.
+ assign text into Array
```swift
struct Entity {
  var names = [String]()
}
let entity = Entity()
entity.names ?<< xml["ResultSet", "Result", "Hit", 1, "Name"].text // assign if it has text
```
### Check error
```swift
print(xml["ResultSet", "Result", "TypoKey"]) // -> "TypoKey not found."
```

### Access as SequenceType
+ for-in
```swift
for element in xml["ResultSet", "Result", "Hit"] {
  print(element.text)
}
```
+ map
```swift
xml["ResultSet", "Result", "Hit"].map { $0["Name"].text }
```

# License

This software is released under the MIT License, see LICENSE.

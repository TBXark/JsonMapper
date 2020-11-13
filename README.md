# JsonMapper

`JsonMapper` is a simple, fast and secure way to access Json.

Because `Any` can't be used in `Codable`, it can be replaced with `JSONElement` for properties of type `Any`.



## Installation

### Swift Package Manager

Add the dependency in your `Package.swift` file:

```swift
let package = Package(
    name: "myproject",
    dependencies: [
        .package(url: "https://github.com/TBXark/JsonMapper.git", .upToNextMajor(from: "1.4.0"))
        ],
    targets: [
        .target(
            name: "myproject",
            dependencies: ["JsonMapper"]),
        ]
)
```

### Carthage

Add the dependency in your `Cartfile` file:

```bash
github "TBXark/JsonMapper" ~> 1.4.0.
```

### CocoaPods

Add the dependency in your `Podfile` file:

```ruby
pod 'JsonMapper', :git=>'https://github.com/TBXark/JsonMapper.git', '~> 1.4.0
```

# Example

```swift

import XCTest
@testable import JsonMapper


final class JsonMapperTests: XCTestCase {
    struct Human: Codable {
        let age: Int
        let name: String
        let height: Double
        let extra: JSONElement // Any?
    }
    
    let dict = ["data": ["man": ["age": 10, "name": "Peter", "height": 180.0, "extra": [123, "123", [123], ["123": 123], true]]]]

    func testJSONMapper() throws {

        let json = JSONMapper(dict)
        // 直接获取
        XCTAssertEqual(json["data"]["man"]["age"].intValue, 10)

        // 使用Key获取
        XCTAssertEqual(json["data"]["man"]["height"].as(Double.self), 180.0)

        // 使用 dynamicMemberLookup 获取
        XCTAssertEqual(json.data.man.height.as(Double.self), 180.0)

        // 使用Keypath获取
        XCTAssertEqual(json[keyPath: "data.man.name"].as(String.self), "Peter")

    }
    
    func testJSONElement() throws {

        let json = try JSONElement(rawJSON: dict)
        // 使用dynamicMemberLookup直接获取
        XCTAssertEqual(json.data.man.age.intValue, 10)

        // 使用Key获取
        XCTAssertEqual(json["data"]["man"]["height"].decimalValue, 180.0)

        // 使用Keypath获取
        XCTAssertEqual(json[keyPath: "data.man.name"].stringValue, "Peter")
        
        // 将不确定类型对象解析为JSONElement
        XCTAssertEqual(json.data.man.extra.arrayValue?.first?.intValue , 123)
    }

    static var allTests = [
        ("testJSONMapper", testJSONMapper),
        ("testJSONElement", testJSONElement)
    ]
}

```

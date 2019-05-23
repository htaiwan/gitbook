# Chapter 22: Encoding and Decoding Types

------

## 大綱

- [Encodable and Decodable protocols](#1)
- [What is Codable?](#2)
- [Automatic encoding and decoding](#3)
- Encoding and decoding custom types
  - [JSONEncoder and JSONDecoder](#4)
- Renaming properties with CodingKeys
  - [CodingKey protocol and CodingKeys enum](#5)
- [Manual encoding and decoding](#6)
  - The encode function
  - The decode function
  - [encodeIfPresent and decodeIfPresent](#7)
- [Key points](#8)

------

<h2 id="1">Encodable and Decodable protocols</h2>

- Swift中encode和decodable的protocols

```swift
// encode
func encode(to: Encoder) throws

// decodable
init(from decoder: Decoder) throws
```

------

<h2 id="2">What is Codable?</h2>

- Codable = Encodable + Decodable

```swift
typealias Codable = Encodable & Decodable
```

------

<h2 id="3">Automatic encoding and decoding</h2>

- 基本上, 所有在swift standard library的type都是Codable. ex. Int, Array, String, Date…
- 任何Foundation framework中的type也是Codable。
- 如果想要自己custom type也是Codable, 只要這個type中所有stored properties也是Codable, 那麼這個type也自動是Codable。

```swift
// Employee要是Codable
struct Employee: Codable {
  var name: String
  var id: Int
  // Toy一定也要是Codable
  var favoriteToy: Toy?
}

struct Toy: Codable {
  var name: String
}

```

------

<h2 id="4">JSONEncoder and JSONDecoder</h2>

- 一旦type是Codable, 就可以利用JSONEncoder, 將資料轉成data。

> “By design, it’s specified at compilation time as it prevents a security vulnerability where someone on the outside might try to inject a type you weren’t expecting. It also plays well with Swift’s natural preference for static types.”
>

```swift
let toy1 = Toy(name: "Teddy Bear");
let employee1 = Employee(name: "John Appleseed", id: 7, favoriteToy: toy1)

let jsonEncoder = JSONEncoder()
let jsonData = try jsonEncoder.encode(employee1)

let jsonString = String(data: jsonData, encoding: .utf8)!
print(jsonString)
// {"name":"John Appleseed","id":7,"favoriteToy":{"name":"Teddy Bear"}}

let jsonDecoder = JSONDecoder()
let employee2 = try jsonDecoder.decode(Employee.self, from: jsonData)
```

------

<h2 id="5">CodingKey protocol and CodingKeys enum</h2>

- 利用CodingKey進行key的轉換

```swift
struct Employee: Codable {
  var name: String
  var id: Int
  var favoriteToy: Toy?

  // 1. 宣告一個nested enum type
  // 2. confirm CodingKey
  // 3. 設定raw type, 不是String就是Int
  enum CodingKeys: String, CodingKey {
    // 4. 必須把所有case都寫上，即使不需要進行轉換的key
    case id = "employeeId"
    case name
    case favoriteToy
  }
}
```

------

<h2 id="6">Manual encoding and decoding</h2>

- 如果想要json進行格式轉換(非只是key轉換)

```json
{ "employeeId": 7, "name": "John Appleseed", "favoriteToy": {"name": "Teddy Bear"}}

{ "employeeId": 7, "name": "John Appleseed", "gift": "Teddy Bear" }
```

```Swift
struct Employee {
  var name: String
  var id: Int
  var favoriteToy: Toy?

  enum CodingKeys: String, CodingKey {
    case id = "employeeId"
    case name
    case gift
  }
}

// 手動改寫Encodable
extension Employee: Encodable {
  func encode(to encoder: Encoder) throws {
    // 取得encoder的container
    var container = encoder.container(keyedBy: CodingKeys.self)
    // 重新encode
    try container.encode(name, forKey: .name)
    try container.encode(id, forKey: .id)
    // 將favoriteToy?.name取出來，直接mapping到新的key
    try container.encode(favoriteToy?.name, forKey: .gift)
  }
}

// 手動改寫Decodable
extension Employee: Decodable {
  init(from decoder: Decoder) throws {
    let values = try decoder.container(keyedBy: CodingKeys.self)
    name = try values.decode(String.self, forKey: .name)
    id = try values.decode(Int.self, forKey: .id)
    // 反向處理
    if let gift = try values.decode(String.self, forKey: .gift) {
      favoriteToy = Toy(name: gift)
    }
  }
}
```

------

<h2 id="7">encodeIfPresent and decodeIfPresent</h2>

- 如果當employees沒有 favorite toy就會導致轉成git產生null value。

```json
{"name":"John Appleseed","gift":null,"employeeId":7}
```

```swift
extension Employee: Encodable {
  func encode(to encoder: Encoder) throws {
    var container = encoder.container(keyedBy: CodingKeys.self)
    try container.encode(name, forKey: .name)
    try container.encode(id, forKey: .id)
    // 利用encodeIfPresent, 當employees沒有favorite toy就不會產生gift key
    try container.encodeIfPresent(favoriteToy?.name, forKey: .gift)
  }
}

extension Employee: Decodable {
  init(from decoder: Decoder) throws {
    let values = try decoder.container(keyedBy: CodingKeys.self)
    name = try values.decode(String.self, forKey: .name)
    id = try values.decode(Int.self, forKey: .id)
    // 利用decodeIfPresent, 當有gift key才轉換成當employees的favorite toy名字
    if let gift = try values.decodeIfPresent(String.self, forKey: .gift) {
      favoriteToy = Toy(name: gift)
    }
  }
```

------

<h2 id="8">Key points</h2>

- You need to encode (or serialize) an instance before you can save it to a file or send it over the web.
- You need to decode (or deserialize) to bring it back from a file or the web as an instance.
- Your type should conform to the Codable protocol to support encoding and decoding.
- If all stored properties of your type are Codable, then the compiler can automatically implement the requirements of Codable for you.
- JSON is the most common encoding in modern applications and web services, and you can use JSONEncoder and JSONDecoder to encode and decode your types to and from JSON.
- Codable is very flexible and can be customized to handle almost any valid JSON.
- Codable can be used with serialization formats beyond JSON.
  
# Chapter 25: Priority Queue Challenges

> Challenge 1:
>
> You have learned to use a heap to construct a priority queue by conforming to the Queue protocol. Now, construct a priority queue using an Array.

- 思路: 用array來做Priority Queue, 就是把array的內容先排序一次

> Within the init method, you leverage the array's sort function. Accord to Apple the sort function takes O(n log n) time.

```swift
public struct PriorityQueueArray<T: Equatable>: Queue {

    // 利用array來存elements
    private var elements: [T] = []
    let sort: (Element, Element) -> Bool

    public var isEmpty: Bool {
        return elements.isEmpty
    }

    public var peek: T? {
        return elements.first
    }

    // 建立init
    public init(sort: @escaping(Element, Element) -> Bool, elements: [Element] = []) {
        self.sort = sort
        self.elements = elements
        // 將element進行排序
        // Accord to Apple the sort function takes O(n log n) time.
        // Swift's sort function uses introsort which is a combination of insertion sort and heap sort.
        self.elements.sort(by: sort)
    }

    public mutating func enqueue(_ element: T) -> Bool {
        //  O(n)
        for (index, otherElement) in elements.enumerated() {
            // 檢查element是否有更高priority
            if sort(element, otherElement) {
                elements.insert(element, at: index)
                return true
            }
        }
        // If the element does not have a higher priority than any element in the queue, simply append the element to the end
        elements.append(element)
        return true
    }

    public mutating func dequeue() -> T? {
        // O(n) operation, since you have to shift the existing elements to the left by one.
        return isEmpty ? nil : elements.removeFirst()
    }

}
```



------

> Challenge 2:
>
> Your favorite T-Swift concert was sold out. Fortunately there is a waitlist for people who still want to go! However the ticket sales will first prioritize someone with a military background, followed by seniority. Write a sort function that will return the list of people on the waitlist by the priority mentioned. The Person struct is provided below:

- 思路: 如何寫一個自己客製化的sort

```swift
func tswiftSort(person1: Person, person2: Person) -> Bool {
    // 如果都是軍人身份或都不是，那就比年紀
    if person1.isMilitary == person2.isMilitary {
        return person1.age > person2.age
    }

    // 不然就比誰有軍人身份
    return person1.isMilitary
}

let p1 = Person(name: "Josh", age: 21, isMilitary: true)
let p2 = Person(name: "Jake", age: 22, isMilitary: true)
let p3 = Person(name: "Clay", age: 28, isMilitary: false)
let p4 = Person(name: "Cindy", age: 28, isMilitary: false)
let p5 = Person(name: "Sabrina", age: 30, isMilitary: false)

let waitlist = [p1, p2, p3, p4, p5]

var priorityQueue = PriorityQueue(sort: tswiftSort, elements: waitlist)
while !priorityQueue.isEmpty {
    print(priorityQueue.dequeue()!)
}
```


---
layout: post
title: 《Swift进阶》阅读笔记 - 集合类型
date: 2017-03-13 22:03:06 +0800
tags: [Swift, 阅读笔记]
categories: [Code, Swift]
---

## 数组
### 高阶函数

实现map函数，把for循环中的代码模版部分用一个泛型函数封装起来。下面是可能的实现方式：

```Swift
extension Array {
	  func map<T>(_ transform: (Element) -> T) -> [T] {
        var result: [T] = []
        result.reserveCapacity(count)
        for x in self {
		      result.append(transform(x))
        }
        return result
     }
}
```

注：如果明确知道数组的大小，可以调用reserveCapacity(_:)方法，避免在添加元素过程中重复申请内存。[array-reservecapacity](https://developer.apple.com/reference/swift/array/1538966-reservecapacity)

* map和flatMap —— 如何对元素进行变换

```Swift
let numbers = [1, 2, 3, 4, 5]
let squareMapNumbers = numbers.map { $0 * 2}
print(squareMapNumbers) //[2, 4, 6, 8, 10]
let squareFlatMapNumbers = numbers.flatMap { $0 * 2}
print(squareFlatMapNumbers) //[2, 4, 6, 8, 10]
```

可以看到map跟flatMap的结果是相同的，那么map跟flatMap的不同之处在哪呢，当对二维数组进行操作时就可以看出不同了：

```Swift
let numbersCompound = [[1,2,3],[4,5,6]];
let mapCompound = numbersCompound.map { $0.map{ $0 + 2 } }
print(mapCompound) // [[2, 4, 6], [8, 10, 12]]
let flatMapCompound = numbersCompound.flatMap { $0.map { $0 + 2 } }
print(flatMapCompound) // [2, 4, 6, 8, 10, 12]
```

可以看到二维数组经过`map`转换还是二维数组，而经过`flatMap`转换后被拍扁了，变成了一维数组。

当数组中含有nil时，flatMap也会把nil去掉。

```Swift
let string = ["ab", "cc", nil, "dd"]
let flatMapString = string.flatMap { $0 }
print(flatMapString) // ["ab", "cc", "dd"]
// 用filter和map组合实现flatMap过滤nil值的效果
let mapString = string.filter { $0 != nil}.map { $0!}
print(mapString) // ["ab", "cc", "dd"]
```

flatMap还可以用于将不同数组里的元素进行配对。

```Swift
let chineseNumbers = ["😢", "😊", "😓", "🐶", "🐱"]
let result = numbers.flatMap { number in
	chineseNumbers.map { chineseNumber in
		(number, chineseNumber)
	}
}
print(result) // [(1, "😢"), (1, "😊"), (1, "😓"), (1, "🐶"), (1, "🐱"), (2, "😢"), (2, "😊"), (2, "😓"), (2, "🐶"), (2, "🐱"), (3, "😢"), (3, "😊"), (3, "😓"), (3, "🐶"), (3, "🐱"), (4, "😢"), (4, "😊"), (4, "😓"), (4, "🐶"), (4, "🐱"), (5, "😢"), (5, "😊"), (5, "😓"), (5, "🐶"), (5, "🐱")]
```

详细的说明可以看这篇文章[谈谈 Swift 中的 map 和 flatMap](http://swiftcafe.io/2016/03/28/about-map/)

* filter —— 元素是否应该被包含在结果中

```Swift
let filterNumbers = [1, 2, 3, 4, 5]
let filteredNumbers = filterNumbers.filter { $0 > 3 }
print(filteredNumbers) //prints [4, 5]
```

* reduce —— 如何将元素合并到一个总和的值中

```Swift
let reduceNumbers = [1, 2, 3, 4 ,5]
let reducedNumber = reduceNumbers.reduce(0) { $0 + $1 }
print(reducedNumber) //prints 15
```

* sequence —— 序列中下一个元素应该是什么
* forEach —— 对于一个元素，应该执行怎样的操作，需要注意的是forEach中的return语句不会终止循环，仅仅是从闭包中返回而已。
* sort，lexicographicCompare和partition —— 两个元素应该以怎样的顺序进行排列
* index，first 和 contains — 元素是否符合某个条件
* min 和 max — 两个元素中的最小/最大值是哪个
* elementsEqual 和 starts — 两个元素是否相等
* split — 这个元素是否是一个分割符

```Swift
let name = "Firts Lase"
let nameArray = name.characters.split { $0 == " " }.map(String.init)
print(nameArray) // ["Firts", "Lase"]
```

### 切片

Array中允许通过指定下标范围来获取某一段的值：

```Swift
let slice = numbers[1..<numbers.endIndex]
print(slice) // [2, 3, 4, 5]
```

slice的类型为ArraySlice。需要注意的是slice与numbers一开始是共用存储空间的。在上述例子中如果企图访问slice[0]编译器是会报错的，显示越界访问。

## 字典

字典中通过键获取值所花费的评价时间是常数量级的。数组中搜寻一个特定元素所花的时间与数组尺寸成正比的。

### 可变性

字典可以通过下表修改和删除对应的值。如果需要删除某个值的话，将下标对应的值设为nil即可。如果需要返回删除的值可以调用`removeValue(forKey:)`。更新操作也是，如果需要返回更新前的值，则可以调用`updateValue(_:forKey:)`

### 有用的字典扩展

* 合并，用来合并的字典可以覆盖重复的值。

```Swift
extension Dictionary {
    mutating func merge<S>(_ other: S)
        where S: Sequence, S.Iterator.Element == (key: Key, value: Value) {
        for (k, v) in other {
            self[k] = v
        }
    }
}
```

必须是序列，以进行循环枚举，必须是key-value的形式。这样只要是key-value的形式都可以进行合并，不一定是字典。

* 使用一个key-value的序列来创建字典

```Swift
extension Dictionary {
    init<S: Sequence>(_ sequence: S)
         where S.Iterator.Element == (key: Key, value: Value) {
         self = [:]
         self.merge(sequence)
    }
}
```

* 对字典中的值进行映射

```Swift
extension Dictionary {
    func mapValues<NewValue>(transform: (Value) -> NewValue)
        -> [Key:NewValue] {
        return Dictionary<Key, NewValue>(map { (key, value) in
            return (key, transform(value))
        })
    }
}
```

## Set

集合Set是一组无序的元素，每个元素只会出现一次。测试集合中是否包含某个元素是一个常数时间的操作。

可以在一个集合中求另一个集合的补集，也可以求两个集合的交集，也可以求两个集合的并集。

## 集合类型协议

### 序列

Sequence协议是集合类型结构中的基础。一个序列代表一系列具有相同类型的值，你可以对这些值进行迭代。

Sequence协议：

```Swift
protocol Sequence {
    associatedtype Iterator: IteratorProtocol
    func makeIterator() -> Iterator
}
```

Sequence还有另外一个关联类型，叫做SubSequence：

```Swift
protocol Sequence {
    associatedtype Iterator: IteratorProtocol
    associatedtype SubSequence
}
```

在返回原序列的切片操作中，SubSequence被用作返回值的字类型：
* prefix和suffix——获取开头或者结尾n个元素
* dropFirst和dropLast——返回移除掉前n个或后n个元素的字序列
* split——将一个序列在指定的分隔元素时截断，返回子序列的数组

### 迭代器

IteratorProtocol协议中唯一的一个方法是next()，这个方法在每次调用时返回序列中的下一个值。当序列耗尽时返回nil：

```Swift
protocol IteratorProtocol {
	  associatedtype Element
	  mutating func next() -> Element?
}
```

### 集合类型

集合类型指的是稳定的序列，它们能够被多次遍历且保持一致。Collection协议是建立在Sequence 协议上的。在Sequence协议的基础上，Collection还提供了比如count属性的新能力。

```Swift
protocol Collection: Indexable, Sequence {
    /// 一个表示集合中位置的类型
	  associatedtype Index: Comparable
    /// 一个非空集合首个元素的位置
    var startIndex: Index { get }
    /// 集合中超过末位的位置，也就是比最后一个有效下标值大1的位置
    var endIndex: Index { get }
	  /// 返回在给定索引之后的那个索引值
	  func index(after i: Index) -> Index
    /// 访问特定位置的元素
    subscript(position: Index) -> Element { get }
}
```

遵守ExpressibleByArrayLiteral协议

遵守ExpressibleByArrayLiteral协议则可以以 [value1, value2, etc] 的方式创建一个队列：

```Swift
extension FIFOQueue: ExpressibleByArrayLiteral {
    public init(arrayLiteral elements: Element...) {
        self.init(left: elements.reversed(), right: [])
    }
}
```

## 索引

每个集合都有两个特殊的索引，startIndex和endIndex。startIndex指定集合中第一个元素，endIndex是集合中最后一个元素之后的位置。endIndex并不是一个有效的下标索引。集合类型的Index必须实现Comparable。[Comparable协议](https://developer.apple.com/reference/swift/comparable)

### 索引失效

失效有两层含义：
1. 索引本身有效，但是已经指向另外一个元素
2. 索引本身已经无效，通过它来访问集合会造成崩溃

### 链表

```Swift
/// 一个简单的链表枚举
enum List<Element> {
	  case end
	  indirect case node(Element, next: List<Element>)
}
```

indirect关键字允许一个枚举成员能够持有引用。

```Swift
extension List {
    /// 在链表前方添加一个值为`x`的节点，并返回这个链表
    func cons(_ x: Element) -> List {
        return .node(x, next: self)
    }
}
let list = List<Int>.end.cons(1).cons(2).cons(3)
print(list) // node(3, List<Swift.Int>.node(2, List<Swift.Int>.node(1, List<Swift.Int>.end)))
```

同样也可以添加ExpressibleByArrayLiteral协议。

```Swift
extension List: ExpressibleByArrayLiteral {
	init(arrayLiteral elements: Element...) {
		// 逆序操作，从根节点开始初始化
		self = elements.reversed().reduce(.end) { partialList, element in
			partialList.cons(element)
		}
	}
}
let list2: List = [3, 2, 1]
print(list2) // node(3, List<Swift.Int>.node(2, List<Swift.Int>.node(1, List<Swift.Int>.end)))
```

### 栈

定义一个通用的栈的协议：

```Swift
/// 一个进栈和出栈都是常数时间操作的后进先出（LIFO）栈
protocol Stack {
	/// 栈中存储的元素的类型
	associatedtype Element
	/// 将`x`入栈到`self`作为栈顶元素
	/// - 复杂度：O(1)
	mutating func push(_ x: Element)
	
	/// 从`self`移除栈顶元素，并返回它
	/// 如果`self`是空，返回`nil`
	/// - 复杂度：O(1)
	mutating func pop() -> Element?
}
```

为List添加Stack协议：

```Swift
extension List: Stack {
	
	mutating func push(_ x: Element) {
		self = self.cons(x)
	}
	
	mutating func pop() -> Element? {
		switch self {
		case .end:
			return nil
		case let .node(x, next:xs):
			self = xs
			return x
		}
	}
}
```

为List添加IteratorProtocol和Sequence协议，只需要添加next()方法：

```Swift
extension List: IteratorProtocol, Sequence {
	mutating func next() -> Element? {
		return pop()
	}
}
```


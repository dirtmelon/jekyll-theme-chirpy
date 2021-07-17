---
layout: post
title: 图片资源的管理方式
date: 2016-12-08 15:28 +0800
tags: [iOS, Swift]
categories: [Code, iOS]
---

在开发app的过程中少不了要加载app内的图片资源，最简单的写法就是
```Swift
let image = UIImage(named: "image")!
```
我们并不希望在加载app内图片过程中出错，所以都会进行强制解析。但是这样写多了会很乱，有可能会拼错，图片资源删除了也要一个个去修改，也不会在编译时就报错。有可能上线了才发现缺失图片资源。

# 静态属性方式
[Yep](https://github.com/CatchChat/Yep)中采取扩展的方法来处理。
```Swift
extension UIImage {
    static var xxx_image: UIImage {
        return UIImage(named: "image")!
    }
}
```
这样就可以通过调用`UIImage.xxx_image`来直接加载图片资源。[nix]()也提供了[脚步](https://github.com/nixzhu/dev-blog/blob/master/2016-08-11-awk.md)来批量生成需要的图片资源加载方法。
# Enum方式
Enum 方式同样也是通过扩展来进行处理，不过是添加了`enum`计算属性
先创建一个枚举变量，包含了所有的图片资源名字，然后创建便利构造器来根据`enum.rawValue`来加载图片资源。
```Swift
extension UIImage {
    enum AssetIentifier: String {
        case imageA, imageB, imageC, imageD
    }
    convenience init!(assetIdentifier: AssetIdentifier) {
        self.init(named: assetIdentifier.rawValue)
    }
}
```
通过以下方式进行调用：
```Swift
let image = UIImage(assetIdentifier: .imageA)
```
[SwiftGen](https://github.com/AliSoftware/SwiftGen)提供很好的生成工具。
# R.swift方式
也可以通过第三库[R.swift](https://github.com/mac-cain13/R.swift)来调用。
普通的调用方式：
```Swift
let settingsIcon = UIImage(named: "settings-icon")
let gradientBackground = UIImage(named: "gradient.jpg")
```
R.swift的调用方式：
```Swift
let settingsIcon = R.image.settingsIcon()
let gradientBackground = R.image.gradientJpg()
```
R.swift作用不限于此，还可以`Storyboards`,`Segue`等也采用同样的调用方式。
由于`R.swift`是在build时期运行的工具，并不是一个动态库。所以无法采取`Carthage`的方式来接入。现在在项目中也开始尝试使用`R.swift`。更安全和更便捷的调用资源。

如果仅仅是为了管理图片资源，可以自己仿照`Yep`的方式用脚本生成。也可以通过使用`SwiftGen`来生成。
如果想同时也对其它资源进行管理，可以试一下`R.swift`。
最不推荐的方法是东写一个`UIImage(named: "image")!`，西写一个`UIImage(named: "image")!`，这样不只是浪费时间，最重要的是如果对图片进行了修改或者删除，有可能造成崩溃。同时最好采用单元测试来检测所有的图片资源，确保万无一失。

> 通向荣誉的路上，并不铺满鲜花。

## 前言
用Java爬到我房间电表电量使用情况后，封装了一个接口，用于客户端的调用😝
在日常生活中，我用Mac的时间是最多的，如果将爬到的数据，展示在Mac的顶栏上，是一件很美好的事情😌
作为一个对Swift一无所知的我，花了两天时间，用Swift语言开发了一个Mac应用，接下来就跟大家分享下这个应用的开发过程，欢迎各为感兴趣的开发者阅读本文。

先跟大家看下最终实现的效果:
![](https://user-gold-cdn.xitu.io/2020/3/25/1710d937efe4d12c?w=1019&h=381&f=png&s=630185)

## 环境搭建
> Swift: 4.2.1

> Xcode: 10.1

> MacOS: 10.14.2

> 库管理工具: Carthage

> Alamofire: 4.0 (网络请求库)

> SwiftyJSON: 3.0 (Json解析库)

### 创建项目
* 打开Xcode，我们看到的界面如图所示
  * 左边为创建项目部分，右边为最近打开的项目
  * 点击图中用红款勾选的地方
![](https://user-gold-cdn.xitu.io/2020/3/25/1710d93bea637092?w=820&h=488&f=png&s=83879)
* 如图所示，按图中标明的序号，分别进行点击。
![](https://user-gold-cdn.xitu.io/2020/3/25/1710d93e391d5c25?w=1425&h=984&f=png&s=545996)
* 如图所示，分别填写项目相关信息
![](https://user-gold-cdn.xitu.io/2020/3/25/1710d943ea892330?w=748&h=544&f=png&s=53396)
* 选择项目创建位置
![](https://user-gold-cdn.xitu.io/2020/3/25/1710d946256db929?w=817&h=466&f=png&s=67131)
* 出现如图所示的页面后，项目创建成功
![](https://user-gold-cdn.xitu.io/2020/3/25/1710d948264eb0ea?w=1418&h=978&f=png&s=164623)

### 配置项目为一个菜单栏应用
此时项目为一个空白项目，点运行后会出现一个空的window窗口，同时dock上出现应用图标，这并不是我们要的，所以要添加配置来移除他们。
![](https://user-gold-cdn.xitu.io/2020/3/25/1710d94a6d10fc95?w=570&h=491&f=png&s=117973)

* 如图所示，在info下添加添加一个配置项
![](https://user-gold-cdn.xitu.io/2020/3/25/1710d94c931fdaf5?w=1418&h=978&f=png&s=222737)
* Key选择**Application is agent (UI Element)**,Value选择Yes
![](https://user-gold-cdn.xitu.io/2020/3/25/1710d95f847f77a6?w=687&h=312&f=png&s=58919)
* 此时再次运行程序，我们发现dock栏已经不显示程序图标，但是window窗口依然在
* 根据图中所示，删除**Window Controller Scene**和**View Controller Scene**
![](https://user-gold-cdn.xitu.io/2020/3/25/1710d96d16274b9e?w=1418&h=978&f=png&s=209777)
* 此时，我们在云心应用程序，发现窗口也没了。

### 在菜单栏创建图标
* 如图所示,点击**Assets.xcassets**文件，新建一个**Image Set**
![](https://user-gold-cdn.xitu.io/2020/3/25/1710d973f2cc6551?w=1421&h=984&f=png&s=436690)
* 如图所示，将其命名为**StatusIcon**，将自己中意的图片制作成3种规格，托入对应的位置。
![](https://user-gold-cdn.xitu.io/2020/3/25/1710d97ca9bea24f?w=881&h=320&f=png&s=46912)
* 拖入图标后，执行图片中的步骤，让图标适配系统的黑暗模式
![](https://user-gold-cdn.xitu.io/2020/3/25/1710d980e2d416ff?w=1140&h=787&f=png&s=105848)
* 打开AppDelegate.swift文件，添加如下代码
![](https://user-gold-cdn.xitu.io/2020/3/25/1710d9841bbdd090?w=792&h=410&f=png&s=66969)

```swift
    // 创建状态栏按钮
    let statusItem = NSStatusBar.system.statusItem(withLength: NSStatusItem.squareLength)
```
```swift
    // applicationDidFinishLaunching生命周期
    if let button = statusItem.button {
         button.image = NSImage(named: "StatusIcon")
    }
```
* 运行程序，我们发现菜单栏有了我们刚才设置的图标，此时点击后什么都不会发生。

### 添加**Popover**容器
* 在项目目录下，新建一个**Cocoa Class**，命名为**PopoverViewController**,此文件为点击时打开的弹层页面
![](https://user-gold-cdn.xitu.io/2020/3/25/1710d9908d63a59a?w=542&h=542&f=png&s=440488)
![](https://user-gold-cdn.xitu.io/2020/3/25/1710d9956953bf59?w=757&h=547&f=png&s=160021)
![](https://user-gold-cdn.xitu.io/2020/3/25/1710d997dda69e80?w=748&h=544&f=png&s=35952)
![](https://user-gold-cdn.xitu.io/2020/3/25/1710d99c199f709a?w=817&h=466&f=png&s=68053)
* 添加View Controller容器
![](https://user-gold-cdn.xitu.io/2020/3/25/1710d99d68a0fa2d?w=1420&h=976&f=png&s=651738)
* 如图所示，对上一步添加的容器进行修改
![](https://user-gold-cdn.xitu.io/2020/3/25/1710d99efc1266fb?w=1651&h=629&f=png&s=97301)
* 打开PopoverViewController.swift文件，在末尾添加如下代码

```swift
extension PopoverViewController {
    static func freshController() -> PopoverViewController {
        //获取对Main.storyboard的引用
        let storyboard = NSStoryboard(name: NSStoryboard.Name("Main"), bundle: nil)
        // 为PopoverViewController创建一个标识符
        let identifier = NSStoryboard.SceneIdentifier("PopoverViewController")
        // 实例化PopoverViewController并返回
        guard let viewcontroller = storyboard.instantiateController(withIdentifier: identifier) as? PopoverViewController else {
            fatalError("Something Wrong with Main.storyboard")
        }
        return viewcontroller
    }
}

```
* 打开AppDelegate.swift文件在class内添加如下代码
```swift
// 声明一个Popover
let popover = NSPopover()
```

### 创建显示/隐藏Popover的函数
* 在AppDelegate.swift文件的class内添加如下代码
```swift
    // 控制Popover状态
    @objc func togglePopover(_ sender: AnyObject) {
        if popover.isShown {
            closePopover(sender)
        } else {
            showPopover(sender)
        }
    }
    // 显示Popover
    @objc func showPopover(_ sender: AnyObject) {
        if let button = statusItem.button {
            popover.show(relativeTo: button.bounds, of: button, preferredEdge: NSRectEdge.minY)
        }
        
    }
    // 隐藏Popover
    @objc func closePopover(_ sender: AnyObject) {
        popover.performClose(sender)
    }
```
* 在AppDelegate.swift文件的**applicationDidFinishLaunching**函数中添加如下代码
```swift
    if let button = statusItem.button {
            button.image = NSImage(named: "StatusIcon")
            button.action = #selector(togglePopover(_:))
    }
    popover.contentViewController =            PopoverViewController.freshController()
```
* 此时，运行项目，点击菜单栏的应用图标，会显示弹层，再次点击弹层会消失
![](https://user-gold-cdn.xitu.io/2020/3/25/1710d9aa09843c8d?w=498&h=366&f=png&s=171398)

### 优化Popover
执行完上个步骤后，我们会发现弹层只会在点击时关闭或者消失，接下来我们来优化下，失去焦点时，也让它隐藏
* 新建一个EventMonitor.swift文件，用于事件监听,此文件代码如下
```swift
import Cocoa

public class EventMonitor {
    private var monitor: Any?
    private let mask: NSEvent.EventTypeMask
    private let handler: (NSEvent?) -> Void
    
    public init(mask: NSEvent.EventTypeMask, handler: @escaping (NSEvent?) -> Void) {
        self.mask = mask
        self.handler = handler
    }
    
    deinit {
        stop()
    }
    
    public func start() { //开启监视器
        monitor = NSEvent.addGlobalMonitorForEvents(matching: mask, handler: handler)
    }
    
    public func stop() { //关闭监视器
        if monitor != nil {
            NSEvent.removeMonitor(monitor!)
            monitor = nil
        }
    }
}

```
* 打开AppDelegate.swift在class中声明这个监视器
```swift
// 声明监视器
var eventMonitor: EventMonitor?
```
* 在applicationDidFinishLaunching函数中添加
```swift
eventMonitor = EventMonitor(mask: [.leftMouseDown, .rightMouseDown]) { [weak self] event in
  if let strongSelf = self, strongSelf.popover.isShown {
    strongSelf.closePopover(event!)
  }
}
```
* 修改showPopover和closePopover函数
```swift
    // 显示Popover
    @objc func showPopover(_ sender: AnyObject) {
        if let button = statusItem.button {
            popover.show(relativeTo: button.bounds, of: button, preferredEdge: NSRectEdge.minY)
        }
        eventMonitor?.start()
        
    }
    // 隐藏Popover
    @objc func closePopover(_ sender: AnyObject) {
        popover.performClose(sender)
        eventMonitor?.stop()
    }
```
* 运行项目，我们发现失去焦点后，弹层隐藏掉了。

### 添加右键菜单
* 如图所示，添加menu组件进来
![](https://user-gold-cdn.xitu.io/2020/3/25/1710d9aed82349f3?w=1634&h=773&f=png&s=244591)
* 将 Menu 与 AppDelegate.swift 建立联系
![](https://user-gold-cdn.xitu.io/2020/3/25/1710d9b21411809d?w=291&h=429&f=png&s=157270)
![](https://user-gold-cdn.xitu.io/2020/3/25/1710d9b605a42b0f?w=1392&h=455&f=png&s=124322)
![](https://user-gold-cdn.xitu.io/2020/3/25/1710d9bcdf877e65?w=330&h=189&f=png&s=25356)
* 删除多余项，添加退出图标和条目
![](https://user-gold-cdn.xitu.io/2020/3/25/1710d9bf89b8d456?w=1321&h=708&f=png&s=202399)
* 修改AppDelegate.swift文件，添加Handler来接管togglePopover
```swift
// 接管togglePopover
    @objc func mouseClickHandler() {
        if let event = NSApp.currentEvent {
            switch event.type {
            case .leftMouseUp:
                togglePopover(popover)
            default:
                statusItem.menu = Menu
                statusItem.button?.performClick(nil)
            }
        }
    }
```
* statusItem.button添加如下代码
```swift
// 点击事件
 button.action = #selector(mouseClickHandler)
 button.sendAction(on: [.leftMouseUp, .rightMouseUp])
```
* 在AppDelegate.swift的末尾添加
```swift
extension AppDelegate: NSMenuDelegate {
    // 为了保证按钮的单击事件设置有效，menu要去除
    func menuDidClose(_ menu: NSMenu) {
        self.statusItem.menu = nil
    }
}

```
* 在applicationDidFinishLaunching内添加
```swift
// 修复按钮单击事件无效问题
Menu.delegate = self
```
* 此时右键，我们发现已成功添加
![](https://user-gold-cdn.xitu.io/2020/3/25/1710d9d0e4089b76?w=207&h=127&f=png&s=22485)

### 实现退出功能
* 如图所示，将推出函数关联至AppDelegate文件下
![](https://user-gold-cdn.xitu.io/2020/3/25/1710d9d40bbde690?w=1606&h=647&f=png&s=337110)
* 完善退出app函数
```swift
    // 关闭App
    @IBAction func Quit(_ sender: Any) {
        NSApplication.shared.terminate(self)
    }
```
* 再次运行后，右键，点击推出，即可关闭应用

## 编写Popover页面
执行完上述步骤后，我们创建了一个空的Popover,接下来我们往Popover添加内容，调用接口，显示我们房间电表电量使用情况。

### 布局页面
* 如图所示,添加Text Field和label组件，拖拽至PopoverViewController中，并与PopoverViewController.swift进行关联
![](https://user-gold-cdn.xitu.io/2020/3/25/1710d9d8f13ab865?w=685&h=768&f=png&s=316966)
![](https://user-gold-cdn.xitu.io/2020/3/25/1710d9dc577402b0?w=1645&h=556&f=png&s=275660)
* 调整拖出来的控件大小，搞成如图所示的样子
![](https://user-gold-cdn.xitu.io/2020/3/25/1710d9dd9c3fc162?w=538&h=532&f=png&s=33339)

### 安装Cartfile库管理工具
[Cartfile](https://github.com/Carthage/Carthage)是一个优秀的库管理工具，相当于我们前端的npm。

* 点击[Cartfile下载地址](https://github.com/Carthage/Carthage/releases)，进入Cartfile的github仓库的下载页面，选择pkg文件下载，然后安装。
![](https://user-gold-cdn.xitu.io/2020/3/25/1710d9e4896289dd?w=944&h=521&f=png&s=63778)

### 安装网络请求库和Json解析库
> Alamofire,为一个优秀的网络请求库，他封装了各种http请求。

> SwiftyJSON,为一个优秀的json解析库

我们可以通过Cartfile来获取他们

* 在我们的项目根目录创建Cartfile文件，并添加如下内容
```bash
github "Alamofire/Alamofire" ~> 4.0
github "SwiftyJSON/SwiftyJSON" ~> 3.0
```
* 打开终端，进入到我们项目的根目录，执行如下命令
```bash
carthage update --platform macOS
```
* 执行完毕后，我们发现，项目的根目录下多了Carthage文件夹
![](https://user-gold-cdn.xitu.io/2020/3/25/1710d9e8d20e85dc?w=788&h=454&f=png&s=78244)
* 此时我们打开，xcode，打开如图所示的页面
![](https://user-gold-cdn.xitu.io/2020/3/25/1710d9ebd63d0b2d?w=1418&h=978&f=png&s=190138)
* 如图所示，选择我们刚才Carthage文件夹下的，build->Mac->framework文件
![](https://user-gold-cdn.xitu.io/2020/3/25/1710d9ed5588449d?w=822&h=317&f=png&s=166007)
* 添加成功后，如图所示
![](https://user-gold-cdn.xitu.io/2020/3/25/1710d9ee68e5e93c?w=1188&h=726&f=png&s=135965)

### 开启网络访问
Xcode默认不允许http请求，按照如图所示的操作进行即可。
![](https://user-gold-cdn.xitu.io/2020/3/25/171122bca2dc187f?w=986&h=574&f=png&s=155646)

### 调用接口渲染页面
> 在PopoverViewController.swift文件中添加如下代码
```swift
import Cocoa
import Alamofire
import SwiftyJSON

class PopoverViewController: NSViewController {
    // 今日用电
    @IBOutlet weak var electricityToday: NSTextField!
    // 本月已用
    @IBOutlet weak var currentMonthBatteryTotal: NSTextField!
    // 剩余电量
    @IBOutlet weak var remainingBattery: NSTextField!
    // 统计时间
    @IBOutlet weak var time: NSTextField!

    private var timer: Timer?
    // 定时器记数: 每20分钟执行一次，3轮为1小时
    private var timeCount = 3
    
    override func viewDidLoad() {
        super.viewDidLoad()
        // 获取并设置页面数据
        setPageData()
        // 启动定时器
        loop()
    }
    
    // 获取并设置数据
    func setPageData(){
        // 发起post请求
        Alamofire.request("https://www.xxx.com",method: .post,parameters: ["userName":"xxx","password":"xxx"],encoding: JSONEncoding.default).responseJSON { (response) in
            switch response.result {
            // 请求成功
            case .success(let resData):
                // 将返回的数据转为JSON对象
                let jsonData = JSON.init(resData as Any)
                // 变量赋值
                self.electricityToday.stringValue = jsonData["data"]["electricityToday"].string!
                self.currentMonthBatteryTotal.stringValue = jsonData["data"]["currentMonthBatteryTotal"].string!
                self.remainingBattery.stringValue = jsonData["data"]["remainingBattery"].string!
                self.time.stringValue = jsonData["data"]["time"].string!
                break
            case .failure(let error):
                print("接口调用失败")
                print(error);
                break
            }
        }
    }
    
    // GCD 方式的定时器，循环
    func loop() {
        print("\(Date()): 定时器初始化")
        // timeInterval: 隔多少秒执行一次
        timer = Timer(timeInterval: 1200, repeats: true, block: { timer in
            self.loopFireHandler(timer)
        })
        // 添加定时器
        RunLoop.main.add(timer!, forMode: .common)
    }
    // 定时器需要执行的内容
    @objc private func loopFireHandler(_ timer: Timer?) -> Void {
        // 定时器执行结束结束
        if self.timeCount <= 0 {
            print("\(Date()): 执行完1轮，开始下一轮")
            self.timeCount = 3
            return
        }
        // 获取并设置页面数据
        setPageData()
        // 执行完分钟
        self.timeCount -= 1
      
    }

}


extension PopoverViewController {
    static func freshController() -> PopoverViewController {
        //获取对Main.storyboard的引用
        let storyboard = NSStoryboard(name: NSStoryboard.Name("Main"), bundle: nil)
        // 为PopoverViewController创建一个标识符
        let identifier = NSStoryboard.SceneIdentifier("PopoverViewController")
        // 实例化PopoverViewController并返回
        guard let viewcontroller = storyboard.instantiateController(withIdentifier: identifier) as? PopoverViewController else {
            fatalError("Something Wrong with Main.storyboard")
        }
        return viewcontroller
    }
}


```

## 写在最后
如何爬取你房间内电表使用情况，请移步这篇文章:[Java爬取电表电量使用情况](https://juejin.im/post/5e75dcbde51d45271515811b)

## 说明

**我们写代码, 是为了让自己和别人更好地 Copy**.   
所以, 良好的编程习惯是必要的. 而且, 严格遵循优秀的编码规范也能够很大程度上提高自己的开发效率, 很多细枝末节的东西你不需要花时间去考虑, 前人已经写下了最佳实践.  
该规范主要参考了 Apple API 设计风格和[禅与 Objective-C 编程艺术 ](https://github.com/oa414/objc-zen-book-cn), 以及一些个人学习经验的总结. 

## 命名规范

### 基本原则

- 尽可能与 Cocoa 编程风格保持一致
- 越重要, 区别度越大的语素越要往前放
- 命名应该尽可能的清晰和简洁
- 整个工程的命名风格要保持一致性, 不同类中完成相似功能的方法应该叫相似的名字

### 类名

- 类的前缀, 所有类名最好都加一个统一的标示符, 可以是公司, 组织缩写, 或者项目的名称缩写
- 类的后缀, 所有的试图控制器都加上 ViewController, 自定义容器控制器加上
 Container, 所有的通知名都加上 Notification

### 方法名
- 以 alloc, copy, init, mutableCopy, new 开头的方法要注意, 它们会改变 ARC 的行为, 不要使用
- 以 get, set 开头的方法有特殊的意义, 不要随意定义. set 是属性默认的设置方法, 如果函数不是为了设置类成员, 则不要用 set 开头, 可用 setup 替代. get 在 Objective-C 中通常只用来表示从函数指针返回值的函数
-  不要用 and 来连接两个参数

### 协议名

- 好的协议名应能立刻让人分辨出这不是一个类名, 除了以常用的 delegate, dateSource 做结尾外, 还可以使用 …ing 这种形式, 如: NSCoding, NSCopying, NSLocking

### 通知名

- 基本命名格式是: [与通知相关的类名] + [Did | Will] + [UniquePartOfName] + Notification  
比如: kUserInfoDidUpdateNotification
- 另外, 尽量不要使用通知, 它可能会让你的代码难以维护, 当你想要使用的通知的时候, 你应当足够地评估它的必要性

### 临时变量命名

- 临时变量可以写得很短, 如 tmp, k, vc 这样, 也可以加适当前缀

### 常量命名

- 宏常量加 k 前缀
- 建议使用 NS_ENUM 和 NS_OPTIONS 宏来定义枚举类型
- 除非调试用的, 控制不同编译模式行为的常量可用宏外, 其他常量尽量不用宏定义

## 代码格式化

### 空格

- 使用4个空格代替制表符  
- 方法声明在方法类型与返回类型之间要有空格
- 方法返回值, 参数, 变量定义和声明, 指针符号 * 前面要有空格

		- (UIImage *)imageWithColor:(UIColor *)color;
- 条件判断的括号内侧不应有空格
- 关系运算符 (如 >=, !=) 和逻辑运算符 (如 &&, ||) 两边要有空格

		if(age > 14 && (gender == famale)) {
			[self doSonmething];
		}
- 属性定义, 数组定义等逗号后留一个空格

		NSArray *numbers = @[@1, @2, @3];
- 左花括号前要有空格
- 注释中中英文之间要有空格

### 花括号

- 方法, if, else, switch 的左花括号不换行 
- 右花括号单独一行
- if, else 代码块只有一行代码花括号不省略

### 折行

- 与多数其他规范不同, 不建议按照80限制手动折行, 太长可手动折行

### 字面值语法

- 应该使用可读性更好的字面值语法来构造 NSArray, NSDictionary 等数据结构

		NSMutableArray *members = @[].mutablecopy;
- 避免使用 Format

		NSLog(@"点击了第 %@ 行", @(indexPath.row))

## 布局

- 采用纯代码布局, 动态布局使用 Masonry
- 自定义 View, 不要在 ViewController 中有大量组装 View 的代码, 可以自定义 View, 然后将相关的事件响应用代理用着 block 传递给 Controller
- 不可以在 layoutSubviews 中使用 autolayout
- 每一个自定义 View 有一个 build 方法, 在 build 方法中完成子视图的创建, 不要在 init 方法中直接初始化和构建子视图

## 代码组织

- 不要在 viewDidLoad 里面初始化 View 然后再 add, 可以使用懒加载或者为每一个 View 构造一个 build 方法
- 注意提取通用 View 和自定义 View 的封装, 以减少 ViewController 中 View 的初始化
- 使用 `#pragma mark` 将代码分成不同的块

		#Pragma mark - Life
		#Pragma mark - Getters & Setters
- 提取工具方法, 若对应于某 Foundation 类, 建议添加到分类中, 分类方法建议加前缀
- ViewController 中不应该有业务逻辑相关的方法
- ViewController 应该是独立的, 它不需要知道其他 ViewController 的视图层级或者内部实现
- ViewController 之间的通信必须定义明确的公共接口, 比如 Protocol
- 建议不要重用 ViewController

## 单例

- 如果可能, 请尽量避免使用单例而是依赖注入
- 尝试着把单例作为一个对象的容器, 在代码或者应用层面上共享, 是一个糟糕和丑陋的设计

## 注释

- 重要接口或属性可以添加注释
- 推荐使用文档注释

		/**
		 简要描述
		*/
- 修改了实现影响到接口的使用的, 一定要修改注释
- 不要出现被注释掉的代码, 如果是出于特殊原因的, 一定要为这些代码添加注释

## 接口定义

- 接口尽可能精简
- 接口中不要使用可变数据类型

## 属性
- 尽管使用实例变量是一种有效的方式, 但更偏向于使用属性来保持代码一致性
- 除非必要, 否则不要手动合成属性
- 在 init 和 dealloc 中不要用存取方法访问实例变量
- NSString, NSArray 等有可变类型的数据类型需要使用 copy 语义
- 统一空格和语义等的顺序, 比如: 

	    @property (nonatomic, readwrite, copy) NSString *title;

## 其他编码风格

- 不要使用 new 方法
- BOOL 的使用, 永远不要将 BOOL 类型变量直接和 YES 比较
- nil 检查, 不要使用 nil == Object
- 尽量不使用 PCH 文件
# LXIntrospection

这是一个简单的 `NSObject` 分类，改进自 [garnett/DLIntrospection](https://github.com/garnett/DLIntrospection)，可以查看类中的方法、实例变量、属性、协议等等，并做了格式化和类型解析处理，使日志信息更加美观、详细。在原作者的基础上修复了一些 bug，完善了对结构体的解析，但是对象类型的方法参数和返回值依旧只能解析成 `id` 类型，`block` 和函数指针的具体类型无法解析。

## 在 Objective-C 中使用

### 查看属性

```Objective-C
(lldb) po [UIView lx_propertyDescriptionList]
(
    @property (nonatomic, assign) int action,
    @property (atomic, copy, readonly) NSString *description,
    @property (nonatomic, assign) CGAffineTransform transform,
    @property (nonatomic, assign) BOOL skipsSubviewEnumeration,
    @property (nonatomic, strong) UIColor *interactionTintColor,
    @property (nonatomic, assign, readonly) CGSize _sensitivitySize,
    @property (nonatomic, weak, readonly) UIView *preferredFocusedView,
    @property (atomic, assign, readonly) NSLayoutXAxisAnchor *leadingAnchor,
    @property (nonatomic, assign, getter=_viewDelegate, setter=_setViewDelegate:) UIViewController *viewDelegate,
    ...
)
```

### 查看实例变量

```Objective-C
(lldb) po [UIView lx_ivarDescriptionList]
(
    NSMutableArray *_constraintsExceptingSubviewAutoresizingConstraints,
    UITraitCollection *_cachedTraitCollection,
    CALayer *_layer,
    CALayer *_layerRetained,
    id _gestureInfo,
    NSMutableArray *_gestureRecognizers,
    NSArray *_subviewCache,
    float _charge,
    UIEdgeInsets _inferredLayoutMargins,
    ...
)
```

### 查看实例方法

```Objective-C
(lldb) po [UIView lx_instanceMethodDescriptionList]
(
    - (void)_web_setSubviews:(id)arg0,
    - (id)_recursiveFindDescendantScrollViewAtPoint:(CGPoint)arg0 withEvent:(id)arg1,
    - (id)_findDescendantViewAtPoint:(CGPoint)arg0 withEvent:(id)arg1,
    - (BOOL)_accessibilityCanBecomeNativeFocused,
    ...
)
```

### 查看类方法

```Objective-C
(lldb) po [UIView lx_classMethodDescriptionList]
(
    + (id)_axFocusedWindowSubviews,
    + (id)_accessibilityTitleForSystemTag:(long)arg0,
    + (Class)safeCategoryBaseClass,
    + (void)_accessibilityPerformValidations:(id)arg0,
    + (id)_accessibilityElementsAndContainersDescendingFromViews:(id)arg0 options:(id)arg1 sorted:(BOOL)arg2,
    ...
)
```

### 查看采纳的协议

```Objective-C
(lldb) po [UIView lx_adoptedProtocolDescriptionList]
(
    _UIScrollNotification <NSObject>,
    UITextEffectsOrdering,
    NSISVariableDelegate <NSObject>,
    _UILayoutItem <NSLayoutItem>,
    NSISEngineDelegate <NSObject>,
    _UITraitEnvironmentInternal <UITraitEnvironment>,
    _UIFocusEnvironmentInternal <UIFocusEnvironment>,
	...
)
```

### 查看继承层级关系

```Objective-C
(lldb) po [UIButton lx_inheritanceTree]
• NSObject
 • UIResponder
  • UIView
   • UIControl
    • UIButton
```

### 查看协议内容

```Objective-C
NSLog(@"%@", LXProtocolDescription(@protocol(NSObject)));

{
    @optional = (
        - (id)debugDescription,
    );
    @properties = (
        @property (atomic, assign, readonly) unsigned long hash,
        @property (atomic, assign, readonly) Class superclass,
        @property (atomic, copy, readonly) NSString *description,
        @property (atomic, copy, readonly) NSString *debugDescription,
    );
    @required = (
        - (NSZone *)zone,
        - (id)retain,
        - (oneway void)release,
        - (id)autorelease,
        - (unsigned long)retainCount,
        - (id)description,
        - (BOOL)isKindOfClass:(Class)arg0,
        - (unsigned long)hash,
        - (BOOL)isEqual:(id)arg0,
        - (BOOL)respondsToSelector:(SEL)arg0,
        - (id)self,
        - (id)performSelector:(SEL)arg0,
        - (id)performSelector:(SEL)arg0 withObject:(id)arg1,
        - (BOOL)conformsToProtocol:(id)arg0,
        - (id)performSelector:(SEL)arg0 withObject:(id)arg1 withObject:(id)arg2,
        - (BOOL)isProxy,
        - (BOOL)isMemberOfClass:(Class)arg0,
        - (Class)superclass,
        - (Class)class,
    );
}
```

### 查看系统中所有类

```Objective-C
(lldb) po LXClassNameList()
(
    ABAnyValuePredicate,
    ABCCallbackInvoker,
    ABDataCollection,
    ...
    UIBarItemAccessibility,
    UIBezierPath,
    UIBlurEffect,
    ...
    ___UIViewServiceInterfaceConnectionRequestAccessibility_super,
)
```

## 在 Swift 中使用

为了保持格式化效果，需要桥接为 `NSArray` 或者 `NSDictionary`，并使用 `NSLog` 函数打印：

```swift
NSLog("%@", NSObject.lx_classMethodDescriptionList() as NSArray)
```

为了方便，专门提供了一些打印方法：

```swift
// 在 LLDB 调试器中
(lldb) p NSObject.lx_printClassMethodDescriptionList()

// 在代码中
NSObject.lx_printClassMethodDescriptionList()
```

## 注意事项

为了获得更好的对齐效果，通过分类覆盖了 `NSArray` 和 `NSDictionary` 的 `debugDescription` 和
`descriptionWithLocale:` 方法，可以通过 `NSObject+DLIntrospection.h` 文件中的宏开启或关闭：

```objective-c
#define ENABLE_LOG_ALIGNMENT 1
```

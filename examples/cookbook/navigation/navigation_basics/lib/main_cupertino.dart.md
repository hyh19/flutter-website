# Flutter Cupertino 导航基础示例代码解析

## 概述

这是一个使用 Flutter Cupertino（iOS 风格）组件实现的导航基础示例。与 Material Design 版本不同，这个示例展示了如何在 iOS 风格的界面中进行页面导航，使用 `Navigator.push()` 跳转到新页面，以及使用 `Navigator.pop()` 返回上一页面。

## 代码结构

代码包含三个主要部分：

1. **应用入口**：`main()` 函数，使用 `CupertinoApp`
2. **第一个路由页面**：`FirstRoute` 类，使用 Cupertino 组件
3. **第二个路由页面**：`SecondRoute` 类，使用 Cupertino 组件

## Cupertino vs Material

这个示例与 Material Design 版本的主要区别在于：

- **应用入口**：使用 `CupertinoApp` 而非 `MaterialApp`
- **页面布局**：使用 `CupertinoPageScaffold` 而非 `Scaffold`
- **导航栏**：使用 `CupertinoNavigationBar` 而非 `AppBar`
- **按钮**：使用 `CupertinoButton` 而非 `ElevatedButton`
- **路由**：使用 `CupertinoPageRoute` 而非 `MaterialPageRoute`

这些组件提供了 iOS 风格的视觉设计和交互体验。

## 详细解析

### 应用入口

```dart 3:5:examples/cookbook/navigation/navigation_basics/lib/main_cupertino.dart
void main() {
  runApp(const CupertinoApp(title: 'Navigation Basics', home: FirstRoute()));
}
```

`main()` 函数是 Flutter 应用的入口点。这里创建了一个 `CupertinoApp` 实例，并将 `FirstRoute` 设置为应用的首页。

**关键点**：

- `CupertinoApp` 是 Flutter iOS 风格应用的基础组件，提供 iOS 风格的主题和配置
- `title` 属性用于设置应用的标题
- `home` 属性指定了应用启动时显示的初始页面
- 与 `MaterialApp` 不同，`CupertinoApp` 提供了 iOS 风格的默认主题和字体

### 第一个路由页面（FirstRoute）

```dart 7:29:examples/cookbook/navigation/navigation_basics/lib/main_cupertino.dart
class FirstRoute extends StatelessWidget {
  const FirstRoute({super.key});

  @override
  Widget build(BuildContext context) {
    return CupertinoPageScaffold(
      navigationBar: const CupertinoNavigationBar(middle: Text('First Route')),
      child: Center(
        child: CupertinoButton(
          child: const Text('Open route'),
          onPressed: () {
            Navigator.push(
              context,
              CupertinoPageRoute<void>(
                builder: (context) => const SecondRoute(),
              ),
            );
          },
        ),
      ),
    );
  }
}
```

`FirstRoute` 是一个无状态的 Widget，它构建了应用的第一个页面，使用 iOS 风格的组件。

**组件说明**：

1. **CupertinoPageScaffold**：iOS 风格的基本页面布局结构，类似于 Material 的 `Scaffold`，但提供 iOS 风格的视觉样式
2. **CupertinoNavigationBar**：iOS 风格的导航栏，显示在页面顶部
   - `middle` 属性：设置导航栏中间显示的标题文本
   - iOS 风格的导航栏通常有半透明效果和模糊背景
3. **Center**：将子组件居中显示
4. **CupertinoButton**：iOS 风格的按钮，具有 iOS 原生的按钮样式和交互效果

**导航逻辑**：

当用户点击按钮时，`onPressed` 回调函数会执行以下操作：

```dart 18:23:examples/cookbook/navigation/navigation_basics/lib/main_cupertino.dart
Navigator.push(
  context,
  CupertinoPageRoute<void>(
    builder: (context) => const SecondRoute(),
  ),
);
```

- `Navigator.push()`：将新路由推入导航栈，实现页面跳转
- `context`：当前 Widget 的上下文，用于访问导航器
- `CupertinoPageRoute`：iOS 风格的页面路由，提供从右侧滑入的页面切换动画（iOS 标准动画）
- `builder`：一个函数，返回要显示的新页面 Widget（这里是 `SecondRoute`）

### 第二个路由页面（SecondRoute）

```dart 31:48:examples/cookbook/navigation/navigation_basics/lib/main_cupertino.dart
class SecondRoute extends StatelessWidget {
  const SecondRoute({super.key});

  @override
  Widget build(BuildContext context) {
    return CupertinoPageScaffold(
      navigationBar: const CupertinoNavigationBar(middle: Text('Second Route')),
      child: Center(
        child: CupertinoButton(
          onPressed: () {
            Navigator.pop(context);
          },
          child: const Text('Go back!'),
        ),
      ),
    );
  }
}
```

`SecondRoute` 是第二个页面，结构与 `FirstRoute` 类似，但功能不同。

**关键区别**：

1. **页面标题**：导航栏显示 "Second Route"
2. **按钮文本**：显示 "Go back!"
3. **导航操作**：使用 `Navigator.pop()` 返回上一页面

**返回逻辑**：

```dart 40:42:examples/cookbook/navigation/navigation_basics/lib/main_cupertino.dart
onPressed: () {
  Navigator.pop(context);
},
```

- `Navigator.pop(context)`：从导航栈中弹出当前路由，返回到上一个页面
- 这会销毁当前页面并显示之前的页面（`FirstRoute`）
- iOS 风格的返回动画是从右侧滑出

## 导航流程

1. **应用启动**：显示 `FirstRoute` 页面，使用 iOS 风格的界面
2. **用户点击按钮**：点击 "Open route" 按钮
3. **页面跳转**：`Navigator.push()` 使用 `CupertinoPageRoute` 将 `SecondRoute` 推入导航栈，新页面从右侧滑入（iOS 标准动画）
4. **用户返回**：在第二个页面点击 "Go back!" 按钮
5. **返回**：`Navigator.pop()` 从导航栈中移除当前页面，页面从右侧滑出，返回到 `FirstRoute`

## 核心概念

### Cupertino 设计语言

Cupertino 是 Flutter 提供的 iOS 风格组件库，包括：

- **视觉风格**：遵循 iOS 设计规范，包括颜色、字体、间距等
- **交互模式**：提供 iOS 原生的交互体验和动画效果
- **组件命名**：所有组件以 `Cupertino` 前缀命名

### CupertinoPageRoute

`CupertinoPageRoute` 提供了：

- **iOS 风格动画**：从右侧滑入的页面切换动画（iOS 标准导航动画）
- **路由管理**：管理页面的生命周期
- **类型安全**：通过泛型 `<void>` 指定路由不传递返回值
- **与 MaterialPageRoute 的区别**：动画方向不同（iOS 从右到左，Material 从下到上）

### CupertinoPageScaffold

`CupertinoPageScaffold` 是 iOS 风格的页面布局容器：

- 提供导航栏区域（`navigationBar`）
- 提供内容区域（`child`）
- 自动处理安全区域（Safe Area）适配
- 支持 iOS 风格的页面结构

### CupertinoNavigationBar

iOS 风格的导航栏特点：

- **半透明效果**：默认具有模糊背景效果
- **标题位置**：使用 `middle` 属性设置中间标题
- **自动返回按钮**：当导航栈中有多个页面时，会自动显示返回按钮
- **iOS 原生样式**：完全符合 iOS 设计规范

### StatelessWidget

两个路由页面都继承自 `StatelessWidget`，表示它们是静态的、不可变的 Widget。如果页面需要维护状态，应该使用 `StatefulWidget`。

## 使用场景

这个示例适用于：

- 开发 iOS 风格的应用
- 需要与 iOS 原生应用保持一致的视觉和交互体验
- 学习 Flutter Cupertino 组件库的使用
- 理解 iOS 风格的导航实现
- 作为跨平台应用中 iOS 端的基础模板

## Material vs Cupertino 选择建议

**选择 Material Design**：

- 开发 Android 优先的应用
- 需要 Material Design 的丰富组件和主题
- 跨平台应用但希望统一使用 Material 风格

**选择 Cupertino**：

- 开发 iOS 优先的应用
- 需要与 iOS 原生应用保持一致
- 跨平台应用中 iOS 端的实现
- 希望提供平台特定的用户体验

## 扩展建议

基于这个基础示例，可以进一步扩展：

- 在页面间传递参数
- 使用命名路由（Named Routes）
- 自定义 iOS 风格的页面过渡动画
- 处理 iOS 系统返回手势（右滑返回）
- 添加页面间的数据回传
- 使用 `CupertinoTabScaffold` 实现标签栏导航
- 集成 iOS 风格的对话框和操作表

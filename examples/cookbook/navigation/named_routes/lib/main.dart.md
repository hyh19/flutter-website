# Flutter 命名路由示例代码解析

本文档详细解析了 Flutter 中使用命名路由（Named Routes）进行页面导航的示例代码。

## 代码概述

这是一个完整的 Flutter 应用示例，演示了如何使用命名路由在多个页面之间进行导航。应用包含两个页面：`FirstScreen`（第一个屏幕）和 `SecondScreen`（第二个屏幕），用户可以通过按钮在它们之间切换。

## 主要组件

### 1. 应用入口（main 函数）

```dart 3:20:examples/cookbook/navigation/named_routes/lib/main.dart
void main() {
  runApp(
    // #docregion MaterialApp
    MaterialApp(
      title: 'Named Routes Demo',
      // Start the app with the "/" named route. In this case, the app starts
      // on the FirstScreen widget.
      initialRoute: '/',
      routes: {
        // When navigating to the "/" route, build the FirstScreen widget.
        '/': (context) => const FirstScreen(),
        // When navigating to the "/second" route, build the SecondScreen widget.
        '/second': (context) => const SecondScreen(),
      },
    ),
    // #enddocregion MaterialApp
  );
}
```

**功能说明**：

- `main()` 函数是应用的入口点，调用 `runApp()` 启动 Flutter 应用
- `MaterialApp` 是 Material Design 风格的应用根组件，负责管理应用的整体结构和路由
- `title` 属性设置应用的标题
- `initialRoute` 指定应用启动时显示的路由，这里设置为 `'/'`，表示应用启动时显示根路由对应的页面
- `routes` 是一个 `Map<String, WidgetBuilder>`，定义了所有可用的命名路由：
  - `'/'` 路由对应 `FirstScreen` 组件
  - `'/second'` 路由对应 `SecondScreen` 组件

**命名路由的优势**：

- 路由名称集中管理，便于维护
- 避免在代码中硬编码页面类名
- 支持通过 URL 进行深度链接（deep linking）
- 代码更加清晰和可读

### 2. 第一个屏幕（FirstScreen）

```dart 22:43:examples/cookbook/navigation/named_routes/lib/main.dart
class FirstScreen extends StatelessWidget {
  const FirstScreen({super.key});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text('First Screen')),
      body: Center(
        child: ElevatedButton(
          // #docregion PushNamed
          // Within the `FirstScreen` widget
          onPressed: () {
            // Navigate to the second screen using a named route.
            Navigator.pushNamed(context, '/second');
          },
          // #enddocregion PushNamed
          child: const Text('Launch screen'),
        ),
      ),
    );
  }
}
```

**功能说明**：

- `FirstScreen` 是一个无状态组件（`StatelessWidget`），表示应用的第一个页面
- `Scaffold` 提供了基本的 Material Design 页面结构
- `AppBar` 显示页面标题 "First Screen"
- `body` 包含一个居中的按钮（`ElevatedButton`）
- 按钮的 `onPressed` 回调中调用 `Navigator.pushNamed(context, '/second')` 来导航到第二个屏幕

**导航机制**：

- `Navigator.pushNamed()` 方法用于通过命名路由进行导航
- 第一个参数 `context` 是构建上下文，用于定位导航器
- 第二个参数 `'/second'` 是目标路由的名称，必须在 `MaterialApp` 的 `routes` 中定义
- 调用此方法会将新页面推入导航栈，用户可以通过返回按钮或调用 `Navigator.pop()` 返回

### 3. 第二个屏幕（SecondScreen）

```dart 45:67:examples/cookbook/navigation/named_routes/lib/main.dart
class SecondScreen extends StatelessWidget {
  const SecondScreen({super.key});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text('Second Screen')),
      body: Center(
        child: ElevatedButton(
          // #docregion Pop
          // Within the SecondScreen widget
          onPressed: () {
            // Navigate back to the first screen by popping the current route
            // off the stack.
            Navigator.pop(context);
          },
          // #enddocregion Pop
          child: const Text('Go back!'),
        ),
      ),
    );
  }
}
```

**功能说明**：

- `SecondScreen` 是第二个页面组件，结构与 `FirstScreen` 类似
- 页面标题显示为 "Second Screen"
- 按钮文本为 "Go back!"
- 按钮的 `onPressed` 回调中调用 `Navigator.pop(context)` 返回到上一个页面

**返回导航**：

- `Navigator.pop()` 方法用于从导航栈中移除当前页面并返回到上一个页面
- 这是 Flutter 中返回导航的标准方式
- 调用此方法会触发页面动画，将当前页面从栈中弹出

## 导航流程

1. **应用启动**：应用启动时，`MaterialApp` 根据 `initialRoute: '/'` 显示 `FirstScreen`
2. **导航到第二个屏幕**：用户在 `FirstScreen` 点击 "Launch screen" 按钮，触发 `Navigator.pushNamed(context, '/second')`，导航到 `SecondScreen`
3. **返回第一个屏幕**：用户在 `SecondScreen` 点击 "Go back!" 按钮，触发 `Navigator.pop(context)`，返回到 `FirstScreen`

## 关键概念

### 导航栈（Navigation Stack）

Flutter 使用栈（Stack）数据结构管理页面导航：

- 每次调用 `Navigator.pushNamed()` 或 `Navigator.push()` 时，新页面被推入栈顶
- 调用 `Navigator.pop()` 时，栈顶页面被移除，显示栈中的上一个页面
- 这种机制确保了用户可以按顺序返回之前访问的页面

### 命名路由 vs 匿名路由

**命名路由**（本示例使用的方式）：

- 在 `MaterialApp` 的 `routes` 中预先定义所有路由
- 使用字符串名称进行导航，如 `Navigator.pushNamed(context, '/second')`
- 适合路由数量较少、结构固定的应用

**匿名路由**（另一种方式）：

- 直接传递 `Widget` 进行导航，如 `Navigator.push(context, MaterialPageRoute(builder: (context) => SecondScreen()))`
- 更灵活，适合动态页面或路由数量较多的应用

## 使用场景

命名路由适合以下场景：

- 应用有固定的页面结构
- 需要支持深度链接（deep linking）
- 希望集中管理所有路由
- 需要在多个地方导航到同一个页面

## 扩展建议

如果需要传递参数给目标页面，可以使用以下方式：

1. **通过路由参数**：使用 `Navigator.pushNamed(context, '/second', arguments: {'key': 'value'})`
2. **在目标页面接收**：使用 `ModalRoute.of(context)!.settings.arguments` 获取参数
3. **或者使用 `onGenerateRoute`**：在 `MaterialApp` 中使用 `onGenerateRoute` 回调来动态生成路由，支持更复杂的参数传递

## 总结

这个示例展示了 Flutter 中命名路由的基本用法，包括：

- 在 `MaterialApp` 中定义路由映射
- 使用 `Navigator.pushNamed()` 进行导航
- 使用 `Navigator.pop()` 返回上一页
- 理解导航栈的工作原理

这是 Flutter 导航系统的基础，掌握这些概念后可以进一步学习更高级的导航模式，如嵌套导航、路由守卫等。

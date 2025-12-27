# Flutter 导航基础示例代码解析

## 概述

这是一个 Flutter 导航基础示例，演示了如何在两个页面之间进行基本的导航操作。代码展示了使用 `Navigator.push()` 跳转到新页面，以及使用 `Navigator.pop()` 返回上一页面的完整流程。

## 代码结构

代码包含三个主要部分：

1. **应用入口**：`main()` 函数
2. **第一个路由页面**：`FirstRoute` 类
3. **第二个路由页面**：`SecondRoute` 类

## 详细解析

### 应用入口

```dart 3:5:examples/cookbook/navigation/navigation_basics/lib/main.dart
void main() {
  runApp(const MaterialApp(title: 'Navigation Basics', home: FirstRoute()));
}
```

`main()` 函数是 Flutter 应用的入口点。这里创建了一个 `MaterialApp` 实例，并将 `FirstRoute` 设置为应用的首页（`home` 属性）。

**关键点**：

- `MaterialApp` 是 Flutter Material Design 应用的基础组件
- `title` 属性用于设置应用的标题（在 Android 任务管理器中显示）
- `home` 属性指定了应用启动时显示的初始页面

### 第一个路由页面（FirstRoute）

```dart 7:29:examples/cookbook/navigation/navigation_basics/lib/main.dart
class FirstRoute extends StatelessWidget {
  const FirstRoute({super.key});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text('First Route')),
      body: Center(
        child: ElevatedButton(
          child: const Text('Open route'),
          onPressed: () {
            Navigator.push(
              context,
              MaterialPageRoute<void>(
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

`FirstRoute` 是一个无状态的 Widget，它构建了应用的第一个页面。

**组件说明**：

1. **Scaffold**：Material Design 的基本布局结构，提供了应用栏、主体内容等标准区域
2. **AppBar**：应用栏，显示页面标题 "First Route"
3. **Center**：将子组件居中显示
4. **ElevatedButton**：一个带阴影的按钮，显示 "Open route" 文本

**导航逻辑**：

当用户点击按钮时，`onPressed` 回调函数会执行以下操作：

```dart 18:23:examples/cookbook/navigation/navigation_basics/lib/main.dart
Navigator.push(
  context,
  MaterialPageRoute<void>(
    builder: (context) => const SecondRoute(),
  ),
);
```

- `Navigator.push()`：将新路由推入导航栈，实现页面跳转
- `context`：当前 Widget 的上下文，用于访问导航器
- `MaterialPageRoute`：Material Design 风格的页面路由，定义了页面切换的动画和过渡效果
- `builder`：一个函数，返回要显示的新页面 Widget（这里是 `SecondRoute`）

### 第二个路由页面（SecondRoute）

```dart 31:48:examples/cookbook/navigation/navigation_basics/lib/main.dart
class SecondRoute extends StatelessWidget {
  const SecondRoute({super.key});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text('Second Route')),
      body: Center(
        child: ElevatedButton(
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

1. **页面标题**：显示 "Second Route"
2. **按钮文本**：显示 "Go back!"
3. **导航操作**：使用 `Navigator.pop()` 而不是 `Navigator.push()`

**返回逻辑**：

```dart 40:42:examples/cookbook/navigation/navigation_basics/lib/main.dart
onPressed: () {
  Navigator.pop(context);
},
```

- `Navigator.pop(context)`：从导航栈中弹出当前路由，返回到上一个页面
- 这会销毁当前页面并显示之前的页面（`FirstRoute`）

## 导航流程

1. **应用启动**：显示 `FirstRoute` 页面
2. **用户点击按钮**：点击 "Open route" 按钮
3. **页面跳转**：`Navigator.push()` 将 `SecondRoute` 推入导航栈，显示第二个页面
4. **用户返回**：在第二个页面点击 "Go back!" 按钮
5. **返回**：`Navigator.pop()` 从导航栈中移除当前页面，返回到 `FirstRoute`

## 核心概念

### Navigator 导航栈

Flutter 使用栈（Stack）数据结构来管理页面导航：

- **push**：将新页面推入栈顶（前进）
- **pop**：从栈顶移除当前页面（后退）

### MaterialPageRoute

`MaterialPageRoute` 提供了：

- **页面切换动画**：默认的 Material Design 滑动动画
- **路由管理**：管理页面的生命周期
- **类型安全**：通过泛型 `<void>` 指定路由不传递返回值

### StatelessWidget

两个路由页面都继承自 `StatelessWidget`，表示它们是静态的、不可变的 Widget。如果页面需要维护状态，应该使用 `StatefulWidget`。

## 使用场景

这个示例适用于：

- 学习 Flutter 导航的基础概念
- 实现简单的页面跳转功能
- 理解 Navigator API 的基本用法
- 作为更复杂导航功能的基础模板

## 扩展建议

基于这个基础示例，可以进一步扩展：

- 在页面间传递参数
- 使用命名路由（Named Routes）
- 实现自定义页面过渡动画
- 处理返回按钮（Android 系统返回键）
- 添加页面间的数据回传

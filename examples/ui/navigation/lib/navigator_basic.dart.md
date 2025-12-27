# navigator_basic.dart 代码解析

## 概述

这个文件展示了 Flutter 中最基础的导航方式——使用 `Navigator.push()` 和 `MaterialPageRoute` 进行页面跳转。这是 Flutter 导航的基础，所有其他导航方式（命名路由、GoRouter 等）都是基于这个机制构建的。

## 核心概念

### Navigator 基础

- **Navigator**：Flutter 的导航管理器，维护一个路由栈（Route Stack）
- **路由栈**：类似栈数据结构，后进先出（LIFO）
- **push**：将新路由推入栈顶，显示新页面
- **pop**：从栈顶移除当前路由，返回到上一个页面

### 基础导航的优势

- **简单直接**：代码直观，易于理解
- **灵活性强**：可以完全控制路由的创建和配置
- **无需配置**：不需要预先定义路由表
- **适合简单应用**：对于路由数量少的应用，代码量最少

## 代码结构分析

### 应用入口

```dart 3:5:examples/ui/navigation/lib/navigator_basic.dart
void main() {
  runApp(const MaterialApp(title: 'Navigation basics', home: FirstScreen()));
}
```

这里使用最简单的 `MaterialApp` 配置：

- **`title`**：应用的标题
- **`home`**：直接指定首页 Widget，不需要路由配置

### 第一个屏幕

```dart 7:29:examples/ui/navigation/lib/navigator_basic.dart
class FirstScreen extends StatelessWidget {
  const FirstScreen({super.key});
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text('First Screen')),
      body: Center(
        child: ElevatedButton(
          // #docregion push-route
          child: const Text('Open second screen'),
          onPressed: () {
            Navigator.of(context).push(
              MaterialPageRoute<void>(
                builder: (context) => const SecondScreen(),
              ),
            );
          },
          // #enddocregion push-route
        ),
      ),
    );
  }
}
```

关键导航代码解析：

```dart
Navigator.of(context).push(
  MaterialPageRoute<void>(
    builder: (context) => const SecondScreen(),
  ),
);
```

**逐步解析**：

1. **`Navigator.of(context)`**：
   - 从 `context` 中获取最近的 `Navigator` 实例
   - 每个 `MaterialApp` 都会创建一个 `Navigator`

2. **`.push()`**：
   - 将新路由推入导航栈
   - 参数是一个 `Route` 对象

3. **`MaterialPageRoute<void>`**：
   - `MaterialPageRoute`：Material Design 风格的路由，提供页面转场动画
   - `<void>`：泛型参数，表示这个路由不返回任何值（也可以指定返回类型）

4. **`builder`**：
   - 路由构建器函数
   - 接收 `context`，返回要显示的 Widget

### 第二个屏幕

```dart 31:47:examples/ui/navigation/lib/navigator_basic.dart
class SecondScreen extends StatelessWidget {
  const SecondScreen({super.key});
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text('Second Screen')),
      body: Center(
        child: ElevatedButton(
          child: const Text('Pop current screen'),
          onPressed: () {
            Navigator.pop(context);
          },
        ),
      ),
    );
  }
}
```

返回操作：

- **`Navigator.pop(context)`**：从导航栈中移除当前路由
  - 如果栈中有多个路由，返回到上一个页面
  - 如果栈中只有一个路由，在 Android 上会关闭应用，iOS 上不会

## 导航方法详解

### Navigator.push()

```dart
Future<T?> Navigator.push<T>(
  BuildContext context,
  Route<T> route,
)
```

**参数说明**：

- `context`：BuildContext 实例
- `route`：要推入栈的路由对象

**返回值**：`Future<T?>`，当目标页面通过 `pop()` 返回时，可以传递返回值

**使用示例**：

```dart
// 不关心返回值
Navigator.of(context).push(
  MaterialPageRoute(builder: (context) => SecondScreen()),
);

// 等待返回值
final result = await Navigator.of(context).push(
  MaterialPageRoute<String>(
    builder: (context) => SecondScreen(),
  ),
);
print('返回的值: $result');
```

### Navigator.pop()

```dart
void Navigator.pop<T>(
  BuildContext context,
  [T? result],
)
```

**参数说明**：

- `context`：BuildContext 实例
- `result`：可选，返回给上一个页面的值

**使用示例**：

```dart
// 简单返回
Navigator.pop(context);

// 返回数据
Navigator.pop(context, '用户选择的数据');
```

## 路由类型

### MaterialPageRoute

Material Design 风格的路由，提供标准的页面转场动画：

```dart
MaterialPageRoute(
  builder: (context) => SecondScreen(),
  settings: RouteSettings(name: '/second'),  // 可选：路由设置
  fullscreenDialog: false,  // 可选：是否全屏对话框
)
```

### CupertinoPageRoute

iOS 风格的路由，提供 iOS 风格的转场动画：

```dart
import 'package:flutter/cupertino.dart';

CupertinoPageRoute(
  builder: (context) => SecondScreen(),
)
```

### PageRouteBuilder

自定义转场动画的路由：

```dart
PageRouteBuilder(
  pageBuilder: (context, animation, secondaryAnimation) => SecondScreen(),
  transitionsBuilder: (context, animation, secondaryAnimation, child) {
    return FadeTransition(opacity: animation, child: child);
  },
)
```

## 传递参数

### 通过构造函数传递

最直接的方式是通过 Widget 构造函数传递参数：

```dart
// 导航时传递参数
Navigator.of(context).push(
  MaterialPageRoute(
    builder: (context) => SecondScreen(
      userId: 123,
      userName: 'John',
    ),
  ),
);

// 目标页面接收参数
class SecondScreen extends StatelessWidget {
  final int userId;
  final String userName;
  
  const SecondScreen({
    super.key,
    required this.userId,
    required this.userName,
  });
  
  // ...
}
```

### 通过返回值获取数据

```dart
// 导航并等待返回值
final result = await Navigator.of(context).push(
  MaterialPageRoute<String>(
    builder: (context) => SecondScreen(),
  ),
);

if (result != null) {
  print('用户选择: $result');
}

// 在 SecondScreen 中返回数据
Navigator.pop(context, '选中的数据');
```

## 导航栈操作

### 检查是否可以返回

```dart
bool canPop = Navigator.of(context).canPop();
```

### 返回到指定路由

```dart
Navigator.of(context).popUntil((route) => route.isFirst);
// 或者
Navigator.of(context).popUntil((route) => route.settings.name == '/home');
```

### 替换当前路由

```dart
Navigator.of(context).pushReplacement(
  MaterialPageRoute(builder: (context) => NewScreen()),
);
```

### 移除当前路由并推入新路由

```dart
Navigator.of(context).pushAndRemoveUntil(
  MaterialPageRoute(builder: (context) => NewScreen()),
  (route) => false,  // 移除所有之前的路由
);
```

## 转场动画

### 自定义转场动画

```dart
PageRouteBuilder(
  pageBuilder: (context, animation, secondaryAnimation) => SecondScreen(),
  transitionsBuilder: (context, animation, secondaryAnimation, child) {
    const begin = Offset(1.0, 0.0);
    const end = Offset.zero;
    const curve = Curves.ease;

    var tween = Tween(begin: begin, end: end).chain(
      CurveTween(curve: curve),
    );

    return SlideTransition(
      position: animation.drive(tween),
      child: child,
    );
  },
)
```

## 使用场景

### 适合使用基础 Navigator 的情况

1. **简单应用**：路由数量少（少于 5 个）
2. **快速原型**：需要快速实现导航功能
3. **完全控制**：需要完全控制路由的创建和转场动画
4. **学习目的**：理解 Flutter 导航机制的基础

### 不适合的情况

1. **复杂应用**：路由数量多，需要集中管理
2. **需要深度链接**：需要 Web URL 支持
3. **团队协作**：需要统一的导航规范
4. **维护性要求高**：路由分散在各处，难以维护

## 与其他导航方式的对比

### 基础 Navigator vs 命名路由 vs GoRouter

| 特性 | 基础 Navigator | 命名路由 | GoRouter |
| --- | --- | --- | --- |
| 代码量 | 最少 | 中等 | 较多 |
| 路由管理 | 分散 | 集中 | 集中且声明式 |
| 参数传递 | 构造函数 | arguments | 类型安全 |
| 深度链接 | 不支持 | 部分支持 | 完全支持 |
| 学习曲线 | 低 | 中 | 中高 |
| 适用场景 | 简单应用 | 中等应用 | 复杂应用 |

## 最佳实践

### 1. 使用 const 构造函数

```dart
// 推荐
MaterialPageRoute(builder: (context) => const SecondScreen())

// 不推荐
MaterialPageRoute(builder: (context) => SecondScreen())
```

### 2. 处理异步返回值

```dart
final result = await Navigator.of(context).push(
  MaterialPageRoute(builder: (context) => SecondScreen()),
);

// 检查返回值
if (result != null) {
  // 处理返回的数据
}
```

### 3. 使用命名路由简化代码

当路由数量增多时，考虑迁移到命名路由：

```dart
// 基础方式 - 代码重复
Navigator.of(context).push(
  MaterialPageRoute(builder: (context) => SecondScreen()),
);

// 命名路由 - 更简洁
Navigator.pushNamed(context, '/second');
```

## 常见问题

### 问题 1：pop() 后页面没有更新

**原因**：上一个页面没有监听返回值

**解决方案**：使用 `await` 等待返回值并更新状态

```dart
final result = await Navigator.of(context).push(
  MaterialPageRoute(builder: (context) => SecondScreen()),
);
setState(() {
  // 根据返回值更新状态
});
```

### 问题 2：Android 返回键行为不一致

**原因**：`pop()` 在栈中只有一个路由时的行为在不同平台不同

**解决方案**：使用 `WillPopScope` 或 `PopScope` 控制返回行为

```dart
PopScope(
  canPop: false,
  onPopInvoked: (didPop) {
    if (!didPop) {
      // 自定义返回逻辑
    }
  },
  child: Scaffold(...),
)
```

## 总结

基础 Navigator 是 Flutter 导航的基石，通过 `Navigator.push()` 和 `MaterialPageRoute` 可以简单直接地实现页面跳转。它适合简单的应用场景，代码直观易懂。但随着应用复杂度增加，建议考虑使用命名路由或 GoRouter 来更好地组织和管理路由代码。

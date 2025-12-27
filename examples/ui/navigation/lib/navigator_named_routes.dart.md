# navigator_named_routes.dart 代码解析

## 概述

这个文件展示了如何在 Flutter 应用中使用**命名路由（Named Routes）**进行导航。命名路由是 Flutter 内置的导航方式，通过字符串路径来管理路由，适合中等复杂度的应用。

## 核心概念

### 命名路由的优势

- **集中管理**：所有路由定义在一个地方，易于维护
- **字符串路径**：使用字符串标识路由，直观易懂
- **无需导入页面**：在路由表中定义后，可以在任何地方通过路径导航
- **内置支持**：Flutter 框架原生支持，无需额外依赖

## 代码结构分析

### 应用入口与路由配置

```dart 3:14:examples/ui/navigation/lib/navigator_named_routes.dart
void main() {
  runApp(
    MaterialApp(
      title: 'Navigation with named routes',
      initialRoute: '/',
      routes: {
        '/': (context) => const FirstScreen(),
        '/second': (context) => const SecondScreen(),
      },
    ),
  );
}
```

关键配置说明：

- **`MaterialApp`**：使用标准的 `MaterialApp` 构造函数
- **`initialRoute`**：指定应用的初始路由路径，默认为 `/`
- **`routes`**：路由映射表，类型为 `Map<String, WidgetBuilder>`
  - 键（Key）：路由路径字符串，通常以 `/` 开头
  - 值（Value）：`WidgetBuilder` 函数，返回对应路径的 Widget

### 第一个屏幕

```dart 16:34:examples/ui/navigation/lib/navigator_named_routes.dart
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
            Navigator.pushNamed(context, '/second');
          },
          // #enddocregion push-route
        ),
      ),
    );
  }
}
```

导航方法：

- **`Navigator.pushNamed(context, '/second')`**：通过命名路由进行导航
  - `context`：BuildContext，用于获取 Navigator
  - `'/second'`：目标路由的路径字符串，必须在 `routes` 中定义
  - 行为：在导航栈中添加新路由，可以通过返回按钮或 `pop()` 返回

### 第二个屏幕

```dart 36:52:examples/ui/navigation/lib/navigator_named_routes.dart
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

返回方法：

- **`Navigator.pop(context)`**：从导航栈中移除当前路由
  - 行为：返回到上一个页面
  - 如果栈中只有一个路由，`pop` 不会关闭应用（在 Android 上会关闭应用，iOS 上不会）

## 导航方法详解

### Navigator.pushNamed()

```dart
Navigator.pushNamed(
  context,
  '/route-name',
  arguments: optionalArguments,  // 可选参数
);
```

**参数说明**：

- `context`：BuildContext 实例
- `routeName`：路由路径字符串
- `arguments`：可选，传递给目标页面的参数

**返回值**：`Future<T?>`，当目标页面通过 `pop()` 返回时，可以传递返回值

### Navigator.pop()

```dart
Navigator.pop(
  context,
  result,  // 可选返回值
);
```

**参数说明**：

- `context`：BuildContext 实例
- `result`：可选，返回给上一个页面的值

## 传递参数

### 传递参数到目标页面

```dart
// 导航时传递参数
Navigator.pushNamed(
  context,
  '/second',
  arguments: {'userId': 123, 'userName': 'John'},
);

// 在目标页面接收参数
class SecondScreen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    final args = ModalRoute.of(context)!.settings.arguments as Map<String, dynamic>;
    final userId = args['userId'];
    final userName = args['userName'];
    // ...
  }
}
```

### 接收返回值

```dart
// 导航并等待返回值
final result = await Navigator.pushNamed(context, '/second');

// 返回时传递值
Navigator.pop(context, '返回的数据');
```

## 路由生成器（onGenerateRoute）

对于更复杂的路由场景，可以使用 `onGenerateRoute`：

```dart
MaterialApp(
  onGenerateRoute: (settings) {
    switch (settings.name) {
      case '/':
        return MaterialPageRoute(builder: (_) => FirstScreen());
      case '/second':
        return MaterialPageRoute(builder: (_) => SecondScreen());
      case '/user':
        final args = settings.arguments as Map<String, dynamic>;
        return MaterialPageRoute(
          builder: (_) => UserScreen(userId: args['id']),
        );
      default:
        return MaterialPageRoute(builder: (_) => NotFoundScreen());
    }
  },
)
```

## 路由守卫

虽然命名路由本身不直接支持路由守卫，但可以通过 `onGenerateRoute` 实现：

```dart
MaterialApp(
  onGenerateRoute: (settings) {
    // 检查登录状态
    if (settings.name != '/login' && !isLoggedIn) {
      return MaterialPageRoute(builder: (_) => LoginScreen());
    }
    
    // 正常路由逻辑
    switch (settings.name) {
      case '/':
        return MaterialPageRoute(builder: (_) => FirstScreen());
      // ...
    }
  },
)
```

## 与基础 Navigator 的对比

### 命名路由 vs 基础 Navigator

| 特性 | 命名路由 | 基础 Navigator |
| --- | --- | --- |
| 路由定义 | 集中定义在 `routes` 中 | 分散在各处 |
| 导航方式 | `pushNamed('/path')` | `push(MaterialPageRoute(...))` |
| 参数传递 | 通过 `arguments` | 通过构造函数 |
| 代码复用 | 高，路由可复用 | 低，每次需要创建 Route |
| 维护性 | 高，集中管理 | 低，分散管理 |

## 使用场景

### 适合使用命名路由的情况

1. **中等复杂度的应用**：路由数量适中（5-20 个）
2. **需要集中管理路由**：希望所有路由定义在一个地方
3. **简单的参数传递**：只需要传递基本类型参数
4. **不需要深度链接**：不需要 Web URL 支持

### 不适合的情况

1. **复杂路由结构**：嵌套路由、路由守卫等复杂需求
2. **类型安全要求高**：需要类型安全的路由参数
3. **深度链接支持**：需要 Web URL 和深度链接
4. **动态路由生成**：需要根据数据动态生成路由

## 常见问题

### 路由未定义错误

如果使用未在 `routes` 中定义的路由，会抛出异常：

```dart
// 错误：'/unknown' 未在 routes 中定义
Navigator.pushNamed(context, '/unknown');
```

**解决方案**：使用 `onGenerateRoute` 或 `onUnknownRoute` 处理未定义的路由。

### 路由参数类型安全

命名路由的参数传递不是类型安全的，需要手动进行类型转换：

```dart
// 需要手动类型转换
final args = ModalRoute.of(context)!.settings.arguments as Map<String, dynamic>;
```

## 总结

命名路由是 Flutter 内置的导航方式，通过集中定义路由映射表，可以在应用的任何地方通过路径字符串进行导航。它适合中等复杂度的应用，提供了比基础 Navigator 更好的代码组织和维护性。对于需要更高级功能（如深度链接、类型安全）的应用，建议考虑使用 `go_router` 等第三方路由库。

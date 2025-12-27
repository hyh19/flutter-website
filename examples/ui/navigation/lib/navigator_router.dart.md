# navigator_router.dart 代码解析

## 概述

这个文件展示了如何在 Flutter 应用中使用 `go_router` 包进行声明式路由管理。`go_router` 是 Flutter 官方推荐的现代路由解决方案，提供了类型安全、声明式的路由配置方式。

## 核心概念

### GoRouter 的优势

- **声明式路由**：路由配置集中管理，代码更清晰
- **类型安全**：支持类型安全的路由参数传递
- **深度链接支持**：天然支持 Web URL 和深度链接
- **导航栈管理**：自动管理导航栈，支持 `go`、`push`、`pop` 等操作

## 代码结构分析

### 应用入口

```dart 4:11:examples/ui/navigation/lib/navigator_router.dart
void main() {
  runApp(
    MaterialApp.router(
      title: 'Navigation with a router',
      routerConfig: _router,
    ),
  );
}
```

这里使用了 `MaterialApp.router` 构造函数，而不是普通的 `MaterialApp`。关键区别：

- `MaterialApp.router`：专门用于声明式路由，需要传入 `routerConfig` 参数
- `routerConfig`：接收一个 `GoRouter` 实例，用于配置所有路由规则

### 路由配置

```dart 13:19:examples/ui/navigation/lib/navigator_router.dart
// Declare routing information
final _router = GoRouter(
  routes: [
    GoRoute(path: '/', builder: (context, state) => const FirstScreen()),
    GoRoute(path: '/second', builder: (context, state) => const SecondScreen()),
  ],
);
```

路由配置说明：

- **`GoRouter`**：路由管理器，包含所有路由规则
- **`routes`**：路由列表，每个路由都是一个 `GoRoute` 对象
- **`GoRoute`**：单个路由配置
  - `path`：路由路径，支持路径参数（如 `/user/:id`）
  - `builder`：路由构建器，根据路径返回对应的 Widget

### 第一个屏幕

```dart 21:37:examples/ui/navigation/lib/navigator_router.dart
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
          onPressed: () => context.go('/second'),
          // #enddocregion push-route
        ),
      ),
    );
  }
}
```

关键点：

- **`context.go('/second')`**：使用 `go_router` 提供的扩展方法进行导航
  - `go` 方法会替换当前路由栈，直接跳转到目标路由
  - 与 `Navigator.push` 不同，`go` 不会在栈中添加新路由，而是替换当前路由
  - 适合用于应用内的主要导航（如底部导航栏切换）

### 第二个屏幕

```dart 39:53:examples/ui/navigation/lib/navigator_router.dart
class SecondScreen extends StatelessWidget {
  const SecondScreen({super.key});
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text('Second Screen')),
      body: Center(
        child: ElevatedButton(
          child: const Text('Go to first screen'),
          onPressed: () => context.go('/'),
          ),
        ),
      ),
    );
  }
}
```

这里同样使用 `context.go('/')` 返回到首页。

## 导航方法对比

### `context.go()` vs `context.push()`

- **`go(path)`**：替换当前路由，不保留导航历史
  - 适合：主要页面切换（如底部导航栏）
  - 行为：直接跳转到目标路由，无法通过返回按钮回到上一个页面

- **`push(path)`**：在导航栈中添加新路由
  - 适合：打开详情页、表单页等需要返回的场景
  - 行为：在栈中添加新路由，可以通过返回按钮或 `pop()` 返回

### 示例对比

```dart
// 使用 go - 替换当前路由
context.go('/second');  // 无法返回

// 使用 push - 添加新路由
context.push('/second');  // 可以返回
```

## 依赖要求

使用 `go_router` 需要在 `pubspec.yaml` 中添加依赖：

```yaml
dependencies:
  go_router: ^12.0.0
```

## 使用场景

### 适合使用 GoRouter 的情况

1. **需要深度链接支持**：Web 应用、需要分享特定页面的应用
2. **复杂路由结构**：嵌套路由、路由守卫、重定向等
3. **类型安全的路由参数**：需要传递类型安全的路由参数
4. **声明式路由管理**：希望集中管理所有路由配置

### 与其他导航方式的对比

- **基础 Navigator**：适合简单的页面跳转，代码量少
- **命名路由**：适合中等复杂度的应用，路由集中管理
- **GoRouter**：适合复杂应用，需要深度链接、类型安全等高级特性

## 扩展功能

### 路由参数传递

```dart
GoRoute(
  path: '/user/:id',
  builder: (context, state) {
    final id = state.pathParameters['id'];
    return UserScreen(userId: id);
  },
)

// 使用
context.go('/user/123');
```

### 查询参数

```dart
GoRoute(
  path: '/search',
  builder: (context, state) {
    final query = state.uri.queryParameters['q'];
    return SearchScreen(query: query);
  },
)

// 使用
context.go('/search?q=flutter');
```

### 路由重定向

```dart
final _router = GoRouter(
  redirect: (context, state) {
    // 未登录时重定向到登录页
    if (!isLoggedIn && state.uri.path != '/login') {
      return '/login';
    }
    return null;
  },
  routes: [...],
);
```

## 总结

这个示例展示了使用 `go_router` 进行声明式路由管理的基本用法。`go_router` 提供了现代化的路由解决方案，特别适合需要深度链接、类型安全和复杂路由管理的应用。通过 `context.go()` 方法可以方便地进行路由跳转，代码简洁且易于维护。

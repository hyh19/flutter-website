# Flutter 导航返回数据示例代码解析

## 概述

这是一个 Flutter 示例应用，演示了如何在页面导航时传递数据并接收返回结果。该示例展示了使用 `Navigator.push()` 导航到新页面，然后通过 `Navigator.pop()` 返回数据给上一个页面的完整流程。

## 应用结构

应用包含三个主要组件：

1. **HomeScreen**：应用的主屏幕
2. **SelectionButton**：触发导航的按钮组件
3. **SelectionScreen**：选择屏幕，可以返回不同的结果

## 代码详解

### 应用入口

```dart 3:5:examples/cookbook/navigation/returning_data/lib/main.dart
void main() {
  runApp(const MaterialApp(title: 'Returning Data', home: HomeScreen()));
}
```

应用入口函数创建了一个 `MaterialApp`，并将 `HomeScreen` 设置为首页。

### HomeScreen 组件

```dart 7:17:examples/cookbook/navigation/returning_data/lib/main.dart
class HomeScreen extends StatelessWidget {
  const HomeScreen({super.key});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text('Returning Data Demo')),
      body: const Center(child: SelectionButton()),
    );
  }
}
```

`HomeScreen` 是一个无状态的 `StatelessWidget`，它提供了一个带有应用栏的脚手架，并在页面中央显示 `SelectionButton` 组件。

### SelectionButton 组件

```dart 19:24:examples/cookbook/navigation/returning_data/lib/main.dart
class SelectionButton extends StatefulWidget {
  const SelectionButton({super.key});

  @override
  State<SelectionButton> createState() => _SelectionButtonState();
}
```

`SelectionButton` 是一个有状态的组件，因为它需要处理异步导航操作和显示返回的结果。

#### 按钮 UI

```dart 27:35:examples/cookbook/navigation/returning_data/lib/main.dart
  @override
  Widget build(BuildContext context) {
    return ElevatedButton(
      onPressed: () {
        _navigateAndDisplaySelection(context);
      },
      child: const Text('Pick an option, any option!'),
    );
  }
```

按钮的 `onPressed` 回调调用 `_navigateAndDisplaySelection` 方法来处理导航逻辑。

#### 核心导航方法

```dart 37:57:examples/cookbook/navigation/returning_data/lib/main.dart
  // #docregion navigateAndDisplay
  // A method that launches the SelectionScreen and awaits the result from
  // Navigator.pop.
  Future<void> _navigateAndDisplaySelection(BuildContext context) async {
    // Navigator.push returns a Future that completes after calling
    // Navigator.pop on the Selection Screen.
    final result = await Navigator.push(
      context,
      MaterialPageRoute<String>(builder: (context) => const SelectionScreen()),
    );

    // When a BuildContext is used from a StatefulWidget, the mounted property
    // must be checked after an asynchronous gap.
    if (!context.mounted) return;

    // After the Selection Screen returns a result, hide any previous snackbars
    // and show the new result.
    ScaffoldMessenger.of(context)
      ..removeCurrentSnackBar()
      ..showSnackBar(SnackBar(content: Text('$result')));
  }

  // #enddocregion navigateAndDisplay
```

这是整个示例的核心方法，展示了如何：

1. **导航到新页面**：使用 `Navigator.push()` 导航到 `SelectionScreen`，并指定返回类型为 `String`
2. **等待返回结果**：使用 `await` 等待 `Navigator.pop()` 返回的数据
3. **检查 mounted 状态**：在异步操作后检查 `context.mounted`，确保组件仍然挂载在树中（这是 Flutter 的最佳实践，避免在已销毁的组件上使用 `BuildContext`）
4. **显示结果**：使用 `ScaffoldMessenger` 显示一个 SnackBar 来展示返回的结果

**关键点**：

- `Navigator.push()` 返回一个 `Future`，该 `Future` 会在目标页面调用 `Navigator.pop()` 时完成
- `MaterialPageRoute<String>` 中的泛型参数指定了返回数据的类型
- 使用级联操作符（`..`）可以链式调用多个方法

### SelectionScreen 组件

```dart 62:102:examples/cookbook/navigation/returning_data/lib/main.dart
class SelectionScreen extends StatelessWidget {
  const SelectionScreen({super.key});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text('Pick an option')),
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: <Widget>[
            Padding(
              padding: const EdgeInsets.all(8),
              // #docregion Yep
              child: ElevatedButton(
                onPressed: () {
                  // Close the screen and return "Yep!" as the result.
                  Navigator.pop(context, 'Yep!');
                },
                child: const Text('Yep!'),
              ),
              // #enddocregion Yep
            ),
            Padding(
              padding: const EdgeInsets.all(8),
              // #docregion Nope
              child: ElevatedButton(
                onPressed: () {
                  // Close the screen and return "Nope." as the result.
                  Navigator.pop(context, 'Nope.');
                },
                child: const Text('Nope.'),
              ),
              // #enddocregion Nope
            ),
          ],
        ),
      ),
    );
  }
}
```

`SelectionScreen` 提供了两个按钮，每个按钮在点击时都会：

1. **关闭当前页面**：调用 `Navigator.pop()`
2. **返回数据**：将字符串结果（`'Yep!'` 或 `'Nope.'`）作为第二个参数传递给 `Navigator.pop()`

**关键点**：

- `Navigator.pop(context, result)` 的第一个参数是 `BuildContext`，第二个参数是要返回的数据
- 返回的数据类型必须与 `Navigator.push()` 时指定的泛型类型匹配（这里是 `String`）

## 工作流程

1. 用户点击 `SelectionButton` 上的按钮
2. `_navigateAndDisplaySelection` 方法被调用
3. 使用 `Navigator.push()` 导航到 `SelectionScreen`
4. 用户在 `SelectionScreen` 上选择 "Yep!" 或 "Nope."
5. 点击按钮后，`Navigator.pop()` 被调用，返回相应的字符串
6. `Navigator.push()` 返回的 `Future` 完成，结果被赋值给 `result` 变量
7. 检查 `context.mounted` 确保组件仍然有效
8. 使用 `ScaffoldMessenger` 显示包含返回结果的 SnackBar

## 最佳实践

### 1. 检查 mounted 状态

```dart 48:50:examples/cookbook/navigation/returning_data/lib/main.dart
    // When a BuildContext is used from a StatefulWidget, the mounted property
    // must be checked after an asynchronous gap.
    if (!context.mounted) return;
```

在异步操作后使用 `BuildContext` 之前，必须检查 `mounted` 属性。这是因为在异步操作期间，组件可能已经被销毁，如果继续使用已销毁组件的 `context`，会导致错误。

### 2. 类型安全

```dart 43:46:examples/cookbook/navigation/returning_data/lib/main.dart
    final result = await Navigator.push(
      context,
      MaterialPageRoute<String>(builder: (context) => const SelectionScreen()),
    );
```

使用泛型 `MaterialPageRoute<String>` 可以确保类型安全，编译器会检查返回的数据类型是否正确。

### 3. 清理之前的 SnackBar

```dart 54:56:examples/cookbook/navigation/returning_data/lib/main.dart
    ScaffoldMessenger.of(context)
      ..removeCurrentSnackBar()
      ..showSnackBar(SnackBar(content: Text('$result')));
```

在显示新的 SnackBar 之前，先移除当前显示的 SnackBar，避免多个 SnackBar 叠加显示。

## 扩展应用

这个示例可以轻松扩展以支持：

- **返回复杂对象**：将泛型类型从 `String` 改为自定义类，如 `MaterialPageRoute<User>`
- **返回 null**：如果用户取消操作，可以调用 `Navigator.pop(context, null)`
- **多个选择项**：在 `SelectionScreen` 中添加更多按钮，每个返回不同的值
- **条件导航**：根据返回的结果执行不同的操作

## 总结

这个示例展示了 Flutter 中页面间数据传递的标准模式：

- **发送数据**：通过 `Navigator.push()` 的 `MaterialPageRoute` 构造函数传递参数
- **返回数据**：通过 `Navigator.pop(context, result)` 的第二个参数返回数据
- **接收数据**：通过 `await Navigator.push()` 获取返回的 `Future` 结果

这是 Flutter 应用中处理页面间通信的常用且推荐的方式。

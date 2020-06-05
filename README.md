# react-native-safe-area-context

[![npm](https://img.shields.io/npm/v/react-native-safe-area-context)](https://www.npmjs.com/package/react-native-safe-area-context) ![Supports Android, iOS and web](https://img.shields.io/badge/platforms-android%20%7C%20ios%20%7C%20web-lightgrey.svg) ![MIT License](https://img.shields.io/npm/l/react-native-safe-area-context.svg)

[![JavaScript tests](https://github.com/th3rdwave/react-native-safe-area-context/workflows/JavaScript%20tests/badge.svg)](https://github.com/th3rdwave/react-native-safe-area-context/actions?query=workflow%3AJavaScript%20tests) [![iOS build](https://github.com/th3rdwave/react-native-safe-area-context/workflows/iOS%20build/badge.svg)](https://github.com/th3rdwave/react-native-safe-area-context/actions?query=workflow%3AiOS%20build) [![Android build](https://github.com/th3rdwave/react-native-safe-area-context/workflows/Android%20build/badge.svg)](https://github.com/th3rdwave/react-native-safe-area-context/actions?query=workflow%3AAndroid%20build)

A flexible way to handle safe area, also works on Android and Web!

一种灵活的处理安全区域的方式，同样适用于 Android 和 Web！

## Getting started

开始

```
npm install react-native-safe-area-context
```

You then need to link the native parts of the library for the platforms you are using.

你需要为你使用的平台链接原生包

#### Linking in React Native >= 0.60

Linking the package is not required anymore with [Autolinking](https://github.com/react-native-community/cli/blob/master/docs/autolinking.md).

React Native >= 0.60 通过自动链接就行了，不再需要手动链接。

- **iOS Platform:**

  iOS 平台

  `$ npx pod-install`


#### Linking in React Native < 0.60

The easiest way to link the library is using the CLI tool by running this command from the root of your project:

React Native < 0.60 时用以下的命令就能方便的链接原生库：

```
react-native link react-native-safe-area-context
```

If you can't or don't want to use the CLI tool, you can also manually link the library using the instructions below (click on the arrow to show them):

如果你无法或者不想使用 CLI 工具，你可以按以下步骤来手动链接原生库：

<details>
<summary>Manually link the library on iOS</summary>

手动链接到 iOS：

Either follow the [instructions in the React Native documentation](https://facebook.github.io/react-native/docs/linking-libraries-ios#manual-linking) to manually link the framework or link using [Cocoapods](https://cocoapods.org) by adding this to your `Podfile`:


参照 [React Native文档中的说明](https://reactnative.cn/docs/linking-libraries-ios#手动链接) 来手动链接，或使用 [Cocoapods](https://cocoapods.org)，将其添加到你的 `Podfile` 中：

```ruby
pod 'react-native-safe-area-context', :path => '../node_modules/react-native-safe-area-context'
```

</details>

<details>
<summary>Manually link the library on Android</summary>

手动链接到 Android：

Make the following changes:

做以下的改动：

#### `android/settings.gradle`

`android/settings.gradle` 文件的改动

```groovy
include ':react-native-safe-area-context'
project(':react-native-safe-area-context').projectDir = new File(rootProject.projectDir, '../node_modules/react-native-safe-area-context/android')
```

#### `android/app/build.gradle`

`android/app/build.gradle` 文件的改动

```groovy
dependencies {
   ...
   implementation project(':react-native-safe-area-context')
}
```

#### `android/app/src/main/.../MainApplication.java`

`android/app/src/main/.../MainApplication.java` 文件的改动

On top, where imports are:

在顶部引入包

```java
import com.th3rdwave.safeareacontext.SafeAreaContextPackage;
```

Add the `SafeAreaContextPackage` class to your list of exported packages.

增加 `SafeAreaContextPackage` 类的实例化

```java
@Override
protected List<ReactPackage> getPackages() {
    return Arrays.asList(
            new MainReactPackage(),
            ...
            new SafeAreaContextPackage()
    );
}
```

</details>

## Usage

用法

This library has 2 important concepts, if you are familiar with React Context this is very similar.

这个库有两个重要的概念，和 React Context 相似。

### Providers

The [SafeAreaProvider](#safeareaprovider) component is a `View` from where insets provided by [Consumers](#consumers) are relative to. This means that if this view overlaps with any system elements (status bar, notches, etc.) these values will be provided to descendent consumers. Usually you will have one provider at the top of your app.

[SafeAreaProvider](#safeareaprovider) 组件是一个“视图”，[Consumers]（＃consumers）提供的插图相对于此视图。这意味着，如果此视图与任何系统元素（状态栏，槽口等）重叠，则将这些值提供给后代使用者。通常，您的应用程序顶部将有一个提供程序。

### Consumers

Consumers are components and hooks that allow using inset values provided by the nearest parent [Provider](#providers). Values are always relative to a provider and not to these components.

Consumers 是一类组件和 hooks，可以使用祖先元素中离自己最近的一个 [Provider](#providers) 提供的值。这些值始终与 Provider 相关，而不与这些组件相关。

- [SafeAreaView](#safeareaview) is the preferred way to consume insets. This is a regular `View` with insets applied as extra padding or margin. It offers better performance by applying insets natively and avoids flickers that can happen with the other JS based consumers.


  [SafeAreaView](#safeareaview) 是使用插值的首选方式。这是一个常规的 “View”，插值被该组件用作额外的填充或边距。它通过原生的方式引入，提供了更好的性能，并且避免了其他基于 JS 开发的 Consumer 可能发生的闪烁。

  > 在原生层面提供安全区域

- [useSafeAreaInsets](#usesafeareainsets) offers more flexibility, but can cause some layout flicker in certain cases. Use this if you need more control over how insets are applied.

  [useSafeAreaInsets](#usesafeareainsets) 提供了更大的灵活性，但在一些情况下可能会导致某些布局闪烁。如果需要控制插值的应用，可以使用此选项。

  > 在 JS 层面使用这些边距值

## API

接口

### SafeAreaProvider

You should add `SafeAreaProvider` in your app root component. You may need to add it in other places like the root of modals and routes when using `react-native-screen`.


你应该在你的应用根组件中添加 `SafeAreaProvider`。如果使用了 ` react-native-screen`，你还需要在除应用根组件之外的地方添加它，例如 modal 和 routes 的根组件。

Note that providers should not be inside a `View` that is animated with `Animated` or inside a `ScrollView` since it can cause very frequent updates.

请注意，Providers 不应位于含有 `Animated` 动画的 `View` 内部，也不应位于 `ScrollView` 内部，因为它们可能导致非常频繁的更新。

#### Example

示例

```js
import { SafeAreaProvider } from 'react-native-safe-area-context';

function App() {
  return <SafeAreaProvider>...</SafeAreaProvider>;
}
```

#### Props

属性：

Accepts all [View](https://reactnative.dev/docs/view#props) props. Has a default style of `{flex: 1}`.

接受所有 `View` 组件的属性，另外，其拥有一个默认的样式 `flex: 1`。

##### `initialMetrics`

Optional, defaults to `null`.

可选，默认为 `null`。

Can be used to provide the initial value for frame and insets, this allows rendering immediatly. See [optimization](#optimization) for more information on how to use this prop.

可用于提供框架和内部区域的初始值，这可以立即进行渲染，提高性能。有关如何使用此道具的更多信息，请参见[optimization]（＃optimization）。

### SafeAreaView

`SafeAreaView` is a regular `View` component with the safe area insets applied as padding or margin.

`SafeAreaView` 是一个常规的 `View` 组件，其安全区域的边框大小用作填充或边距。

Padding or margin styles are added to the insets, for example `style={{paddingTop: 10}}` on a `SafeAreaView` that has insets of 20 will result in a top padding of 30.

可以将填充样式或边距样式作用到边框上，例如，`SafeAreaView` 上的 ` style={{ paddingTop：10 }}`，将导致在大小为 20 的原始边框机型上，顶部填充大小为 30。

#### Example

示例：

```js
import { SafeAreaView } from 'react-native-safe-area-context';

function SomeComponent() {
  return (
    <SafeAreaView style={{ flex: 1, backgroundColor: 'red' }}>
      <View style={{ flex: 1, backgroundColor: 'blue' }} />
    </SafeAreaView>
  );
}
```

### Props

属性

Accepts all [View](https://reactnative.dev/docs/view#props) props.

接受所有 [View](https://reactnative.dev/docs/view#props) 的属性

##### `edges`

Optional, array of `top`, `right`, `bottom`, and `left`. Defaults to all.

可选，由 `top`, `right`, `bottom`, 和 `left` 组成的数组。默认所有值都在数组里面。

Sets the edges to apply the safe area insets to.

设置哪些边缘应用安全区域边框。

For example if you don't want insets to apply to the top edge because the view does not touch the top of the screen you can use:

例如，如果你不希望将边框应用于顶部边缘，因为视图未触及屏幕顶部，则可以使用：

```js
<SafeAreaView edges={['right', 'bottom', 'left']} />
```

##### `mode`

Optional, `padding` (default) or `margin`.

可选，默认是 `padding`，还可以设成 `margin`。

Apply the safe area to either the padding or the margin.

设置安全区域边框为 `padding` 模式或 `margin` 模式。

This can be useful for example to create a safe area aware separator component:

这对于区分开安全区域和边框很有用：

```js
<SafeAreaView mode="margin" style={{ height: 1, backgroundColor: '#eee' }} />
```

### useSafeAreaInsets

Returns the safe area insets of the nearest provider. This allows manipulating the inset values from JavaScript. Note that insets are not updated synchronously so it might cause a slight delay for example when rotating the screen.

返回最近的 Provider 的安全区域边框。这允许在 JavaScript 层面处理边框值。请注意，边框值不会及时的同步更新，因此可能会导致轻微的延迟，例如在旋转屏幕时。

Object with `{ top: number, right: number, bottom: number: number, left: number }`.

返回的值是一个这样的对象： `{ top: number, right: number, bottom: number: number, left: number }`.

```js
import { useSafeAreaInsets } from 'react-native-safe-area-context';

function HookComponent() {
  const insets = useSafeAreaInsets();

  return <View style={{ paddingBottom: Math.max(insets.bottom, 16) }} />;
}
```


### useSafeAreaFrame

Returns the frame of the nearest provider. This can be used as an alternative to the `Dimensions` module.

返回最近的 Provider 的尺寸。可以用来替代 `Dimensions` 模块。

Object with `{ x: number, y: number, width: number, height: number }`

返回的值是一个这样的对象：`{ x: number, y: number, width: number, height: number }`

### `SafeAreaInsetsContext`

React Context with the value of the safe area insets.

Can be used with class components:

将安全区域边框的值设置在 React Context 中：

可以与类组件一起使用：

```js
import { SafeAreaInsetsContext } from 'react-native-safe-area-context';

class ClassComponent extends React.Component {
  render() {
    return (
      <SafeAreaInsetsContext.Consumer>
        {(insets) => <View style={{ paddingTop: insets.top }} />}
      </SafeAreaInsetsContext.Consumer>
    );
  }
}
```

### `withSafeAreaInsets`

Higher order component that provides safe area insets as the `insets` prop.

提供安全区域边框作为 `insets` 属性的高阶组件。

### `SafeAreaFrameContext`

React Context with the value of the safe area frame.

将安全区域尺寸的值设置在 React Context 中：

### `initialWindowMetrics`

Insets and frame of the window on initial render. This can be used with the `initialMetrics` from `SafeAreaProvider`. See [optimization](#optimization) for more information.

初始渲染时视口的边框和尺寸。可以与 SafeAreaProvider 中的initialMetrics 一起使用。有关更多信息，请参见 [optimization](#optimization)。

数据结构是：

```ts
{
  frame: { x: number, y: number, width: number, height: number },
  insets: { top: number, left: number, right: number, bottom: number },
}
```

## Deprecated apis

废弃的接口

### useSafeArea

Use `useSafeAreaInsets` intead.

### SafeAreaConsumer

Use `SafeAreaInsetsContext.Consumer` instead.

### SafeAreaContext

Use `SafeAreaInsetsContext` instead.

### initialWindowSafeAreaInsets

Use `initialWindowMetrics` instead.

## Web SSR

If you are doing server side rendering on the web you can use `initialMetrics` to inject insets and frame value based on the device the user has, or simply pass zero values. Since insets measurement is async it will break rendering your page content otherwise.


如果您要在 Web 页面上进行服务器端渲染，则可以使用 `initialMetrics` 根据用户拥有的设备注入边框和尺寸，或者简单地传递零值。因为边框测量是异步进行的，如果不传零值将无法呈现页面内容。

## Optimization

If you can, use `SafeAreaView`. It's implemented natively so when rotating the device, there is no delay from the asynchronous bridge.

如果可以的话，尽可能使用 `SafeAreaView`。它是原生实现的，因此在旋转设备时，异步桥不会产生任何延迟。

To speed up the initial render, you can import `initialWindowMetrics` from this package and set as the `initialMetrics` prop on the provider as described in Web SSR. You cannot do this if your provider remounts, or you are using `react-native-navigation`.

为了加快初始渲染速度，你可以导入此包中的 `initialWindowMetrics`，并按照 Web SSR 中的说明在 Provider 上将其设置为 `initialMetrics` 属性的值。如果你的 Provider 重新加载，或者你使用的是 `react-native-navigation`，则无法执行此操作。

```js
import {
  SafeAreaProvider,
  initialWindowMetrics,
} from 'react-native-safe-area-context';

function App() {
  return (
    <SafeAreaProvider initialMetrics={initialWindowMetrics}>
      ...
    </SafeAreaProvider>
  );
}
```

## Testing

测试

You can use `initialMetrics` to provide mock data for frame and insets.

您可以通过 `initialMetrics` 属性为尺寸和边框值提供一些模拟数据。

```js
export function TestSafeAreaProvider() {
  return (
    <SafeAreaProvider
      initialMetrics={{
        frame: { x: 0, y: 0, width: 0, height: 0 },
        insets: { top: 0, left: 0, right: 0, bottom: 0 },
      }}
    />
  );
}
```

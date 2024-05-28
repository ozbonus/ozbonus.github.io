---
title: Flutter Tap Target Size
category: [Programming,Guide]
tags: [flutter, guides]
img_path: /assets/img/
image: tap-target-title.webp
---

On mobile platforms you may have noticed that certain kinds of buttons always have some small amount of implicit padding, while on desktop they do not. In this blog post I'll describe why this happens and how you can customize the behavior in your own apps.

![](tap-target-spacing.webp)

## Why Flutter Does This

In the interest of accessibility, Material guidelines for tap targets (buttons, chips, etc.) specify a minimum size of 48x48 pixels on mobile devices. Here are a few scenarios that show how they may affect your Flutter app:

- Your button widget is naturally 40 pixels wide and 36 pixels tall.
  - An extra 8 pixels of horizontal padding and 12 pixels of vertical padding will be added.
- Your button widget is naturally 120 pixels wide and 36 pixels tall.
  - 12 pixels of vertical padding will added, with no additional horizontal padding.
- Your button widget is 100 pixels wide and 100 pixels tall.
  - No additional padding will be added.

Remember that the normal behavior for desktop platforms is to never add additional padding.

## Affected Widgets

Any built-in widget that a user can interact with by tapping is affected. Basically anything that has an `onPressed` or `onTap` property. These include `IconButton`, `FilledButton`, `TextButton`, `Chip`, `Radio`, `Switch`, `Checkbox`, and others which you can read about in the official documentation. [^docs]

[^docs]: https://api.flutter.dev/flutter/material/MaterialTapTargetSize.html 

## A Look Into the Source Code

Whether to carry out this behavior is decided by this piece of code in `flutter/lib/src/material/them_data.dart`:

```dart
switch (platform) {
  case TargetPlatform.android:
  case TargetPlatform.fuchsia:
  case TargetPlatform.iOS:
    materialTapTargetSize ??= MaterialTapTargetSize.padded;
  case TargetPlatform.linux:
  case TargetPlatform.macOS:
  case TargetPlatform.windows:
     materialTapTargetSize ??= MaterialTapTargetSize.shrinkWrap;
}
```


## Overriding the Default Behavior

You can force this behavior to be always off or on for all platforms by setting `materialTapTargetSize` in your app's theme. If, for example, you set it `MaterialTapTargetSize.shrinkWrap`, implicit padding will be disabled for all platforms, like so:

```dart
MaterialApp(
  theme: ThemeData(
    materialTapTargetSize: MaterialTapTargetSize.padded,
  ),
  // ...
)
```

![](tap-target-spacing-shrinkwrap.webp)

And this will enable it for all platforms.

```dart
MaterialApp(
  theme: ThemeData(
    materialTapTargetSize: MaterialTapTargetSize.shrinkWrap,
  ),
  // ...
)
```

![](tap-target-spacing-padded.webp)

## Wrap-up

Overriding the default tap target size behavior is easy to do, but should you do it? In most cases the default behavior is ideal, but if your app has a highly customized design, then the it may get in the way. Whatever the case, make sure that touch targets are still adequately large for users on touch-input devices.

## External Links
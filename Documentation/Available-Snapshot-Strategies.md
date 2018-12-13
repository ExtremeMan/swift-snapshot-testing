# Available Snapshot Strategies

SnapshotTesting comes with a wide variety of snapshot strategies for a varienty of platforms. To extend these strategies or define your own, see [Defining Custom Snapshot Strategies](Defining-Custom-Snapshot-Strategies.md).

If you'd like to submit your own custom strategy, see [Contributing](../CONTRIBUTING.md).

## List of Available Snapshot Strategies

  - [`Any`](#any)
      - [`.dump`](#dump)
  - [`CALayer`](#calayer)
      - [`.image`](#image)
  - [`Encodable`](#encodable)
      - [`.json`](#json)
      - [`.plist`](#plist)
  - [`NSImage`](#nsimage)
      - [`.image`](#image-1)
  - [`NSView`](#nsview)
      - [`.image`](#image-2)
      - [`.recursiveDescription`](#recursivedescription)
  - [`NSViewController`](#nsviewcontroller)
      - [`.image`](#image-3)
      - [`.recursiveDescription`](#recursivedescription-1)
  - [`SCNScene`](#scnscene)
      - [`.image`](#image-4)
  - [`SKScene`](#skscene)
      - [`.image`](#image-5)
  - [`String`](#string)
      - [`.lines`](#lines)
  - [`UIImage`](#uiimage)
      - [`.image`](#image-6)
  - [`UIView`](#uiview)
      - [`.image`](#image-7)
      - [`.recursiveDescription`](#recursivedescription-2)
  - [`UIViewController`](#uiviewcontroller)
      - [`.image`](#image-8)
      - [`.recursiveDescription`](#recursivedescription-3)
  - [`URLRequest`](#urlrequest)
      - [`.raw`](#raw)

## Any

**Platforms:** All

### `.dump`

A snapshot strategy for comparing _any_ structure based on a sanitized text dump.

The reference format looks a lot like the output of Swift's built-in [`dump`](https://developer.apple.com/documentation/swift/1539127-dump) function, though it does its best to make output deterministic by stripping out pointer memory addresses and sorting non-deterministic data, like dictionaries and sets.

You can hook into how an instance of a type is rendered in this strategy by conforming to the `AnySnapshotStringConvertible` protocol and defining the `snapshotDescription` property.

**Format:** `String`

#### Example:

``` swift
assertSnapshot(matching: user, as: .dump)
```

Records:

```
▿ User
  - bio: "Blobbed around the world."
  - id: 1
  - name: "Blobby"
```

## CALayer

**Platforms:** iOS, macOS, tvOS

### `.image`

A snapshot strategy for comparing layers based on pixel equality.

**Format:** `NSImage`, `UIImage`

#### Parameters:

  - `precision: Float = 1`

    The percentage of pixels that must match.

#### Example:

``` swift
// Match reference perfectly.
assertSnapshot(matching: layer, as: .image)

// Allow for a 1% pixel difference.
assertSnapshot(matching: layer, as: .image(precision: 0.99)
```

## Encodable

**Platforms:** All

### `.json`

A snapshot strategy for comparing encodable structures based on their JSON representation.

**Format:** `String`

#### Parameters:

  - `encoder: JSONEncoder` (optional)

#### Example:

``` swift
assertSnapshot(matching: user, as: .json)
```

Records:

``` json
{
  "bio" : "Blobbed around the world.",
  "id" : 1,
  "name" : "Blobby"
}
```

### `.plist`

A snapshot strategy for comparing encodable structures based on their property list representation.

**Format:** `String`

#### Parameters:

  - `encoder: PropertyListEncoder` (optional)

#### Example:

``` swift
assertSnapshot(matching: user, as: .plist)
```

Records:

``` xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
	<key>bio</key>
	<string>Blobbed around the world.</string>
	<key>id</key>
	<integer>1</integer>
	<key>name</key>
	<string>Blobby</string>
</dict>
</plist>
```

## NSImage

**Platforms:** macOS

### `.image`

A snapshot strategy for comparing images based on pixel equality.

**Format:** `UIImage`

#### Parameters:

  - `precision: Float = 1`

    The percentage of pixels that must match.

#### Example:

``` swift
// Match reference as-is.
assertSnapshot(matching: image, as: .image)

// Allow for a 1% pixel difference.
assertSnapshot(matching: image, as: .image(precision: 0.99)
```

## NSView

**Platforms:** macOS

### `.image`

A snapshot strategy for comparing layers based on pixel equality.

**Format:** `NSImage`

**Note:** Includes `SCNView`, `SKView`, `WKWebView`.

#### Parameters:

  - `precision: Float = 1`

    The percentage of pixels that must match.

  - `size: CGSize = nil`

    A view size override.
    
  - `traits: UITraitCollection = .init()`

    A trait collection override.

#### Example:

``` swift
// Match reference as-is.
assertSnapshot(matching: view, as: .image)

// Allow for a 1% pixel difference.
assertSnapshot(matching: view, as: .image(precision: 0.99)

// Render at a certain size.
assertSnapshot(
  matching: view,
  as: .image(size: .init(width: 44, height: 44)
)

// Render with a horizontally-compact size class.
assertSnapshot(
  matching: view,
  as: .image(traits: .init(horizontalSizeClass: .regular))
)
```

### `.recursiveDescription`

A snapshot strategy for comparing views based on a recursive description of their properties and hierarchies.

**Format:** `String`

#### Example

``` swift
assertSnapshot(matching: view, as: .recursiveDescription)
```

Records:

```
[   AF      LU ] h=--- v=--- NSButton "Push Me" f=(0,0,77,32) b=(-)
  [   A       LU ] h=--- v=--- NSButtonBezelView f=(0,0,77,32) b=(-)
  [   AF      LU ] h=--- v=--- NSButtonTextField "Push Me" f=(10,6,57,16) b=(-)
A=autoresizesSubviews, C=canDrawConcurrently, D=needsDisplay, F=flipped, G=gstate, H=hidden (h=by ancestor), L=needsLayout (l=child needsLayout), U=needsUpdateConstraints (u=child needsUpdateConstraints), O=opaque, P=preservesContentDuringLiveResize, S=scaled/rotated, W=wantsLayer (w=ancestor wantsLayer), V=needsVibrancy (v=allowsVibrancy), #=has surface
```

## NSViewController

### `.image`

A snapshot strategy for comparing layers based on pixel equality.

**Format:** `NSImage`

#### Parameters:

  - `precision: Float = 1`

    The percentage of pixels that must match.

  - `size: CGSize = nil`

    A view size override.

#### Example:

``` swift
// Match reference as-is.
assertSnapshot(matching: vc, as: .image)

// Allow for a 1% pixel difference.
assertSnapshot(matching: vc, as: .image(precision: 0.99)

// Render at a certain size.
assertSnapshot(
  matching: vc,
  as: .image(size: .init(width: 640, height: 480)
)
```

**See also**: [`NSView`](#nsview).

### `.recursiveDescription`

A snapshot strategy for comparing view controller views based on a recursive description of their properties and hierarchies.

**Format:** `String`

#### Example

``` swift
assertSnapshot(matching: vc, as: .recursiveDescription)
```

Records:

```
[   AF      LU ] h=--- v=--- NSButton "Push Me" f=(0,0,77,32) b=(-)
  [   A       LU ] h=--- v=--- NSButtonBezelView f=(0,0,77,32) b=(-)
  [   AF      LU ] h=--- v=--- NSButtonTextField "Push Me" f=(10,6,57,16) b=(-)
A=autoresizesSubviews, C=canDrawConcurrently, D=needsDisplay, F=flipped, G=gstate, H=hidden (h=by ancestor), L=needsLayout (l=child needsLayout), U=needsUpdateConstraints (u=child needsUpdateConstraints), O=opaque, P=preservesContentDuringLiveResize, S=scaled/rotated, W=wantsLayer (w=ancestor wantsLayer), V=needsVibrancy (v=allowsVibrancy), #=has surface
```

## SCNScene

### `.image`

A snapshot strategy for comparing SceneKit scenes based on pixel equality.

**Platforms:** iOS, macOS, tvOS

#### Parameters:

  - `precision: Float = 1`

    The percentage of pixels that must match.

  - `size: CGSize`

    The size of the scene.

#### Example:

``` swift
assertSnapshot(
  matching: scene,
  as: .image(size: .init(width: 640, height: 480))
)
```

**See also**: [`NSView`](#nsview), [`UIView`](#uiview).

## SKScene

### `.image`

A snapshot strategy for comparing SpriteKit scenes based on pixel equality.

**Platforms:** iOS, macOS, tvOS

#### Parameters:

  - `precision: Float = 1`

    The percentage of pixels that must match.

  - `size: CGSize`

    The size of the scene.

#### Example:

``` swift
assertSnapshot(
  matching: scene,
  as: .image(size: .init(width: 640, height: 480))
)
```

**See also**: [`NSView`](#nsview), [`UIView`](#uiview).

## String

**Platforms:** All

### `.lines`

A snapshot strategy for comparing strings based on equality.

**Format:** `String`

#### Example:

``` swift
assertSnapshot(matching: htmlString, as: .lines)
```

## UIImage

**Platforms:** iOS, tvOS

### `.image`

A snapshot strategy for comparing images based on pixel equality.

**Format:** `UIImage`

#### Parameters:

  - `precision: Float = 1`

    The percentage of pixels that must match.

#### Example:

``` swift
// Match reference as-is.
assertSnapshot(matching: image, as: .image)

// Allow for a 1% pixel difference.
assertSnapshot(matching: image, as: .image(precision: 0.99)
```

## UIView

**Platforms:** iOS, tvOS

### `.image`

A snapshot strategy for comparing layers based on pixel equality.

**Format:** `UIImage`

**Note:** Includes `SCNView`, `SKView`, `WKWebView`.

#### Parameters:

  - `drawHierarchyInKeyWindow: Bool = false`

    Utilize the simulator's key window in order to render `UIAppearance` and `UIVisualEffect`s. This option requires a host application for your tests and will _not_ work for framework test targets.

  - `precision: Float = 1`

    The percentage of pixels that must match.

  - `size: CGSize = nil`

    A view size override.
    
  - `traits: UITraitCollection = .init()`

    A trait collection override.

#### Example:

``` swift
// Match reference as-is.
assertSnapshot(matching: view, as: .image)

// Allow for a 1% pixel difference.
assertSnapshot(matching: view, as: .image(precision: 0.99)

// Render at a certain size.
assertSnapshot(
  matching: view,
  as: .image(size: .init(width: 44, height: 44)
)

// Render with a horizontally-compact size class.
assertSnapshot(
  matching: view,
  as: .image(traits: .init(horizontalSizeClass: .regular))
)
```

### `.recursiveDescription`

A snapshot strategy for comparing views based on a recursive description of their properties and hierarchies.

**Format:** `String`

#### Example

``` swift
assertSnapshot(matching: view, as: .recursiveDescription)
```

Records:

```
<UIButton; frame = (0 0; 22 22); opaque = NO; layer = <CALayer>>
   | <UIImageView; frame = (0 0; 22 22); clipsToBounds = YES; opaque = NO; userInteractionEnabled = NO; layer = <CALayer>>
```

## UIViewController

**Platforms:** iOS, tvOS

### `.image`

A snapshot strategy for comparing layers based on pixel equality.

**Format:** `UIImage`

#### Parameters:

  - `drawHierarchyInKeyWindow: Bool = false`

    Utilize the simulator's key window in order to render `UIAppearance` and `UIVisualEffect`s. This option requires a host application for your tests and will _not_ work for framework test targets.
    
    _Incompatible with the `on` parameter._
    
  - `on: ViewImageConfig`

    A set of device configuration settings.
    
    _Incompatible with the `drawHierarchyInKeyWindow` parameter._

  - `precision: Float = 1`

    The percentage of pixels that must match.

  - `size: CGSize = nil`

    A view size override.
    
  - `traits: UITraitCollection = .init()`

    A trait collection override.

#### Example:

``` swift
// Match reference as-is.
assertSnapshot(matching: vc, as: .image)

// Allow for a 1% pixel difference.
assertSnapshot(matching: vc, as: .image(precision: 0.99)

// Render as if on a certain device.
assertSnapshot(matching: vc, on: .iPhoneX(.portrait))

// Render at a certain size.
assertSnapshot(
  matching: vc,
  as: .image(size: .init(width: 375, height: 667)
)

// Render with a horizontally-compact size class.
assertSnapshot(
  matching: vc,
  as: .image(traits: .init(horizontalSizeClass: .regular))
)

// Match reference as-is.
assertSnapshot(matching: vc, as: .image)
```

**See also**: [`UIView`](#uiview).

### `.recursiveDescription`

A snapshot strategy for comparing view controller views based on a recursive description of their properties and hierarchies.

**Format:** `String`

#### Example

``` swift
assertSnapshot(matching: vc, as: .recursiveDescription)
```

Records:

```
<UIView; frame = (0 0; 375 667); opaque = NO; layer = <CALayer>>
   | <UIImageView; frame = (0 0; 375 667); clipsToBounds = YES; opaque = NO; userInteractionEnabled = NO; layer = <CALayer>>
```

## URLRequest

**Platforms:** All

### `.raw`

A snapshot strategy for comparing requests based on raw equality.

**Format:** `String`

#### Example:

``` swift
assertSnapshot(matching: request, as: .raw)
```

Records:

```
POST http://localhost:8080/account
Cookie: pf_session={"userId":"1"}

email=blob%40pointfree.co&name=Blob
```
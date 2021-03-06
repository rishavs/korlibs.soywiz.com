---
layout: default
title: Filters
title_prefix: KorGE
fa-icon: fa-adjust
priority: 70
---

Views can have filters attached that change how the view and its children are displayed.

```kotlin
var View.filter: Filter? = null
```

{% include toc_include.md %}

## ComposedFilter

You can apply several filters to a view using this:

```kotlin
class ComposedFilter(val filters: List<Filter>) : Filter()
```

## IdentityFilter

This filter can be used to no apply filters at all. But serves for the subtree to be rendered in a texture.
This is useful for example to change the alpha of a complex object as a whole,
instead of affecting each alpha of the descendant views.

```kotlin
object IdentityFilter : Filter()
```

```kotlin
container {
	scale(0.8)
	position(24, 24)
	alpha = 0.25
	filter = IdentityFilter
	image(resourcesVfs["korge.png"].readBitmap())
	image(resourcesVfs["korge.png"].readBitmap()) {
		position(64, 64)
	}
}
```

<div style="display:flex;width:100%;max-width:100%;">
<img src="/i/filters/identity_complex_off.png" style="max-width:50%;" />
<img src="/i/filters/identity_complex_on.png" style="max-width:50%;" />
</div>

## ColorMatrixFilter

```kotlin
class ColorMatrixFilter(var colorMatrix: Matrix3D, var blendRatio: Double) : Filter() {
    companion object {
        val GRAYSCALE_MATRIX = Matrix3D.fromRows(
            0.33f, 0.33f, 0.33f, 0f,
            0.59f, 0.59f, 0.59f, 0f,
            0.11f, 0.11f, 0.11f, 0f,
            0f, 0f, 0f, 1f
        )
        
        val IDENTITY_MATRIX = Matrix3D.fromRows(
            1f, 0f, 0f, 0f,
            0f, 1f, 0f, 0f,
            0f, 0f, 1f, 0f,
            0f, 0f, 0f, 1f
        )
    }
}
```

```kotlin
view.filter = ColorMatrixFilter(ColorMatrixFilter.GRAYSCALE_MATRIX)
```

![](/i/filters/color_matrix.png)

## Convolute3Filter

```kotlin
class Convolute3Filter(var kernel: Matrix3D) : Filter() {
    companion object {
        val KERNEL_GAUSSIAN_BLUR: Matrix3D
        val KERNEL_BOX_BLUR: Matrix3D
        val KERNEL_IDENTITY: Matrix3D
        val KERNEL_EDGE_DETECTION: Matrix3D
    }
}
```

```kotlin
view.filter = ComposedFilter((0 until 5).map { Convolute3Filter(Convolute3Filter.KERNEL_GAUSSIAN_BLUR) })
```

![](/i/filters/blur.png)

## WaveFilter and PageFilter

Can be used to simulate pages from books:

```kotlin
class PageFilter(
    var hratio: Double = 0.5,
    var hamplitude0: Double = 0.0,
    var hamplitude1: Double = 10.0,
    var hamplitude2: Double = 0.0,
    var vratio: Double = 0.5,
    var vamplitude0: Double = 0.0,
    var vamplitude1: Double = 0.0,
    var vamplitude2: Double = 0.0
) : Filter()
```

```kotlin
view.filter = PageFilter(hamplitude1 = 64.0)
```

![](/i/filters/page.png)

```kotlin
class WaveFilter(
    var amplitudeX: Int = 10,
    var amplitudeY: Int = 10,
    var crestCountX: Double = 2.0,
    var crestCountY: Double = 2.0,
    var cyclesPerSecondX: Double = 1.0,
    var cyclesPerSecondY: Double = 1.0,
    var time: Double = 0.0
) : Filter()
```

![](/i/filters/wave.png)

## SwizzleColorsFilter

Serves to do component swizzling (RGBA) per pixel:

```kotlin
class SwizzleColorsFilter(var swizzle: String = "rgba") : Filter()
```

```kotlin
view.filter = SwizzleColorsFilter.ProxySwizzle("bgra")
```

![](/i/filters/swizzle_color.png)

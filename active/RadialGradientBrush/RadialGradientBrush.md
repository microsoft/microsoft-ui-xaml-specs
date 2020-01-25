<!-- The purpose of this spec is to describe a new feature and
its APIs that make up a new feature in WinUI. -->

<!-- There are two audiences for the spec. The first are people
that want to evaluate and give feedback on the API, as part of
the submission process.  When it's complete
it will be incorporated into the public documentation at
docs.microsoft.com (http://docs.microsoft.com/uwp/toolkits/winui/).
Hopefully we'll be able to copy it mostly verbatim.
So the second audience is everyone that reads there to learn how
and why to use this API. -->

# Background

<!-- Use this section to provide background context for the new API(s)
in this spec. -->

<!-- This section and the appendix are the only sections that likely
do not get copied to docs.microsoft.com; they're just an aid to reading this spec. -->

<!-- If you're modifying an existing API, included a link here to the
existing page(s) -->

<!-- For example, this section is a place to explain why you're adding this API rather than
modifying an existing API. -->

<!-- For example, this is a place to provide a brief explanation of some dependent
area, just explanation enough to understand this new API, rather than telling
the reader "go read 100 pages of background information posted at ...". -->

## Composition brush usage

The Xaml RadialGradientBrush will inherit from [XamlCompositionBrushBase](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.xamlcompositionbrushbase) and use a [CompositionRadialGradientBrush](https://docs.microsoft.com/uwp/api/windows.ui.composition.compositionradialgradientbrush), which is supported on Windows 10 1903 (v10.0.18362+). This seems like a useful model for new brushes going forward for both WinUI 2 and 3, and we aren't too concerned about duplicating some properties from other candidate base classes [GradientBrush](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.gradientbrush).

The RadialGradientBrush API will more closely mirror the behavior of the Composition API than the WPF API: for example, the gradient origin defaults to center and provides a `GradientOriginOffset` property to specify an offset rather than providing a `GradientOrigin` property.

Notable inconsistencies with the composition API include:
1. Usage of [Point](https://docs.microsoft.com/uwp/api/windows.foundation.point) rather than [Vector2](https://docs.microsoft.com/uwp/api/windows.foundation.numerics.vector2)/[float2](https://docs.microsoft.com/windows/win32/numerics_h/float2-structure)
2. Usage of [GradientStop](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.gradientstop) rather than [CompositionColorGradientStop](https://docs.microsoft.com/uwp/api/windows.ui.composition.compositioncolorgradientstop)

The main reason for the above is to enable Xaml markup parsing for brushes defined in markup.

### Animation support

Ideally, RadialGradientBrush would support animation of GradientStops and other properties using composition animations by implementing [IAnimationObject](https://docs.microsoft.com/uwp/api/windows.ui.composition.ianimationobject). However, the `IAnimationObject` interface and support for animation of non-composition types is only supported on Windows 10 1809 (v10.0.17763) and newer. This could be revisited with WinUI 3.

## Downlevel fallback behavior

The options for fallback behavior on downlevel pre-1903 OS versions are:

1. Draw a gradient using another renderer, e.g. Direct2D
   * This would require loading additional expensive dependencies which WinUI doesn't currently rely on.
2. Draw a custom gradient surface, e.g. using a CompositionSurfaceBrush or WriteableBitmap
   * This would require largely reimplementing the complex CompositionRadialGradientBrush.
3. **Rely on FallbackColor**
   * Since [CompositionRadialGradientBrush](https://docs.microsoft.com/uwp/api/windows.ui.composition.compositionradialgradientbrush) should be available downlevel with WinUI 3, #1 (FallbackColor) seems like a reasonable approach in the meantime.

# Description

<!-- Use this section to provide a brief description of the feature.
For an example, see the introduction to the PasswordBox control
(http://docs.microsoft.com/windows/uwp/design/controls-and-patterns/password-box). -->

Paints an area with a radial gradient. A center point defines the origin of the gradient, and an ellipse defines the outer bounds of the gradient.

# Examples

<!-- Use this section to explain the features of the API, showing
example code with each description. The general format is:
  feature explanation,
  example code
  feature explanation,
  example code
  etc.-->

<!-- Code samples should be in C# and/or C++/WinRT -->

<!-- As an example of this section, see the Examples section for the PasswordBox control
(https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/password-box#examples). -->

This example creates a radial gradient with six gradient stops and uses it to paint a Rectangle.

![A rectangle filled with a radial gradient.](images/ColorRadialGradientBrush.png)

XAML
```XAML
<Page
  xmlns:media="using:Microsoft.UI.Xaml.Media">

  <Rectangle Width="200" Height="200">
      <Rectangle.Fill>
          <media:RadialGradientBrush>
              <GradientStop Color="Blue" Offset="0.0" />
              <GradientStop Color="Yellow" Offset="0.2" />
              <GradientStop Color="LimeGreen" Offset="0.4" />
              <GradientStop Color="LightBlue" Offset="0.6" />
              <GradientStop Color="Blue" Offset="0.8" />
              <GradientStop Color="LightGray" Offset="1" />
          </media:RadialGradientBrush>
      </Rectangle.Fill>
  </Rectangle>

</Page>
```

This example creates a radial gradient with that uses Absolute mapping mode with custom values for `EllipseCenter`, `EllipseRadius` and `GradientOriginOffset`:

![A rectangle filled with an offset radial gradient.](images/OffsetRadialGradientBrush.png)

```XAML
<Page
  xmlns:media="using:Microsoft.UI.Xaml.Media">

  <Rectangle Width="200" Height="200">
      <Rectangle.Fill>
          <media:RadialGradientBrush
            MappingMode="Absolute"
            EllipseCenter="50,50"
            EllipseRadius="100,100"
            GradientOriginOffset="50,0"
            >
              <GradientStop Color="Yellow" Offset="0.0" />
              <GradientStop Color="Blue" Offset="1" />
          </media:RadialGradientBrush>
      </Rectangle.Fill>
  </Rectangle>

</Page>
```

# Remarks
<!-- Explanation and guidance that doesn't fit into the Examples section. -->

<!-- APIs should only throw exceptions in exceptional conditions; basically,
only when there's a bug in the caller, such as argument exception.  But if for some
reason it's necessary for a caller to catch an exception from an API, call that
out with an explanation either here or in the Examples -->

## Gradient layout

The gradient is drawn within an ellipse that is defined by the `EllipseCenter` and `EllipseRadius` properties. Colors for the gradient start at the center of the ellipse and end at the radius.

The colors for the radial gradient are defined by color stops added to the `GradientStops` collection property. Each gradient stop specifies a color and an offset along the gradient.

The gradient origin defaults to center and can be offset using the `GradientOriginOffset` property.

`MappingMode` defines whether `EllipseCenter`, `EllipseRadius` and `GradientOriginOffset` represent relative or absolute coordinates.

When `MappingMode` is set to `RelativeTBoundingBox`, the X and Y values of the three properties are treated as relative to the brush's rendered bounds, where `(0,0)` represents the top left and `(1,1)` represents the bottom right of the brush's rendered bounds for the `EllipseCenter` and `EllipseRadius` properties and `(0,9)` represents the center for the `GradientOriginOffset` property.

When `MappingMode` is set to `Absolute`, the X and Y values of the three properties are treated as absolute coordinates within the brush's rendered bounds.

## InterpolationSpace

The underlying [CompositionRadialGradientBrush](https://docs.microsoft.com/uwp/api/windows.ui.composition.compositionradialgradientbrush) only supports the following [CompositionColorSpace](https://docs.microsoft.com/uwp/api/windows.ui.composition.compositioncolorspace) values:

* `Auto`
* `Rgb`
* `RgbLinear`

Applying any other interpolation color space will have no effect.

## Windows 10 Version Support

Gradient rendering is supported on Windows 10 version 1903 (v10.0.18362.0) and higher. On previous OS versions the brush will render a solid color specified by the `FallbackColor` property.

## See Also

* [LinearGradientBrush](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.LinearGradientBrush)

* [CompositionRadialGradientBrush](https://docs.microsoft.com/uwp/api/windows.ui.composition.compositionradialgradientbrush)

# API Notes

<!-- Option 1: Give a one or two line description of each API (type
and member), or at least the ones that aren't obvious
from their name.  These descriptions are what show up
in IntelliSense. For properties, specify the default value of the property if it
isn't the type's default (for example an int-typed property that doesn't default to zero.) -->

<!-- Option 2: Put these descriptions in the below API Details section,
with a "///" comment above the member or type. -->

## Fields

| | |
| - | - |
| EllipseCenter | The center of the ellipse that contains the gradient. The default is `(0.5, 0.5)`. |
| EllipseRadius | The radius of the ellipse that contains the gradient. The default is `(0.5, 0.5)`. |
| GradientOriginOffset | The gradient origin's offset from the center of the element. |
| GradientStops | A collection of [GradientStop](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.gradientstop) objects that define the gradient. |
| InterpolationSpace | The color space used to interpolate the gradient's colors. The default is `Auto`. |
| MappingMode | Defines whether `EllipseCenter`, `EllipseRadius` and `GradientOriginOffset` represent relative coordinates in the range 0 to 1 or absolute coordinates. The default is `RelativeToBoundingBox`. |

# API Details
<!-- The exact API, in MIDL3 format (https://docs.microsoft.com/en-us/uwp/midl-3/) -->

```C++
[webhosthidden]
[MUX_PROPERTY_CHANGED_CALLBACK(TRUE)]
[MUX_PROPERTY_CHANGED_CALLBACK_METHODNAME("OnPropertyChanged")]
[contentproperty("GradientStops")]
unsealed runtimeclass RadialGradientBrush : Windows.UI.Xaml.Media.XamlCompositionBrushBase
{
    RadialGradientBrush();

    [MUX_DEFAULT_VALUE("winrt::Point(0.5,0.5)")]
    Windows.Foundation.Point EllipseCenter { get; set; };

    [MUX_DEFAULT_VALUE("winrt::Point(0.5,0.5)")]
    Windows.Foundation.Point EllipseRadius { get; set; };

    Windows.Foundation.Point GradientOriginOffset { get; set; };

    [MUX_DEFAULT_VALUE("winrt::BrushMappingMode::RelativeToBoundingBox")]
    Windows.UI.Xaml.Media.BrushMappingMode MappingMode { get; set; };

    [MUX_DEFAULT_VALUE("winrt::Windows::UI::Composition::CompositionColorSpace::Auto")]
    Windows.UI.Composition.CompositionColorSpace InterpolationSpace { get; set; };

    [MUX_PROPERTY_CHANGED_CALLBACK(FALSE)]
    Windows.Foundation.Collections.IObservableVector<Windows.UI.Xaml.Media.GradientStop> GradientStops { get; };

    static Windows.UI.Xaml.DependencyProperty EllipseCenterProperty { get; };
    static Windows.UI.Xaml.DependencyProperty EllipseRadiusProperty { get; };
    static Windows.UI.Xaml.DependencyProperty GradientOriginOffsetProperty { get; };
    static Windows.UI.Xaml.DependencyProperty InterpolationSpaceProperty { get; };
    static Windows.UI.Xaml.DependencyProperty MappingModeProperty { get; };
}
```

<!-- # Appendix -->
<!-- Anything else that you want to write down for posterity, but
that isn't necessary to understand the purpose and usage of the API.
For example, implementation details. -->

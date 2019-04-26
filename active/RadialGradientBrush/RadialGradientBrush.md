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

<!-- # Background -->
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


# Description
<!-- Use this section to provide a brief description of the feature.
For an example, see the introduction to the PasswordBox control 
(http://docs.microsoft.com/windows/uwp/design/controls-and-patterns/password-box). -->

Paints an area with a radial gradient. A focal point defines the beginning of the gradient, and an ellipse defines the end point of the gradient. Stops can be used to mark the points at which the gradient changes from one color to the next. 

RadialGradientBrush can be animated by changing the location and color of stops over time. 

## Is this the right control? 

Use **RadialGradientBrush** to create interest and depth through a non-linear application of color.

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

### Create a radial gradient in markup

This example creates a radial gradient with four colors and uses it to paint a Rectangle.

![](images/ColorRadialGradientBrush.png)

XAML
```XAML
<StackPanel>
  <!-- This rectangle is painted with a radial gradient. -->
  <Rectangle Width="200" Height="100">
    <Rectangle.Fill>
      <RadialGradientBrush EllipseCenter="0.5,0.5" EllipseRadius="0.5,0.5" GradientOrigin="0.5,0.5">
        <GradientStop Color="Blue" Offset="0.0" />
        <GradientStop Color="Yellow" Offset="0.2" />
        <GradientStop Color="LimeGreen" Offset="0.4" />
        <GradientStop Color="LightBlue" Offset="0.6" />
        <GradientStop Color="Blue" Offset="0.8" />
        <GradientStop Color="White" Offset="1.0" />
      </RadialGradientBrush>
    </Rectangle.Fill>
  </Rectangle>
</StackPanel>
```

# Remarks
<!-- Explanation and guidance that doesn't fit into the Examples section. -->

<!-- APIs should only throw exceptions in exceptional conditions; basically,
only when there's a bug in the caller, such as argument exception.  But if for some
reason it's necessary for a caller to catch an exception from an API, call that
out with an explanation either here or in the Examples -->

### Related articles 

* [AcrylicBrush](https://docs.microsoft.com/en-us/uwp/api/windows.ui.xaml.media.acrylicbrush)
* [SolidColorBrush](https://docs.microsoft.com/en-us/uwp/api/Windows.UI.Xaml.Media.SolidColorBrush)
* [LinearGradientBrush](https://docs.microsoft.com/en-us/uwp/api/Windows.UI.Xaml.Media.LinearGradientBrush)
* [ImageBrush](https://docs.microsoft.com/en-us/uwp/api/Windows.UI.Xaml.Media.ImageBrush)
* [WebViewBrush](https://docs.microsoft.com/en-us/uwp/api/Windows.UI.Xaml.Controls.WebViewBrush)
* [XamlCompositionBrushBase](https://docs.microsoft.com/en-us/uwp/api/windows.ui.xaml.media.xamlcompositionbrushbase)
* [Composition brushes](https://docs.microsoft.com/en-us/windows/uwp/composition/composition-brushes)

<!-- ## Recommendations -->

<!-- # API Notes -->
<!-- Option 1: Give a one or two line description of each API (type
and member), or at least the ones that aren't obvious
from their name.  These descriptions are what show up
in IntelliSense. For properties, specify the default value of the property if it
isn't the type's default (for example an int-typed property that doesn't default to zero.) -->

<!-- Option 2: Put these descriptions in the below API Details section,
with a "///" comment above the member or type. -->

# API Details
<!-- The exact API, in MIDL3 format (https://docs.microsoft.com/en-us/uwp/midl-3/) -->

```C++
runtimeclass RadialGradientBrush : Windows.UI.Xaml.Media.GradientBrush
{
    RadialGradientBrush();

    Windows.Foundation.Numerics.Vector2 EllipseCenter; 
    Windows.Foundation.Numerics.Vector2 EllipseRadius; 
    Windows.Foundation.Numerics.Vector2 GradientOrigin; 

    Windows.UI.Xaml.Media.GradientStopCollection GradientStopCollection

    Boolean IsFeatheringEnabled;

    static Windows.UI.Xaml.DependencyProperty EllipseCenterProperty{ get; };
    static Windows.UI.Xaml.DependencyProperty EllipseRadiusProperty{ get; };
    static Windows.UI.Xaml.DependencyProperty GradientOriginProperty{ get; };
    static Windows.UI.Xaml.DependencyProperty IsFeatheringEnabledProperty{ get; };
    static Windows.UI.Xaml.DependencyProperty GradientStopCollectionProperty{ get; };
};
```

<!-- # Appendix -->
<!-- Anything else that you want to write down for posterity, but 
that isn't necessary to understand the purpose and usage of the API.
For example, implementation details. -->

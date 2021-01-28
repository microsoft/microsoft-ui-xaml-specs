# Image Icon

## Background

Today, WinUI (Xaml) has a way to add icons in different formats using the elements that inherit from 
[IconElement](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.IconElement).
These are: BitmapIcon, FontIcon, SymbolIcon, PathIcon, and IconSourceElement.

BitmapIcon supports a set of bitmap types such as png and jpeg
(the same set supported by 
[BitmapImage](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Imaging.BitmapImage)).
The general [Image](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Image)
element on the other hand supports these and more types, plus supports loading types from
a stream rather than just URI.

The solution in this spec is a new `ImageIcon` type, which like the other icons is an IconElement
(so can be used where IconElement is required, such as 
[NavigationViewItem.Icon](https://docs.microsoft.com/uwp/api/Microsoft.UI.Xaml.Controls.NavigationViewItem.Icon)).
Other than the base class, ImageIcon is equivalent to Image.

And ImageIcon has a superset of the functionality in BitmapIcon, except for the
[BitmapIcon.ShowAsMonochrome](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.BitmapIcon.ShowAsMonochrome)
property, which could be added at a later time.

ImageIcon is a superset of the existing BitmapIcon minus the
[BitmapIcon.ShowAsMonochrome](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.BitmapIcon.ShowAsMonochrome)
property, as it is not a requirement from design to have all icons be monochrome.
Since ShowAsMonochrome will not be part of ImageIcon, the 
[IconElement.Foreground](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.IconElement.Foreground)
inherited from IconElement will not be suported in this control.
It will be up to the application to update the icon to match the user's theme. 

# API Pages

## ImageIcon class

Represents an icon that uses an image as its content.

ImageIcon can be used just like an
[Image](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Image)
element, except it derives from
[IconElement](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.IconElement)
and can therefore be used anywhere an IconElement is required, such as
[NavigationViewItem.Icon](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.NavigationViewItem.Icon).

> Note: the ImageIcon.Foreground property is currently ignored.

## Is this the right control? 

Use an ImageIcon control when you want to display an image as your icon. Do not use an ImageIcon if you want to display an icon from a font. 

## Examples
Below is an example of how you can use ImageIcon in an App Bar Button Item.

> Spec note: This example will also be added to the App Bar Button Item page with the rest of the icon controls. 

XAML
```xml
<AppBarButton Label="ImageIcon" Click="AppBarButton_Click">
    <AppBarButton.Icon>
        <ImageIcon Source="Wheel.png"/>
    </AppBarButton.Icon>
</AppBarButton>
```

## ImageIcon member notes

| Name	| Type | Description | Default |
|:--- | :--- | :--- | :--- |
|Source | ImageSource | Gets and sets the icon source file that will be used in the application. | Null |

# API Details

```c++
namespace Microsoft.UI.Xaml.Controls
{
    unsealed runtimeclass ImageIcon : Windows.UI.Xaml.Controls.IconElement
    {
        ImageIcon();

        Windows.UI.Xaml.Media.ImageSource Source{ get; set; };

        static Windows.UI.Xaml.DependencyProperty SourceProperty{ get; };
    }
}
```

# Appendix

## Scope
|Capability	| Priority |
| Icon can be used anywhere a BitmapIcon is accepted today | Must|
| PNG, JPEG, and SVG images are types the control will accept | Must|
| Icon must respond to user theming changes (Light, Dark, and High Contrast) | Could |
| ICO icons will be accepted by the control | Could |
| SVG image support will be improved | Won't |


## Accessibility

Since ImageIcon does not respond to the foreground property inherited from IconElement,
it is up to the application to handle the icon color changes when the user’s theme is changed.  

## Open Questions

- Should ICOs be supported in the future? 
- Should he ShowAsMonochrome property be added in the future? 
This is somehing that was added because of a design requirement back when BitmapIcon was first designed but it is no longer a hard design requirement. 



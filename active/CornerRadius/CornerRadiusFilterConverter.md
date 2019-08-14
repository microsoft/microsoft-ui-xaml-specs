# Background

Xaml controls can have rounded corners, usually controlled by setting the CornerRadius property on [Control](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Control.CornerRadius) or [Border](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Border.CornerRadius). The [CornerRadius](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.CornerRadius) type itself is a struct with fields TopLeft, TopRight, BottomLeft, and BottomRight.

Typically, a control's ControlTemplate binds the control's CornerRadius to an item in the template, for example this sets the CornerRadius to (5,5,5,5):

```
<Button Background="Gray" Padding="5" CornerRadius="5">
    Click
    <Button.Template>
        <ControlTemplate TargetType="Button">
            <Border Background="{TemplateBinding Background}"
                    CornerRadius="{TemplateBinding CornerRadius}"
                    Padding="{TemplateBinding Padding}">
                <ContentPresenter />
            </Border>
        </ControlTemplate>
    </Button.Template>
</Button>
```

But there are cases where the ControlTemplate needs to get only some of the components out of the CornerRadius struct, for example the top left and right corners, in order to have a rounded top and a square bottom. There's no way to create a TemplateBinding to a struct field, so you can't bind to CornerRadius.TopLeft.

For example when CommandBarFlyout opens up its secondary menu, primary and secondary menus are clipped together we need to clear rounded corners on bottom of primary menu and top of secondary menu to close the gaps. The following shows circles on the rounded corners and squares on the squared corners:

![CommandBarFlyout CornerRadius example](images/commandbarflyout-cornerradius-example.png)

The solution in this spec is a stock IValueConverter that can be used with the TemplateBinding to extract fields out of the struct.

This doesn't help, however, when there's no TemplateBinding on which to set it. That happens with the 
[AutoSuggestBox](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.AutoSuggestBox)
and [ComboBox](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ComboBox)
controls. The solution to that in this spec is a couple of properties that triggers code to square off the properties. This isn't an elegant solution, but it's what we can do from an out-of-band component to modify the behavior of an in-box component.

# Description

## CornerRadiusFilterConverter class
Use the CornerRadiusFilterConverter with a Binding or TemplateBinding to create a new CornerRadius struct from an existing one, extracting only some of the fields, leaving the others zero.

## AutoSuggestBoxHelper/ComboBoxHelper KeepInteriorCornersSquare property
Set this property on an AutoSuggestBox/ComboBox to keep the interior corners square, even if the CornerRadius property is non zero.


# Examples

## CornerRadiusFilterConverter example
A ControlTemplate for a Button that has square bottom corners, and top corners that can be set by the Button.CornerRadius property.

```XAML
<ControlTemplate TargetType="Button">
    <Grid>
        <Grid.Resources>
            <CornerRadiusFilterConverter Filter="Top" x:Name="TopCornersFilter" />
        </Grid.Resources>

        <Border Background="{TemplateBinding Background}"
                CornerRadius="{Binding  CornerRadius, 
                RelativeSource={RelativeSource TemplatedParent}, 
                Converter={StaticResource TopCornersFilter}}"
                Padding="{TemplateBinding Padding}">
            <ContentPresenter />
        </Border>
    </Grid>
</ControlTemplate>
```

## KeepInteriorCornersSquare example

Give a ComboBox rounded external corners, but keep the internal corners square.
```
<ComboBox ItemsSource="{x:Bind Options}"
          SelectedItem="{x:Bind SelectedOption, Mode=TwoWay}"
          CornerRadius="5"
          primitives:AutoSuggestBoxHelper.KeepInteriorCornersSquare="true" />
```


# API Details

## CornerRadiusFilterConverter
```
namespace Microsoft.UI.Xaml.Controls.Primitives
{
    [webhosthidden]
    [default_interface]
    runtimeclass CornerRadiusFilterConverter : Windows.UI.Xaml.Data.IValueConverter
    {
        CornerRadiusFilterConverter();
        CornerRadiusFilterKind Filter;
    };

    [webhosthidden]
    public enum CornerRadiusFilterKind
    {
        Top,
        Right,
        Left,
        Bottom
    };
}
```

## KeepInteriorCornersSquare

```
namespace Microsoft.UI.Xaml.Controls.Primitives
{
    [webhosthidden]
    [default_interface]
    runtimeclass AutoSuggestBoxHelper
    {
        static Windows.UI.Xaml.DependencyProperty KeepInteriorCornersSquareProperty{ get; };
        static void SetKeepInteriorCornersSquare(Windows.UI.Xaml.Controls.AutoSuggestBox element, Boolean value);
        static Boolean GetKeepInteriorCornersSquare(Windows.UI.Xaml.Controls.AutoSuggestBox element);
    };

    [webhosthidden]
    [default_interface]
    runtimeclass ComboBoxHelper
    {
        static Windows.UI.Xaml.DependencyProperty KeepInteriorCornersSquareProperty{ get; };
        static void SetKeepInteriorCornersSquare(Windows.UI.Xaml.Controls.ComboBox element, Boolean value);
        static Boolean GetKeepInteriorCornersSquare(Windows.UI.Xaml.Controls.ComboBox element);
    };

}
```


# Remarks

Currently the ConverterParameter only accepts `Top`, `Bottom`, `Left` and `Right`. We can add more specific directions like `TopLeft` and `TopRight` in the future if there's a need.

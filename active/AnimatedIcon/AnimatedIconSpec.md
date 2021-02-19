
AnimatedIcon
===

# Background

XAML has a couple of base types for showing an icon, aside from simplying showing a small image:
* [IconElement](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.IconElement)
* [IconSource](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.IconSource)

The duplication is cause for confusion but trying to solve that is outside the scope of this spec.
The ultimate difference between the two is that some controls require an IconElement and some
require an IconSource.
The new animated icon APIs here are continuing to use the existing pattern.

IconElement & IconSource have the same subclasses for different kinds of icons, for example
[BitmapIcon](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.BitmapIcon)
and [BitmapIconSource](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.BitmapIconSource).
This spec as adding an `AnimatedIcon` and `AnimatedIconSource`.

AnimatedIcon (and AnimatedIconSource) displays an animation define by a Lottie animation,
similar to the existing
[AnimatedVisualPlayer](https://docs.microsoft.com/uwp/api/Microsoft.UI.Xaml.Controls.AnimatedVisualPlayer)
element.

# AnimatedIcon How-To

## Creating animated content for an AnimatedIcon with Lottie

Defining an animation for an AnimatedIcon[Source] begins the same as the process to define an animation for an
[AnimatedVisualPlayer](http://msdn.microsoft.com/library/Microsoft.UI.Xaml.Controls.AnimatedVisualPlayer).
You will need to download the Lottie file for the icon you are looking to add and follow the steps in
[this]((https://docs.microsoft.com/en-us/windows/communitytoolkit/animations/lottie-scenarios/getting_started_codegen))
tutorial to run that file through LottieGen.
LottieGen generates code for a (WinRT) class that you can then instantiate and use with an AnimatedIcon.

For example, the XAML
[AutoSuggestBox](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.AutoSuggestBox)
control uses the ?? class, which was generated using the LottieGen tool.

The additional step for AnimatedIcon is to define markers in the animation definition, which mark positions within the animation.
You can then set the `State` of the AnimatedIcon to these markers.
For example, if you have a position in the Lottie file marked "PointerOver", you can set AnimatedIcon State to
"PointerOver" to move the animation to that position.

## Naming AnimatedIcon markers

If you set the State of an AnimatedIcon to a new value, you can have a position in the Lottie
animation for that state, or specificially for the transition from the old state to the new state.
The positions are identified with markers in the Lottie file.
You can also define specific markers for the start of the transition or the end of the transition.

For example, XAML's AutoSuggestBox control uses an AnimatedIcon that animates with the following states:
* Normal
* PointerOver
* Pressed

?? Show snippet from control template

You can define markers in the Lottie file with the State names.
You can also define markers as follows:
* Combine state names with a "To". For example, in the AutoSuggestBox case, "NormalToPointerOver".
Changing the AnimatedIcon's state from "Normal" to "PointerOver" will cause it to move to this marker's position.
* Append "_Start" and/or "_End" to a marker name.
For example defining markers "NormalToPointerOver_Start" and "NormalToPointerOver_End" will and changing
the AnimatedIcon's State from "Normal" to "PointerOver" will cause it to animate from the _Start
marker's position to the _End position.

The exact algorithm used to map AnimatedIcon State changes to marker positions:
1) Check the provided file's markers for the markers "[PreviousState]To[NewState]_Start" and "[PreviousState]To[NewState]_End".
If both are found play the animation from "[PreviousState]To[NewState]_Start" to "[PreviousState]To[NewState]_End".
2) If "[PreviousState]To[NewState]_Start" is not found but "[PreviousState]To[NewState]_End" is found,
then we will hard cut to the "[PreviousState]To[NewState]_End" position.
3) If "[PreviousState]To[NewState]_End" is not found but "[PreviousState]To[NewState]_Start" is found,
then we will hard cut to the "[PreviousState]To[NewState]_Start" position.
4) Check if the provided IAnimatedVisualSource2's markers for the marker "[PreviousState]To[NewState]".
If it is found then we will hard cut to the "[PreviousState]To[NewState]" position.
5) Check if the provided IAnimatedVisualSource2's markers for the marker "[NewState]".
If it is found, then we will hard cut to the "[NewState]" position.
6) Check if the provided IAnimatedVisualSource2's markers has any marker which ends with "To[NewState]_End".
If any marker is found which has that ending, we will hard cut to the first marker found with the appropriate ending's position.
7) Check if "[NewState]" parses to a float. If it does, animated from the current position to the parsed float. **
Hard cut to position 0.0.


Details of the marker format in Lottie JSON files are describe in detail [??](??).
The following is an example:

```json
"markers":[{"tm":0,"cm":"NormalToPointerOver_Start","dr":0},{"tm":9,"cm":"NormalToPointerOver_End","dr":0}, 

{"tm":10,"cm":"NormalToPressed_Start","dr":0},{"tm":19,"cm":"NormalToPressed_End","dr":0}, 

{"tm":20,"cm":"PointerOverToNormal_Start","dr":0},{"tm":29,"cm":"PointerOverToNormal_End","dr":0}, 

{"tm":30,"cm":"PointerOverToPressed_Start","dr":0},{"tm":39,"cm":"PointerOverToPressed_End","dr":0}, 

{"tm":40,"cm":"PressedToNormal_Start","dr":0},{"tm":49,"cm":"PressedToNormal_End","dr":0}, 

{"tm":50,"cm":"PressedToPointerOver_Start","dr":0},{"tm":69,"cm":"PressedToPointerOver_End","dr":0}, 

{"tm":90,"cm":"PressedToNormal_Start","dr":0},{"tm":99,"cm":"PressedToNormal_End","dr":0}, 

{"tm":100,"cm":"PressedToPointerOver_Start","dr":0},{"tm":101,"cm":"PressedToPointerOver_End","dr":0}]
```


# API Pages

_Each of the following L2 sections correspond to a page that will be on docs.microsoft.com_

## AnimatedIcon class

An icon that displays and controls an IAnimatedVisual2. One way to define an IAnimatedVisual2
is using the [Lottie-Windows](https://docs.microsoft.com/en-us/windows/communitytoolkit/animations/lottie) library.

You change the position of the animation displayed by an AnimatedIcon by setting its `State` attached property,
on the AnimatedIcon or on its parent in the XAML tree. (If you add an AnimatedIcon to the tree and the
property is already set on the new parent, the icon will move to that state.)

Using this mechanism you can add an AnimatedIcon to a XAML control's ControlTemplate, and several
XAML controls have this support already built in.

### Is this the right control? 

Use an AnimatedIcon control when you want to tie an animated icon to a user interaction in your UI.
Do not use an AnimatedIcon control if you want to just play an animation once or control the playback of the animation.
Do not use an AnimatedIcon control if you want to play your animation in a control that does not support IconElement.
Instead, use the `AnimatedVisualPlayer` control. 

An `AnimatedIcon` control can be used anywhere an IconElement can be used in WinUI. 

### How is AnimatedIcon different than [AnimatedVisualPlayer](https://docs.microsoft.com/uwp/api/Microsoft.UI.Xaml.Controls.AnimatedVisualPlayer)?

AnimatedIcon is an
[IconElement](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.IconElement),
and so can be used anywhere an element or IconElement is required, such as
[NavigationViewItem.Icon](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.NavigationViewItem.Icon).
`AnimatedVisualPlayer` is a more  general animation player that can be used anywhere in an application where AnimatedIcon is intended to
only be used where icons are used in WinUI.

### Example

In this example, we will add an AnimatedIcon to a UI page where the icon will respond to a download button being pressed.
The button control has the Normal, PointerOver, and Pressed states in it's default template.
The animation file being used in the AnimatedIcon already has the correct markers for the Button control based off of the states previously mentioned. 

XAML
```xml
<Grid>
    <Button x:Name="DownloadButton" Content="Download" OnClick="OnButtonClick"/>
    <muxc:AnimatedIcon x:Name="AnimatedSpinningWheel" AutomationProperties.AccessibilityView="Raw"  muxc:AnimatedIcon.State="Normal">
        <muxc:AnimatedIcon.Source>
            <AnimatedVisuals:DownloadAnimation/>
        </muxc:AnimatedIcon.Source>
        <muxc:AnimatedIcon.FallbackIconSource>
            <muxc:FontIconSource
                FontSize="8"
                FontFamily="{ThemeResource SymbolThemeFontFamily}"
                Foreground="{ThemeResource ButtonForegroundPressed}"
                Glyph="&#xE96E;"
                IsTextScaleFactorEnabled="False"/>
        </muxc:AnimatedIcon.FallbackIconSource>
    </muxc:AnimatedIcon>
</Grid>

```

C# 
```csharp

void CurrentStateChanged(object sender, RoutedEventArgs e)
{
    if (sender.Control = UIButton)
    {
        AnimatedSpinningWheel.State = sender.NewState.Name;
    }
    
}
```

## AnimatedIcon.State attached property

```c#
static public void SetState(UIElement element, string value);
static public string GetState(UIElement element);
```

Use the State attached property to change the state of an AnimatedIcon.
This property can be set on the parent of an AnimatedIcon, or on the
AniumatedIcon itself.

This example changes the State of the AnimatedIcon based on the pointer being over it.

```xaml
<AnimatedIcon PointerEntered="HandlePointerEntered" PointerExited="HandlePointerEited"
        State="Normal" />
```

```cs
private void Button_PointerEntered(object sender, PointerRoutedEventArgs e)
{
    AnimatedIcon.SetState(this.StackPanel1, "PointerOver");
}

private void Button_PointerExited(object sender, PointerRoutedEventArgs e)
{
    AnimatedIcon.SetState(this.StackPanel1, "Normal");
}

```

### Remarks

When you set the AnimatedIcon.State property on an element this is what happens: 
* The setter looks to see if the element that the property was just set on is an AnimatedIcon, if it is we finish. 
* If it is not, the setter checks to see if the first child of that element is an AnimatedIcon, if it is not, we finish. 
* If it is, the setter also sets the AnimatedIcon.State property on that child AnimatedIcon. 

If you add an AnimatedIcon to a XAML control, the icon's parent in the tree might not be 
obvious, for example the parent of the following AnimatedIcon is not the Button, because the Button
is inserting it into a tree defined by the Button's control template:

```xaml
<Button>
    <AnimatedIcon ... />
</Button>
```

But non-control types are more evident, for example a panel:

```xaml
<Button>
    <StackPanel Orientation='Horizontal' x:Name='StackPanel1'>
        <AnimatedIcon ... />
        <TextBlock Text='Click' />
    </StackPanel>
</Button>
```

## Other AnimatedIcon class members

| Name | Description | Default |
| :---| :---| :---|
| Source | Animation data, for example generated by the LottieGen tool. | null |
| FallbackSource | Gets or sets the static icon that will be used as a fallback when the OS cannot run the animated icon file.   | null |
| State | Property that the developer sets on AnimatedIcon. See below for more details. | null |
| Markers |	A dictionary (<string, double>) of all marker strings and their associated values found in the icon’s JSON file. The strings and values are based on what the designer names the markers in the Lottie After Effects file.  |	null |
| SetColorProperty | Takes a string and Windows.UI.Color value to set the color of a themed color property in the Lottie file.  |
| TryCreateAnimatedVisualSource | Method to get an IAnimatedVisual from an IAnimatedVisualSource2


## IconSource.CreateIconElement

```cs
public IconElement CreateIconElement();
```

Call the IconSource.CreateIconElement method to get an IconElement from and IconSource.
An IconElement can be used by controls that have an IconElement property,
or since it's an element it can be used anywhere in the XAML element tree.

This example creates a BitmapIcon from a BitmapIconSource:

```cs
IconSource iconSource = new BitmapIconSource();
BitmapIcon = iconSource.CreateIconElement as BitmapIcon();
```

## IconSource.CreateIconElementCore

```cs
protected virtual IconElement CreateIconElementCore();
```

Override this method in a subclass of IconSource to produce an IconElement
that corresponds to your IconSource.

## NavigationViewItem.Icon

_Spec note: this isn't a new property, but has new behavior_

If you set an AnimatedIcon as the value of the Icon property, 
the NavigationViewItem will set the states of the AnimatedIcon for you, according to the states of the control.

For example, this sets a custom animation `PlayIcon` that was generated by the LottieGen tool:

```xml
<muxc:NavigationView.MenuItems>
    <muxc:NavigationViewItem Content = "Play">
        <muxc:NavigationViewItem.Icon>
            <muxc:AnimatedIcon >
                <muxc:AnimatedIcon.Source>
                    <local:PlayIcon />
                </muxc:AnimatedIcon.Source>
            </muxc:AnimatedIcon>
    </muxc:NavigationViewItem.Icon>
</muxc:NavigationView.MenuItems>
```

The NavigationViewItem will automatically set the following states on the AnimatedIcon:
* Normal
* PointerOver
* Pressed
* Selected
* PressedSelected
* PointerOverSelected

If the PlayIcon has a marker for "NormalToPointerOver", it will be animated
to when the mouse moves to over the NavigationViewItem.

To create this custom PlayIcon class, you run a Lottie JSON file (with markers) through the Windows
[CodeGen](https://docs.microsoft.com/en-us/windows/communitytoolkit/animations/lottie-scenarios/getting_started_codegen)
tool to generate the animation code as a C# class.

Once you run your Lottifile through codegen you can add the output class to your project. 
Follow the steps in the
[CodeGen](https://docs.microsoft.com/en-us/windows/communitytoolkit/animations/lottie-scenarios/getting_started_codegen)
tutorial for more instructions. 

## IAnimatedVisualSource2 interface

Provides a Composition `Visual` that can be animated.

`IAnimatedVisualSource2` is an `IAnimatedVisualSource`, so has the
[TryCreateAnimatedVisual](https://docs.microsoft.com/uwp/api/Microsoft.UI.Xaml.Controls.IAnimatedVisualSource.TryCreateAnimatedVisual),
which can be called to retrieve an
[IAnimatedVisual](https://docs.microsoft.com/uwp/api/Microsoft.UI.Xaml.Controls.IAnimatedVisual).
`IAnimatedVisual` can then be used to get a Composition
[Visual](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.Visual),
which can be added to a Xaml element tree using the methods of
[ElementCompositionPreview](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Hosting.ElementCompositionPreview).

`IAnimatedVisualSource2` also has a `SetColorProperty` method that can be used to set a color
of the animated visual, and a `Markers` property that can be used to find named positions
in the animation timeline.

`IAnimatedVisualSource2` does not derive from `IAnimatedVisualSource` because it does not have the diagnostics item. The developer does not need to have `IAnmiatedVisualSource` to use AnimatedIcon. 

```cpp
void AddVisualAndShowStartAnimation(
    const winrt::Border& element,
    const winrt::IAnimatedVisualSource2 source,
    const winrt::hstring& initialState,
    const winrt::hstring& steadyState,
    const winrt::Color& themeColor )
{
    winrt::IAnimatedVisual animatedVisual = source.TryCreateAnimatedIconVisual();

    // Find the positions in the animation of the two states
    auto const markers = source.Markers())
    auto const fromProgress = static_cast<float>(markers.Lookup(initialState));
    auto const toProgress = static_cast<float>(markers.Lookup(steadyState));

    // Set the theme color as the animated visual's foreground.
    source.SetColorProperty("Foreground", themeColor);

    // Helper that uses TryCreateAnimatedVisual to add the source to the XAML element tree.
    // See IAnimatedVisualSource.TryCreateAnimatedVisual for more information.
    AddVisualToElement(element, animatedVisual);

    // Helper to play an animation.
    // See IAnimatedVisual for more information.
    PlaySegment(animatedVisual.RootVisual(), fromProgress, toProgress);
}
```

## Other IAnimatedVisualSource2 interface members

| Name | Description |
| :-| :-|
| Markers | Provides a mapping from marker names to positions in the animation. The names are defined by the animation. |
| SetColorProperty | Sets a color for the animated visual. The property names are defined by the animation. |

## AnimatedAcceptVisualSource class

This class can be used to show an animation for an Accept operation.
This can be used as the Source of an AnimatedIcon, and is used by the XAML CheckBox control.

This class supports markers that can be driven by an AnimatedIcon.State:
* NormalOnToPointerOverOn
* NormalOnToPressedOn
* NormalOffToNormalOn
* NormalOffToPointerOverOff
* NormalOffToPressedOff
* PointerOverOnToPointerOverOff
* PointerOverOnToNormalOn
* PointerOverOnToPressedOn
* PointerOverOffToPointerOverOn
* PointerOverOffToNormalOff
* PointerOverOffToPressedOff
* PressedOnToPressedOff
* PressedOnToPointerOverOff
* PressedOnToNormalOff_Start
* PressedOffToPressedOn
* PressedOffToPointerOver
* PressedOffToNormalOn

## AnimatedGlobalNavigationButtonVisualSource class

This class can be used to show an animation for a Navigation operation.
This can be used as the Source of an AnimatedIcon, and is used by the XAML NavigationView control.

This class supports markers that can be driven by an AnimatedIcon.State:
* ??

## AnimatedBackVisualSource class

This class can be used to show an animation for a Navigate-Back operation.
This can be used as the Source of an AnimatedIcon, and is used by the XAML NavigationView control.

This class supports markers that can be driven by an AnimatedIcon.State:
* ??


## AnimatedSettingsVisualSource class

This class can be used to show an animation for a Settings icon.
This can be used as the Source of an AnimatedIcon, and is used by the XAML NavigationView control.

This class supports markers that can be driven by an AnimatedIcon.State:
* ??

## AnimatedFindVisualSource class

This class can be used to show an animation for a Find icon.
This can be used as the Source of an AnimatedIcon, and is used by the XAML AutoSuggestBox control.

## AnimatedChevronDownSmallVisualSource class

This class can be used to show an animation for button that opens a flyout on another button.
This can be used as the Source of an AnimatedIcon, and is used by the XAML DropDownButton and Expander controls.

This class supports markers that can be driven by an AnimatedIcon.State:
* ??

# AnimatedChevronUpDownSmallVisualSource

This class can be used to show an animation for button that opens a flyout on another button.
This can be used as the Source of an AnimatedIcon, and is used by the XAML NavigationView control.

This class supports markers that can be driven by an AnimatedIcon.State:
* ??

# AnimatedChevronRightDownSmallVisualSource class

This class can be used to show an animation for a button that opens a flyout to the right of another button.
This can be used as the Source of an AnimatedIcon, and is used by the XAML TreeView control.

This class supports markers that can be driven by an AnimatedIcon.State:
* ??


# API Details

## Microsoft.UI.Xaml.Controls

```c++
[webhosthidden]
interface IAnimatedVisualSource2
{
    IAnimatedVisual TryCreateAnimatedVisual(Windows.UI.Composition.Compositor compositor);

    Windows.Foundation.Collections.IMapView<String, Double> Markers { get; };
    void SetColorProperty(String propertyName, Windows.UI.Color value);
};

[webhosthidden]
unsealed runtimeclass AnimatedIcon : Windows.UI.Xaml.Controls.IconElement
{
    AnimatedIcon();

    IAnimatedVisualSource2 Source{ get; set; };
    IconSource FallbackSource {get; set; };

    static Windows.UI.Xaml.DependencyProperty State{ get; };
    static void SetState(Windows.UI.Xaml.DependencyObject object, String value); 
    static String GetState(Windows.UI.Xaml.DependencyObject object); 

    static Windows.UI.Xaml.DependencyProperty SourceProperty{ get; };
}

[webhosthidden]
unsealed runtimeclass AnimatedIconSource : IconSource
{
    AnimatedIconSource();

    IAnimatedVisualSource2 Source{ get; set; };
    IconSource FallbackIconSource{ get; set; };

    static Windows.UI.Xaml.DependencyProperty SourceProperty { get; };
    static Windows.UI.Xaml.DependencyProperty FallbackIconSourceProperty { get; };
}
```

## Microsoft.UI.Xaml.Controls.AnimatedVisuals

```cs
[webhosthidden]
runtimeclass AnimatedAcceptVisualSource
    : [default] Microsoft.UI.Xaml.Controls.IAnimatedVisualSource, Microsoft.UI.Xaml.Controls.IAnimatedVisualSource2
{
    AnimatedAcceptVisualSource();
};

[webhosthidden]
runtimeclass AnimatedGlobalNavigationButtonVisualSource
    : [default] Microsoft.UI.Xaml.Controls.IAnimatedVisualSource, Microsoft.UI.Xaml.Controls.IAnimatedVisualSource2
{
    AnimatedGlobalNavigationButtonVisualSource();
};

[webhosthidden]
runtimeclass AnimatedBackVisualSource
    : [default] Microsoft.UI.Xaml.Controls.IAnimatedVisualSource, Microsoft.UI.Xaml.Controls.IAnimatedVisualSource2
{
    AnimatedBackVisualSource();
};

[webhosthidden]
runtimeclass AnimatedSettingsVisualSource
    : [default] Microsoft.UI.Xaml.Controls.IAnimatedVisualSource, Microsoft.UI.Xaml.Controls.IAnimatedVisualSource2
{
    AnimatedSettingsVisualSource();
};

[webhosthidden]
runtimeclass AnimatedFindVisualSource
    : [default] Microsoft.UI.Xaml.Controls.IAnimatedVisualSource, Microsoft.UI.Xaml.Controls.IAnimatedVisualSource2
{
    AnimatedFindVisualSource();
};

[webhosthidden]
runtimeclass AnimatedChevronDownSmallVisualSource
    : [default] Microsoft.UI.Xaml.Controls.IAnimatedVisualSource, Microsoft.UI.Xaml.Controls.IAnimatedVisualSource2
{
    AnimatedChevronDownSmallVisualSource();
};

[webhosthidden]
runtimeclass AnimatedChevronUpDownSmallVisualSource
    : [default] Microsoft.UI.Xaml.Controls.IAnimatedVisualSource, Microsoft.UI.Xaml.Controls.IAnimatedVisualSource2
{
    AnimatedChevronUpDownSmallVisualSource();
};

[webhosthidden]
runtimeclass AnimatedChevronRightDownSmallVisualSource
    : [default] Microsoft.UI.Xaml.Controls.IAnimatedVisualSource, Microsoft.UI.Xaml.Controls.IAnimatedVisualSource2
{
    AnimatedChevronRightDownSmallVisualSource();
};

```

# Appendix

## Fallback for older operating systems

AnimatedIcon will provide an API for a developer to add a static icon of type IconSource. This will be used when the operating system cannot play the animation from the LottieGen output. Lottie animations are not supported on operating systems older than RS5.


## Accessibility

This control is going to be part of the Icon family of controls so the accessibility requirements will reflect what the other icon controls have. AnimatedIcon will need to ensure that the high contrast brushes propagate correctly if the user changes their settings to a high contrast theme.  

If the user turns off animations in theri system settings, the AnimatedIcons will not animate. 

## Adding an instance state property 

In a future version of this control, we would like to add an instanced state property to the control so Intellisense can inform the developer of the State property. Today, the parser cannot recognize a dependency property and does not have the ability to tell the difference between a dependency property and an instance property of the same name. There would need to be a fix to the parser in order for this to work. 

### Updates to IconSource to support AnimatedIconSource

Since some controls take an IconElement and others take an IconSource, we are going to add two methods (public CreateIconElement and 
protected CreateIconElementCore) to IconSource. This will help cover the two sccenarios below: 

1.	I’m a developer creating a new control and I want to expose an IconSource property where consumers of my control can set ‘any’ iconsource. This control developer should not have to worry about each type of IconSource like FontIconSource, BitmapIconSource, AnimatedIconSource etc. In this case the developer can just use IconSource and call IconSource.CreateIconElement to get the corresponding IconElement without knowing what specific IconSource/Element it is. 
2.	I’m a developer creating a new type of IconSource, say Animated2IconSource. I would have to override Animated2IconSource.CreateElementCore and return an Animated2IconElement so that my new IconSource will work anywhere an IconSource can be used.


## Open Question
* Should the interface be named IAnimatedVisualSource2 if it is not derived from IAnimatedVisualSource?
    * I am stuck on a better name for it. 

## Features for V2
* Adding support for Light/Dark/High Contrast theming. Today developers can add color properties to the Lottie file and update the theming maually but maybe there is something we can add to the control to make this easier? 
* Updating the deafult templates of more controls. One of the controls we could update is TabViewItem. 
* Add support for looping the animations. Possibly add an indeterminate and determinate amount of loops. 
* Exploring progress scenarios and if that is something that should be built off of this control or if this should be a separate control so it can have all of the accesibility benefits of the other progress controls. 


## Update my control template to support AnimatedIcon

You might want to update a control that already has a static icon in it's default template to an animated icon. 
This example will show how to update the DropDownButton (which uses a static chevron glyph today) to use an animated chevron glyph instead. 

In this scenario you would need to update the template to add the setters for the visual states defined above in this document for DropDownButton.

XAML
```xml
<VisualStateManager.VisualStateGroups>
    <VisualStateGroup x:Name="CommonStates">
        <VisualState x:Name="Normal">
            <VisualState.Setters>
                <Setter Target="AnimatedChevronIcon.(AnimatedIcon.State)" Value="Normal"/>
            </VisualState.Setters>
        </VisualState>
        <VisualState x:Name="PointerOver">
            <Storyboard>
                <ObjectAnimationUsingKeyFrames Storyboard.TargetName="RootGrid" Storyboard.TargetProperty="Background">
                    <DiscreteObjectKeyFrame KeyTime="0" Value="{ThemeResource ButtonBackgroundPointerOver}" />
                </ObjectAnimationUsingKeyFrames>
                <ObjectAnimationUsingKeyFrames Storyboard.TargetName="RootGrid" Storyboard.TargetProperty="BorderBrush">
                    <DiscreteObjectKeyFrame KeyTime="0" Value="{ThemeResource ButtonBorderBrushPointerOver}" />
                </ObjectAnimationUsingKeyFrames>
                <ObjectAnimationUsingKeyFrames Storyboard.TargetName="ContentPresenter" Storyboard.TargetProperty="Foreground">
                    <DiscreteObjectKeyFrame KeyTime="0" Value="{ThemeResource ButtonForegroundPointerOver}" />
                </ObjectAnimationUsingKeyFrames>
            </Storyboard>
            <VisualState.Setters>
                <Setter Target="AnimatedChevronIcon.(AnimatedIcon.State)" Value="PointerOver"/>
            </VisualState.Setters>
        </VisualState>

        <VisualState x:Name="Pressed">
            <Storyboard>
                <ObjectAnimationUsingKeyFrames Storyboard.TargetName="RootGrid" Storyboard.TargetProperty="Background">
                    <DiscreteObjectKeyFrame KeyTime="0" Value="{ThemeResource ButtonBackgroundPressed}" />
                </ObjectAnimationUsingKeyFrames>
                <ObjectAnimationUsingKeyFrames Storyboard.TargetName="RootGrid" Storyboard.TargetProperty="BorderBrush">
                    <DiscreteObjectKeyFrame KeyTime="0" Value="{ThemeResource ButtonBorderBrushPressed}" />
                </ObjectAnimationUsingKeyFrames>
                <ObjectAnimationUsingKeyFrames Storyboard.TargetName="ContentPresenter" Storyboard.TargetProperty="Foreground">
                    <DiscreteObjectKeyFrame KeyTime="0" Value="{ThemeResource ButtonForegroundPressed}" />
                </ObjectAnimationUsingKeyFrames>
                <ObjectAnimationUsingKeyFrames Storyboard.TargetName="ChevronIcon" Storyboard.TargetProperty="Foreground">
                    <DiscreteObjectKeyFrame KeyTime="0" Value="{ThemeResource DropDownButtonForegroundSecondaryPressed}" />
                </ObjectAnimationUsingKeyFrames>
            </Storyboard>
            <VisualState.Setters>
                <Setter Target="AnimatedChevronIcon.(AnimatedIcon.State)" Value="Pressed"/>
            </VisualState.Setters>
        </VisualState>

        <VisualState x:Name="Disabled">
            <Storyboard>
                <ObjectAnimationUsingKeyFrames Storyboard.TargetName="RootGrid" Storyboard.TargetProperty="Background">
                    <DiscreteObjectKeyFrame KeyTime="0" Value="{ThemeResource ButtonBackgroundDisabled}" />
                </ObjectAnimationUsingKeyFrames>
                <ObjectAnimationUsingKeyFrames Storyboard.TargetName="RootGrid" Storyboard.TargetProperty="BorderBrush">
                    <DiscreteObjectKeyFrame KeyTime="0" Value="{ThemeResource ButtonBorderBrushDisabled}" />
                </ObjectAnimationUsingKeyFrames>
                <ObjectAnimationUsingKeyFrames Storyboard.TargetName="ContentPresenter" Storyboard.TargetProperty="Foreground">
                    <DiscreteObjectKeyFrame KeyTime="0" Value="{ThemeResource ButtonForegroundDisabled}" />
                </ObjectAnimationUsingKeyFrames>
                <ObjectAnimationUsingKeyFrames Storyboard.TargetName="ChevronIcon" Storyboard.TargetProperty="Foreground">
                    <DiscreteObjectKeyFrame KeyTime="0" Value="{ThemeResource ButtonForegroundDisabled}" />
                </ObjectAnimationUsingKeyFrames>
            </Storyboard>
            <VisualState.Setters>
                <!-- DisabledVisual Should be handled by the control, not the animated icon. -->
                <Setter Target="AnimatedChevronIcon.(AnimatedIcon.State)" Value="Normal"/>
            </VisualState.Setters>
        </VisualState>
    </VisualStateGroup>
</VisualStateManager.VisualStateGroups>
```

You will then need to replace the TextBlock control with the AnimatedIcon control. 

Before: 

XAML
```xml
<Grid.ColumnDefinitions>
    <ColumnDefinition Width="*"/>
    <ColumnDefinition Width="80"/>
</Grid.ColumnDefinitions>

<ContentPresenter x:Name="ContentPresenter"
    Content="{TemplateBinding Content}"
    ContentTransitions="{TemplateBinding ContentTransitions}"
    ContentTemplate="{TemplateBinding ContentTemplate}"
    HorizontalContentAlignment="{TemplateBinding HorizontalContentAlignment}"
    VerticalContentAlignment="{TemplateBinding VerticalContentAlignment}"
    AutomationProperties.AccessibilityView="Raw" />

<TextBlock
    x:Name="ChevronTextBlock"
    Grid.Column="1"
    FontFamily="{ThemeResource SymbolThemeFontFamily}"
    FontSize="8"
    Foreground="{ThemeResource ButtonForegroundPressed}"
    Text="&#xE96E;"
    VerticalAlignment="Center"
    Padding="2,4,2,0"
    Margin="8,0,0,0"
    IsTextScaleFactorEnabled="False"
    AutomationProperties.AccessibilityView="Raw"/>
</Grid>
```

After:

XAML
```xml
<Grid.ColumnDefinitions>
    <ColumnDefinition Width="*"/>
    <ColumnDefinition Width="80"/>
</Grid.ColumnDefinitions>

<ContentPresenter x:Name="ContentPresenter"
    Content="{TemplateBinding Content}"
    ContentTransitions="{TemplateBinding ContentTransitions}"
    ContentTemplate="{TemplateBinding ContentTemplate}"
    HorizontalContentAlignment="{TemplateBinding HorizontalContentAlignment}"
    VerticalContentAlignment="{TemplateBinding VerticalContentAlignment}"
    AutomationProperties.AccessibilityView="Raw" />

<local:AnimatedIcon x:Name="AnimatedChevronIcon" Grid.Column="1" Margin="10,4,2,0" AutomationProperties.AccessibilityView="Raw">
    <local:AnimatedIcon.Source>
        <AnimatedVisuals:AnimatedChevron/>
    </local:AnimatedIcon.Source>
    <local:AnimatedIcon.FallbackIconSource>
        <local:FontIconSource
            FontSize="8"
            FontFamily="{ThemeResource SymbolThemeFontFamily}"
            Foreground="{ThemeResource ButtonForegroundPressed}"
            Glyph="&#xE96E;"
            IsTextScaleFactorEnabled="False"/>
    </local:AnimatedIcon.FallbackIconSource>
</local:AnimatedIcon>
</Grid>
```

Here is the full file update for DropDownButton. 

XAML

```xml
<Style x:Key="DefaultDropDownButtonStyle" TargetType="local:DropDownButton">
        <Setter Property="Background" Value="{ThemeResource ButtonBackground}" />
        <Setter Property="Foreground" Value="{ThemeResource ButtonForeground}" />
        <Setter Property="BorderBrush" Value="{ThemeResource ButtonBorderBrush}" />
        <Setter Property="BorderThickness" Value="{ThemeResource ButtonBorderThemeThickness}" />
        <Setter Property="Padding" Value="{StaticResource ButtonPadding}" />
        <Setter Property="HorizontalAlignment" Value="Left" />
        <Setter Property="VerticalAlignment" Value="Center" />
        <Setter Property="FontFamily" Value="{ThemeResource ContentControlThemeFontFamily}" />
        <Setter Property="FontWeight" Value="Normal" />
        <Setter Property="FontSize" Value="{ThemeResource ControlContentThemeFontSize}" />
        <Setter Property="UseSystemFocusVisuals" Value="{StaticResource UseSystemFocusVisuals}" />
        <Setter Property="FocusVisualMargin" Value="-3" />
        <contract7Present:Setter Property="CornerRadius" Value="{ThemeResource ControlCornerRadius}" />
        <contract7Present:Setter Property="BackgroundSizing" Value="InnerBorderEdge" />
        <Setter Property="Template">
            <Setter.Value>
                <ControlTemplate TargetType="Button">
                    <Grid x:Name="RootGrid"
                        Background="{TemplateBinding Background}"
                        Padding="{TemplateBinding Padding}"
                        BorderBrush="{TemplateBinding BorderBrush}"
                        BorderThickness="{TemplateBinding BorderThickness}"
                        contract7Present:CornerRadius="{TemplateBinding CornerRadius}"
                        contract7NotPresent:CornerRadius="{ThemeResource ControlCornerRadius}"
                        contract7Present:BackgroundSizing="{TemplateBinding BackgroundSizing}">

                        <VisualStateManager.VisualStateGroups>
                            <VisualStateGroup x:Name="CommonStates">
                                <VisualState x:Name="Normal"/>
                                <VisualState x:Name="PointerOver">
                                    <Storyboard>
                                        <ObjectAnimationUsingKeyFrames Storyboard.TargetName="RootGrid" Storyboard.TargetProperty="Background">
                                            <DiscreteObjectKeyFrame KeyTime="0" Value="{ThemeResource ButtonBackgroundPointerOver}" />
                                        </ObjectAnimationUsingKeyFrames>
                                        <ObjectAnimationUsingKeyFrames Storyboard.TargetName="RootGrid" Storyboard.TargetProperty="BorderBrush">
                                            <DiscreteObjectKeyFrame KeyTime="0" Value="{ThemeResource ButtonBorderBrushPointerOver}" />
                                        </ObjectAnimationUsingKeyFrames>
                                        <ObjectAnimationUsingKeyFrames Storyboard.TargetName="ContentPresenter" Storyboard.TargetProperty="Foreground">
                                            <DiscreteObjectKeyFrame KeyTime="0" Value="{ThemeResource ButtonForegroundPointerOver}" />
                                        </ObjectAnimationUsingKeyFrames>
                                    </Storyboard>
                                    <VisualState.Setters>
                                        <Setter Target="AnimatedChevronIcon.(AnimatedIcon.State)" Value="PointerOver"/>
                                    </VisualState.Setters>
                                </VisualState>

                                <VisualState x:Name="Pressed">
                                    <Storyboard>
                                        <ObjectAnimationUsingKeyFrames Storyboard.TargetName="RootGrid" Storyboard.TargetProperty="Background">
                                            <DiscreteObjectKeyFrame KeyTime="0" Value="{ThemeResource ButtonBackgroundPressed}" />
                                        </ObjectAnimationUsingKeyFrames>
                                        <ObjectAnimationUsingKeyFrames Storyboard.TargetName="RootGrid" Storyboard.TargetProperty="BorderBrush">
                                            <DiscreteObjectKeyFrame KeyTime="0" Value="{ThemeResource ButtonBorderBrushPressed}" />
                                        </ObjectAnimationUsingKeyFrames>
                                        <ObjectAnimationUsingKeyFrames Storyboard.TargetName="ContentPresenter" Storyboard.TargetProperty="Foreground">
                                            <DiscreteObjectKeyFrame KeyTime="0" Value="{ThemeResource ButtonForegroundPressed}" />
                                        </ObjectAnimationUsingKeyFrames>
                                        <ObjectAnimationUsingKeyFrames Storyboard.TargetName="ChevronIcon" Storyboard.TargetProperty="Foreground">
                                            <DiscreteObjectKeyFrame KeyTime="0" Value="{ThemeResource DropDownButtonForegroundSecondaryPressed}" />
                                        </ObjectAnimationUsingKeyFrames>
                                    </Storyboard>
                                    <VisualState.Setters>
                                        <Setter Target="AnimatedChevronIcon.(AnimatedIcon.State)" Value="Pressed"/>
                                    </VisualState.Setters>
                                </VisualState>

                                <VisualState x:Name="Disabled">
                                    <Storyboard>
                                        <ObjectAnimationUsingKeyFrames Storyboard.TargetName="RootGrid" Storyboard.TargetProperty="Background">
                                            <DiscreteObjectKeyFrame KeyTime="0" Value="{ThemeResource ButtonBackgroundDisabled}" />
                                        </ObjectAnimationUsingKeyFrames>
                                        <ObjectAnimationUsingKeyFrames Storyboard.TargetName="RootGrid" Storyboard.TargetProperty="BorderBrush">
                                            <DiscreteObjectKeyFrame KeyTime="0" Value="{ThemeResource ButtonBorderBrushDisabled}" />
                                        </ObjectAnimationUsingKeyFrames>
                                        <ObjectAnimationUsingKeyFrames Storyboard.TargetName="ContentPresenter" Storyboard.TargetProperty="Foreground">
                                            <DiscreteObjectKeyFrame KeyTime="0" Value="{ThemeResource ButtonForegroundDisabled}" />
                                        </ObjectAnimationUsingKeyFrames>
                                        <ObjectAnimationUsingKeyFrames Storyboard.TargetName="ChevronIcon" Storyboard.TargetProperty="Foreground">
                                            <DiscreteObjectKeyFrame KeyTime="0" Value="{ThemeResource ButtonForegroundDisabled}" />
                                        </ObjectAnimationUsingKeyFrames>
                                    </Storyboard>
                                    <VisualState.Setters>
                                        <!-- DisabledVisual Should be handled by the control, not the animated icon. -->
                                        <Setter Target="AnimatedChevronIcon.(AnimatedIcon.State)" Value="Normal"/>
                                    </VisualState.Setters>
                                </VisualState>
                            </VisualStateGroup>
                        </VisualStateManager.VisualStateGroups>

                        <Grid.ColumnDefinitions>
                            <ColumnDefinition Width="*"/>
                            <ColumnDefinition Width="80"/>
                        </Grid.ColumnDefinitions>

                        <ContentPresenter x:Name="ContentPresenter"
                            Content="{TemplateBinding Content}"
                            ContentTransitions="{TemplateBinding ContentTransitions}"
                            ContentTemplate="{TemplateBinding ContentTemplate}"
                            HorizontalContentAlignment="{TemplateBinding HorizontalContentAlignment}"
                            VerticalContentAlignment="{TemplateBinding VerticalContentAlignment}"
                            AutomationProperties.AccessibilityView="Raw" />

                        <local:AnimatedIcon x:Name="AnimatedChevronIcon" Grid.Column="1" Margin="10,4,2,0" AutomationProperties.AccessibilityView="Raw"  local:AnimatedIcon.State="Normal">
                            <local:AnimatedIcon.Source>
                                <AnimatedVisuals:Controls_02_UpDown_Dropdown/>
                            </local:AnimatedIcon.Source>
                            <local:AnimatedIcon.FallbackIconSource>
                                <local:FontIconSource
                                    FontSize="8"
                                    FontFamily="{ThemeResource SymbolThemeFontFamily}"
                                    Foreground="{ThemeResource ButtonForegroundPressed}"
                                    Glyph="&#xE96E;"
                                    IsTextScaleFactorEnabled="False"/>
                            </local:AnimatedIcon.FallbackIconSource>
                        </local:AnimatedIcon>
                    </Grid>
                </ControlTemplate>
            </Setter.Value>
        </Setter>
    </Style>

    <Style TargetType="local:DropDownButton" BasedOn="{StaticResource DefaultDropDownButtonStyle}" />
```

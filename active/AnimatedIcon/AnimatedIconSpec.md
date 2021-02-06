
# Animated Icon

## Background

Xaml has an
[IconElement](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.IconElement)
base type today with derived types like
[BitmapIcon](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.BitmapIcon)
and [SymbolIcon](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.SymbolIcon).
Since it's an element it can be used in the UI tree, such as in a 
[StackPanel](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.StackPanel).

All of these icons are static though, and developers do not have a straightforward way to
add an animated icon in controls where they can use a static icon.
There is also a desire to have an animated icon respond to user interactivity via state changes to either draw
attention to an action a user can take or to add user delight.

The [AnimatedVisualPlayer](https://docs.microsoft.com/uwp/api/Microsoft.UI.Xaml.Controls.AnimatedVisualPlayer)
element can be used to create animated (Lottie-based) icons,
but to have the animation be driven by the states of and interactions with a control,
developers may need to write a lot of custom code.

Also, AnimatedVisualPlayer does not inherit from IconElement so it cannot be used in places that
need one, like 
[NavigationViewItem.Icon](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.NavigationViewItem.Icon)
or in a DropDownButton.

This spec introduces an AnimatedIcon IconElement which reduces, or ideally eliminates, the amount of code-behind needed to
implement most scenarios, and it can be used in WinUI controls that support an IconElement.

Along with the various IconElement types, which are elements, there are IconSource types.
For example BitmapIcon and
[BitmapIconSource](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.BitmapIconSource).
Similarly in this spec, along with `AnimatedIcon` is an `AnimatedIconSource`.

# How to design animations for AnimatedIcon

## Creating animated content with Lottie 

You will need to download the Lottie file for the icon you are looking to add and follow the steps in [this]((https://docs.microsoft.com/en-us/windows/communitytoolkit/animations/lottie-scenarios/getting_started_codegen)) tutorial to run that file through LottieGen. The output of LottieGen will produce the file needed for AnimatedIcon. This output will include the markers needed for animated icon to map the correct state transitions to the correct animation segments. It will also contain the themable color properties you can set in code. 

## Design Requirements  

Designers will need to add markers named like the following to all Lottie files that will be used with controls that support AnimatedIcon. The controls that currently support AnimatedIcon are listed below. 

The marker names should reflect the visual state the animation is going from to the visual state the animation is
going to. For example, the bare minimum of the markers that are needed for icons being used in a NavigationViewItem are:  

* NormalToPointerOver 
* NormalToPressed 
* PointerOverToNormal 
* PointerOverToPressed 
* PressedToNormal 
* PressedToPointerOver  

“Normal” is the state where the user is not interacting with the NavigationViewIten. 

Markers will need to be named this specific way so AnimatedIcon can map the correct animation segment with the correct visual state transition. This solution is conforming to a standard set of strings that are already defined in the visual state manager for each state. Here is an example of how the markers would look in a Lottie JSON file for the NavigationViewItem.  

"markers":[{"tm":0,"cm":"NormalToPointerOver_Start","dr":0},{"tm":9,"cm":"NormalToPointerOver_End","dr":0}, 

{"tm":10,"cm":"NormalToPressed_Start","dr":0},{"tm":19,"cm":"NormalToPressed_End","dr":0}, 

{"tm":20,"cm":"PointerOverToNormal_Start","dr":0},{"tm":29,"cm":"PointerOverToNormal_End","dr":0}, 

{"tm":30,"cm":"PointerOverToPressed_Start","dr":0},{"tm":39,"cm":"PointerOverToPressed_End","dr":0}, 

{"tm":40,"cm":"PressedToNormal_Start","dr":0},{"tm":49,"cm":"PressedToNormal_End","dr":0}, 

{"tm":50,"cm":"PressedToPointerOver_Start","dr":0},{"tm":69,"cm":"PressedToPointerOver_End","dr":0}, 

{"tm":90,"cm":"PressedToNormal_Start","dr":0},{"tm":99,"cm":"PressedToNormal_End","dr":0}, 

{"tm":100,"cm":"PressedToPointerOver_Start","dr":0},{"tm":101,"cm":"PressedToPointerOver_End","dr":0}] 

# Controls that currently support AnimatedIcon

We updated the default templates for the following controls with the support for the default markers needed in a Lottie file to have an animation work in that particular control. This will give you the ability to add an animated icon with the correct markers in the file, to one of the controls without needing to do any more work. That is because the template for the control is where we determine which animation segment to play based on the visual state that the control is transitioning to. 

The list of controls are: 

* AutoSuggestBox
* CheckBox
* DropDownButton
* Expander
* SplitButton

Here are the default states for each of the controls:

* AutoSuggestBox 
    * Normal
    * PointerOver
    * Pressed
    * Disabled
* CheckBox 
    * UncheckedNormal
    * UncheckedPointerOver
    * UncheckedPressed
    * UncheckedDisabled
    * CheckedNormal
    * CheckedPointerOver
    * CheckedPressed
    * CheckedDisabled 
    * IndeterminateNormal
    * IndeterminatePointerOver
    * IndeterminatePressed
    * IndeterminateDisabled
* Expander 
    * Normal
    * PointerOver
    * Pressed
    * Disabled
* DropDownButton 
    * Normal
    * PointerOver
    * Pressed
    * Disabled
* SplitButton 
    * Normal
    * PointerOver
    * Pressed
    * Disabled


# API Pages

## AnimatedIcon class

An AnimatedIcon is a UI component that has APIs that enables the playing of animated images in response to user interaction and visual state changes. Some WinUI controls will call these APIs in response to visual state changes made by the user interacting with the control. 

On Windows, the VisualStateManager is used to manage the logic for transitioning between states for controls.
Each control has a specified VisualStateGroup, which contains mutually exclusive VisualState objects.
For example, a DropDownButton may have a VisualStateGroup called “CommonStates”, which specifies a VisualState object for
“Normal” “Pressed”, “PointerOver”, and “Disabled”.

A control will use the VisualStateManager to determine what state the animated icon is transitioning to and
play the corresponding animation segment of the icon.
The icon code will need to have markers named in a standard way (described below) so
AnimatedIcon can map the correct animation segment with the visual state the control is transitioning to.  

**Important APIs:** [AnimatedIcon class](https://docs.microsoft.com/uwp/api/microsoft.ui.xaml.controls.AnimatedIcon)

### Is this the right control? 

Use an AnimatedIcon control when you want to tie an animated icon to a user interaction in your UI.
Do not use an AnimatedIcon control if you want to just play an animation once or control the playback of the animation.
Do not use an AnimatedIcon control if you want to play your animation in a control that does not support IconElement.
Instead, use the AnimatedVisualPlayer control. 

An AnimatedIcon control can be used anywhere an IconElement can be used in WinUI. 

## How is this different than [AnimatedVisualPlayer](https://docs.microsoft.com/uwp/api/Microsoft.UI.Xaml.Controls.AnimatedVisualPlayer)?

AnimatedIcon is an
[IconElement](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.IconElement),
and so can be used anywhere an element or IconElement is required, such as
[NavigationViewItem.Icon](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.NavigationViewItem.Icon).
AnimatedVisualPlayer is a more  general animation player that can be used anywhere in an application where AnimatedIcon is intended to only be used where icons are used in WinUI. 

AVP allows developers to control animations using the following methods: Pause, PlayAsync, Resume, SetProgress, and Stop. When using AVP, developers may need to write a lot of code when getting their generated animation to properly respond to user interaction and state changes. This is the main distinction between the workflows for AVP and AnimatedIcon. An AnimatedIcon will be used when animations need to be driven by state changes and user interaction in a markup friendly way. It would reduce, or ideally eliminate the amount of code-behind needed to implement such scenarios.

# Examples

## Swap out a static icon with an animated icon in a Navigation View Item using the standard markers

NavigationViewItem has states for rest/pointer over/press and AnimatedIcon will
animate the state transitions by having named markers in the Lottie file such as:  

- NormalToPointerOver
- NormalToPressed
- PointerOverToNormal
- PointerOverToPressed
- PressedToNormal
- PressedToPointerOver

Below is an example of how to add an animated play icon with the standard state transitions mentioned above to
a Hierarchical NavigationView item.

AnimatedIcon requires you to run the Lottie JSON file through the Windows [CodeGen](https://docs.microsoft.com/en-us/windows/communitytoolkit/animations/lottie-scenarios/getting_started_codegen) tool to generate the animation code as a C# class. This process is similar to the process needed for AnimatedVisualPlayer. AnimatedIcon needs the JSON file to run through CodeGen so the markers and color properties are translated in the format needed for the control to map the values correctly. 

Once you run your Lottifile through codegen you can add the output class to your project. Follow the steps in the [CodeGen](https://docs.microsoft.com/en-us/windows/communitytoolkit/animations/lottie-scenarios/getting_started_codegen) tutorial for more instructions. 

Once the icon file is added to your project you can then add the icon to your navigation view item like you would any other icon type that inherits from IconElement. 

Before: 

XAML

```xml
< muxc:NavigationView.MenuItems >
    < muxc:NavigationViewItem Content = "Menu Item1" Icon =”Play” Tag="SamplePage" x:Name = "SamplePageItem" >
</ muxc:NavigationView.MenuItems >
```

After: 

XAML

```xml
< muxc:NavigationView.MenuItems >
    < muxc:NavigationViewItem Content = "Menu Item1" Tag="SamplePage" x:Name = "SamplePageItem" >
        < muxc:NavigationViewItem.Icon >
            < muxc:AnimatedIcon x:Name = "PlayAnimatedIcon" >
                < muxc:AnimatedIcon.Source >
                    < animatedvisuals:PlayIcon />
                </ muxc:AnimatedIcon.Source>
            </ muxc:AnimatedIcon >
    </ muxc:NavigationViewItem.Icon >
</ muxc:NavigationView.MenuItems >
```

# Add an animated icon to a control that has not been updated for AnimatedIcon 

If you would like to add an animated icon to a control that does not have the default template updated, you will need to update the template to include the state changes needed to play the animation segments. 

Here is the XAML for the changes you would need to make to add an AnimatedIcon to a button (button does not have the default template updated). This includes the changes you will need to make to the state transitions for Normal, PointerOver, Pressed, and Disabled. 

This example also shows you how to add the fallback static icon that will be used if the animated icon cannot be played. In this example we are using a SymbolIcon glyph. 

XAML
```xml
<Button Click="Button_Click"
        Background="LightGray"
        Height="100" Widht="80">
        <AnimatedIcon x:Name ="PlayIcon" >
            <AnimatedIcon.Source >
                <animatedvisuals:PlayIcon />
            </AnimatedIcon.Source>
            <AnimatedIcon.FallbackIconSource>
                <SymbolIconSource Symbol="Play"/>  
            </AnimatedIcon.FallbackIconSource>
        </AnimatedIcon >
        <Button.Style>
            <StyleTargetType="Button">
                <SetterProperty="Template">
                    <Setter.Value>
                        <ControlTemplateTargetType="Button">
                            <Grid>
                                <VisualStateManager.VisualStateGroups>
                                    <VisualStateGroupx:Name="CommonStates">
                                        <VisualStatex:Name="Normal">
                                            <VisualState.Setters>
                                                <SetterTarget="Icon.(AnimatedIcon.StateProperty)"Value="Normal"/>
                                            </VisualSTate.Setters>
                                        </VisualState>
                                        <VisualState x:Name="PointerOver">
                                            <VisualState.Setters>
                                                <SetterTarget="Icon.(AnimatedIcon.StateProperty)"Value="PointerOver"/>
                                            </VisualSTate.Setters>
                                        </VisualState>
                                        <VisualStatex:Name="Pressed">
                                            <VisualState.Setters>
                                                <SetterTarget="Icon.(AnimatedIcon.StateProperty)"Value="Pressed"/>
                                            </VisualSTate.Setters>
                                        </VisualState>
                                    <VisualStateGroupx:Name="DisabledState"/>
                                        <VisualStatex:Name="Enabled">
                                            <VisualState.Setters>
                                                <SetterTarget="Icon.(AnimatedIcon.StateProperty)"Value="Enabled"/>
                                            </VisualSTate.Setters>
                                        </VisualState>
                                        <VisualStatex:Name="Disabled">
                                            <VisualState.Setters>
                                                <SetterTarget="Icon(AnimatedIcon.StateProperty)"Value="Disabled"/>
                                            </VisualSTate.Setters>
                                        </VisualState>
                                    </VisualStateGroup>
                                </VisualStateManager.VisualStateGroups>
                                <ContentPresenter
                                    x:Name="ContentPresenter"
                                    Background="{​​​​​​​​​TemplateBinding Background}​​​​​​​​​"
                                    BorderBrush="{​​​​​​​​​TemplateBinding BorderBrush}​​​​​​​​​"
                                    BorderThickness="{​​​​​​​​​TemplateBinding BorderThickness}​​​​​​​​​"
                                    Content="{​​​​​​​​​TemplateBinding Content}​​​​​​​​​"
                                    ContentTemplate="{​​​​​​​​​TemplateBinding ContentTemplate}​​​​​​​​​"
                                    ContentTransitions="{​​​​​​​​​TemplateBinding ContentTransitions}​​​​​​​​​"
                                    Padding="{​​​​​​​​​TemplateBinding Padding}​​​​​​​​​"
                                    HorizontalContentAlignment="{​​​​​​​​​TemplateBinding HorizontalContentAlignment}​​​​​​​​​"
                                    VerticalContentAlignment="{​​​​​​​​​TemplateBinding VerticalContentAlignment}​​​​​​​​​"
                                    AutomationProperties.AccessibilityView="Raw" />
                                <Grid x:Name="Icon" HorizontalAlignment="Right" Content="{​​​​​​​​​TemplateBinding Icon}​​​​​​​​​/>
                            </Grid>
                        </ControlTemplate>
                    </Setter.Value>
                </Setter>
            </Style>
        </Button.Style>
</Button>
    
```

# Manually update the visual state of the animated icon

You can also manually update the visual state of AnimatedIcon if you would like a certain animation segment to play based off of another user interaction in your UI. In this snippet, the visual state of the animatied icon above is being updated based on a download button being clicked in the UI.

C#
```C#

public void OnDownloadButtonClick()
{
    string newState = "PointerOver";
    if (PlayIcon.GetState(PlayIcon) != newState)
    {
        PlayIcon.SetState(newState);
    }
}

```

## AnimatedIcon class member notes

| Name | Type |	Description | Default |
|:--- | :---| :---| :---|
| IAnimatedVisualSource2 | Inerface | Interface that LottieGen generates when the developer inputs a Lottie file to be used in their application.  | null |
| TryCreateAnimatedVisualSource | Method | Method to get an IAnimatedVisual from an IAnimatedVisualSource2 | null |
| Markers |	IMapView | Returns a dictionary (<string, double>) of all marker strings and their associated values found in the icon’s JSON file. The strings and values are based on what the designer names the markers in the Lottie After Effects file.  |	null |
| SetColorProperty |	Method	| Takes a string and Windows.UI.Color value to set the color of a themed color property in the Lottie file.  | null |
| StateProperty	| Dependency Property |Attached property that gets the current visual state the icon is animating from. | "Normal" |
| Source | IAnimatedIconSource |Gets or sets the icon visual produced from LottieGen. |	null |
| FallbackSource | IconSource | Gets or sets the static icon that will be used as a fallback when the OS cannot run the animated icon file.   | null |

# API Details

```c++
interface IAnimatedVisualSource2
{
    IAnimatedVisual TryCreateAnimatedVisual(Windows.UI.Composition.Compositor compositor);

    Windows.Foundation.Collections.IMapView<String, Double> Markers { get; };
    void SetColorProperty(String propertyName, Windows.UI.Color value);
};

unsealed runtimeclass AnimatedIcon : Windows.UI.Xaml.Controls.IconElement
 {
    AnimatedIcon();

    IAnimatedVisualSource2 Source{ get; set; };
    IconSource FallbackSource {get; set; };

    [MUX_DEFAULT_VALUE("Normal")]       
    static Windows.UI.Xaml.DependencyProperty StateProperty{ get; };
    static void SetState(Windows.UI.Xaml.DependencyObject object, String value); 
    static String GetState(Windows.UI.Xaml.DependencyObject object); 
    static Windows.UI.Xaml.DependencyProperty SourceProperty{ get; };
}

unsealed runtimeclass AnimatedIconSource : IconSource
{
    AnimatedIconSource();

    IAnimatedVisualSource2 Source{ get; set; };
    IconSource FallbackIconSource{ get; set; };

    static Windows.UI.Xaml.DependencyProperty SourceProperty { get; };
    static Windows.UI.Xaml.DependencyProperty FallbackIconSourceProperty { get; };
}

```

# Appendix

## Fallback for older operating systems

AnimatedIcon will provide an API for a developer to add a static icon of type IconSource. This will be used when the operating system cannot play the animation from the LottieGen output. Lottie animations are not supported on operating systems older than RS5.


## Accessibility

This control is going to be part of the Icon family of controls so the accessibility requirements will reflect what the other icon controls have. AnimatedIcon will need to ensure that the high contrast brushes propagate correctly if the user changes their settings to a high contrast theme.  

If the user turns off animations in theri system settings, the AnimatedIcons will not animate. 

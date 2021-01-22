
# Animated Icon

## Background
Today, developers do not have a straightforward way to add an animated icon in controls where they can use a static icon. Some examples of where a developer may want to add an animated icon are in a NavigationViewItem or in a Button. There is also a desire to have an animated icon respond to user interactivity via state changes to either draw attention to an action a user can take or to add user delight. 

In the current developer workflow, developers may need to write a lot of custom code to get their generated animation to properly respond to user interaction using the AnimatedVisualPlayer control. Also, AnimatedVisualPlayer does not inherit from IconElement so it cannot be used in places that need icons like Navigation View Item or in a button. An AnimatedIcon control would reduce, or ideally eliminate the amount of code-behind needed to implement most scenarios and it can be used in WinUI controls that support an IconElement control. 

## Description
An AnimatedIcon is a UI component which plays animated images in response to user interaction and visual state changes. An AnimatedIcon can be used anywhere a static icon control that inherits from IconElement is used today.  

On Windows, the VisualStateManager is used to manage the logic for transitioning between states for controls. Each control has a specified VisualStateGroup, which contains mutually exclusive VisualState objects. For example, a Button may have a VisualStateGroup called “CommonStates”, which specifies a VisualState object for “Normal” “Pressed”, “PointerOver”, and “Disabled”.

A control will use the VisualStateManager to determine what state the animated icon is transitioning to and play the corresponding animation segment of the icon. The icon code will need to have markers named in a standard way (described below) so AnimatedIcon can map the correct animation segment with the visual state the control is transitioning to.  

**Important APIs:** [AnimatedIcon class](https://docs.microsoft.com/uwp/api/microsoft.ui.xaml.controls.AnimatedIcon)

## Is this the right control? 
Use an AnimatedIcon control when you want to tie an animated icon to a user interaction in your UI. Do not use an AnimatedIcon control if you want to just play an animation once or control the playback of the animation. Do not use an AnimatedIcon control if you want to play your animation in a control that does not support IconElement. Instead, use the AnimatedVisualPlayer control. 

An AnimatedIcon control can be used anywhere an IconElement can be used in WinUI. 

# Remarks
## How is this different than Animated Visual Player?
AnimatedIcon inherits from IconElement to make it easier for developers to swap out a static icon for an animated icon that responds to visual state changes. AnimatedVisualPlayer (AVP) is meant to be a general animation player that can be used anywhere in an application where AnimatedIcon is intended to only be used where icons are used in WinUI. 

AVP allows developers to control animations using the following methods: Pause, PlayAsync, Resume, SetProgress, and Stop. When using AVP, developers may need to write a lot of code when getting their generated animation to properly respond to user interaction and state changes. This is the main distinction between the workflows for AVP and AnimatedIcon. An AnimatedIcon will be used when animations need to be driven by state changes and user interaction in a markup friendly way. It would reduce, or ideally eliminate the amount of code-behind needed to implement such scenarios.

## Pre-requisites for adding an animated icon to an application: 
1.	Download the Lottie file for the icon you are looking to add to your application.
2.	Run the Lottie file through LottieGen. The LottieGen will output the file that you will add to your project that will include the markers needed for animated icon to map the correct state transitions to the correct animation segments. It will also contain the themable color properties you can set in code. 
3.	Take the output file from LottieGen and place it in your project.

## Design Requirements  

Designers will need to add markers named like the following to all Lottie files that will be used with AnimatedIcon. The marker names should reflect the visual state the animation is going from to the visual state the animation is going to. For example, the bare minimum of the markers that are needed for icons being used in a button are:  

* NormalToPointerOver 
* NormalToPressed 
* PointerOverToNormal 
* PointerOverToPressed 
* PressedToNormal 
* PressedToPointerOver  

“Normal” is the state where the user is not interacting with the button. 

Markers will need to be named this specific way so AnimatedIcon can map the correct animation segment with the correct visual state transition. This solution is conforming to a standard set of strings that are already defined in the visual state manager for each state. Here is an example of how the markers would look in a Lottie JSON file for a button.  

"markers":[{"tm":0,"cm":"NormalToPointerOver_Start","dr":0},{"tm":9,"cm":"NormalToPointerOver_End","dr":0}, 

{"tm":10,"cm":"NormalToPressed_Start","dr":0},{"tm":19,"cm":"NormalToPressed_End","dr":0}, 

{"tm":20,"cm":"PointerOverToNormal_Start","dr":0},{"tm":29,"cm":"PointerOverToNormal_End","dr":0}, 

{"tm":30,"cm":"PointerOverToPressed_Start","dr":0},{"tm":39,"cm":"PointerOverToPressed_End","dr":0}, 

{"tm":40,"cm":"PressedToNormal_Start","dr":0},{"tm":49,"cm":"PressedToNormal_End","dr":0}, 

{"tm":50,"cm":"PressedToPointerOver_Start","dr":0},{"tm":69,"cm":"PressedToPointerOver_End","dr":0}, 

{"tm":90,"cm":"PressedToNormal_Start","dr":0},{"tm":99,"cm":"PressedToNormal_End","dr":0}, 

{"tm":100,"cm":"PressedToPointerOver_Start","dr":0},{"tm":101,"cm":"PressedToPointerOver_End","dr":0}] 

# Examples

### Scenario: App developer wants to swap out a static icon with an animated icon in a Navigation View Item using the standard markers
An icon animation will be activated by changes of state in XAML input controls (e.g. Button, and ToggleSwitch). For example, NavigationViewItem has states for rest/pointer over/press and AnimatedIcon will animate the state transitions by having named markers in the Lottie file such as:  

- NormalToPointerOver
- NormalToPressed
- PointerOverToNormal
- PointerOverToPressed
- PressedToNormal
- PressedToPointerOver

Below is an example of how to add an animated play icon with the standard state transitions mentioned above to a Hierarchical NavigationView item. 

Follow the pre-requisute steps to create your icon file and add it to your project. Then, add the icon to your navigation view item like you would any other icon type that inherits from IconElement. 

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
            < controls:AnimatedIcon x:Name = "PlayAnimatedIcon" >
                < controls:AnimatedIcon.Source >
                    < local:PlayIcon />
                </ controls:AnimatedIcon.Source>
            </ controls:AnimatedIcon >
    </ muxc:NavigationViewItem.Icon >
</ muxc:NavigationView.MenuItems >
```

### Scenario: App developer wants to swap out a static icon with an animated icon in a Navigation View Item using marker names that are not standard
If you have marker names that are not the standard names above, you must go in to the template of the NavigationView and provide the values of the markers you are transitioning to when you enter a visual state. For example, say that these are the markers in my JSON file and the designer named PointerOver state Hover: 

"markers":[{"tm":0,"cm":"NormalToHover_Start","dr":0},{"tm":9,"cm":"NormalToHover_End","dr":0}, 

{"tm":10,"cm":"NormalToPressed_Start","dr":0},{"tm":19,"cm":"NormalToPressed_End","dr":0}, 

{"tm":20,"cm":"HoverToNormal_Start","dr":0},{"tm":29,"cm":"HoverToNormal_End","dr":0}, 

{"tm":30,"cm":"HoverToPressed_Start","dr":0},{"tm":39,"cm":"HoverToPressed_End","dr":0}, 

{"tm":40,"cm":"PressedToNormal_Start","dr":0},{"tm":49,"cm":"PressedToNormal_End","dr":0}, 

{"tm":50,"cm":"PressedToHover_Start","dr":0},{"tm":69,"cm":"PressedToHover_End","dr":0}, 

{"tm":90,"cm":"PressedToNormal_Start","dr":0},{"tm":99,"cm":"PressedToNormal_End","dr":0}, 

{"tm":100,"cm":"PressedToHover_Start","dr":0},{"tm":101,"cm":"PressedToHover_End","dr":0}] 

You would then update the template for the NavigationView to set the state property to look for the "Hover" marker names when the visual state is PointerOver:

XAML
```xml
< VisualStateManager.VisualStateGroups >
    < VisualStateGroup x: Name =”CommonStates”>
        < VisualState x: Name =”Normal”> 
        </ VisualState >       
        < VisualState x: Name =”PointerOver”>           
            < VisualState.Setters >            
                < Setter Target = "Icon.(AnimatedIcon.StateProperty)" Value = "Hover" />               
            </ VisualState.Setters >               
        </ VisualState >              
        < VisualState x: Name =”Pressed”>                                   
        </ VisualState >                    
        </ VisualStateGroup >                    
    </ VisualStateManager.VisualStateGroups >
    < ContentPresenter
    x: Name = "ContentPresenter"
    Background = "{TemplateBinding Background}"
    BorderBrush = "{TemplateBinding BorderBrush}"
    BorderThickness = "{TemplateBinding BorderThickness}"
    Content = "{TemplateBinding Content}"
    ContentTemplate = "{TemplateBinding ContentTemplate}"
    ContentTransitions = "{TemplateBinding ContentTransitions}"
    Padding = "{TemplateBinding Padding}"
    HorizontalContentAlignment = "{TemplateBinding HorizontalContentAlignment}"
    VerticalContentAlignment = "{TemplateBinding VerticalContentAlignment}"
    AutomationProperties.AccessibilityView = "Raw" />
< Grid x: Name = "Icon" HorizontalAlignment = "Right" Content = "{TemplateBinding Icon}/>
```
After the template is updated, you can add your animated icon just like any other icon to your NavigationViewItem: 

XAML
```xml
< muxc:NavigationView.MenuItems >
    < muxc:NavigationViewItem Content = "Menu Item1" Tag="SamplePage" x:Name = "SamplePageItem" >
        < muxc:NavigationViewItem.Icon >
            < controls:AnimatedIcon x:Name = "PlayAnimatedIcon" >
                < controls:AnimatedIcon.Source >
                    < local:PlayIcon />
                </ controls:AnimatedIcon.Source>
            </ controls:AnimatedIcon >
    </ muxc:NavigationViewItem.Icon >
</ muxc:NavigationView.MenuItems >
```

# API Notes
### Notable Properties  
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
```

# Appendix
## Fallback for older operating systems 
AnimatedIcon will provide an API for a developer to add a static icon of type IconSource. This will be used when the operating system cannot play the animation from the LottieGen output. Lottie animations are not supported on operating systems older than RS5.


## Accessibility
This control is going to be part of the Icon family of controls so the accessibility requirements will reflect what the other icon controls have. AnimatedIcon will need to ensure that the high contrast brushes propagate correctly if the user changes their settings to a high contrast theme. The control will also need to ensure that when the user changes the theme to light or dark, the icon changes its theme as well. 

If the user turns off animations in theri system settings, the AnimatedIcons will not animate. 

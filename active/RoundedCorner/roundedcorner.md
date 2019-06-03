# Background
This "spec" will be what is being propsed to be a documentation that will be added under https://docs.microsoft.com/en-us/windows/uwp/design/style/ "Corner Radius" page to be created. Thus, spec template has been modified but the core of the information that is necessary for spec discussion should appear here (if not, please give feedback as not intentional).

I am trying out writing a little more "background explanation (WHY)" that our customers have expressed we provide with our documentaiton in some of our focus groups. I would like feedback as this does not follow normal documentation pattern.

# Corner Radius
<Add thinking behind why we are rounding corners related to Fluent design.>
  
## Principles
<Less is more professional, more is more friendlier>

## High level design
## Default designs
There are mainly 3 types of UI parts where the radii of the corners are being rounded and here are the descriptions of how this is applied to default controls.

*Corners of rectangle UI elements*
- These UI elements include basic UIs like buttons that users see them on a surface of the screen at all times.
- The default radius value we use are 2px for these UI.
![Button](https://github.com/microsoft/microsoft-ui-xaml-specs/blob/user/chigy/roundedcorner/active/RoundedCorner/ImageFiles/Button.png)

*Corners of flyout UI elements*
- These UI elements are those that appear temporarily on UI like a MenuFlyout or Flyout.
- The default radius value we use here are 4px.

*Rounding of bars*
- These UI elements are bar like or line like UI (e.g. ProgressBar).
- The default radius value we use here are 2px except for slider in ColorPicker which will be 6px because the bar is wider.

*When not to round*
- When a multiple buttons are inside a single button that houses them (e.g. SplitButton).
![SplitButton](https://github.com/microsoft/microsoft-ui-xaml-specs/blob/user/chigy/roundedcorner/active/RoundedCorner/ImageFiles/SplitButton2.png)
- When a control is housed inside another container (e.g. ScrollBar's bar and buttons that are part of the ScrollBar container which is also part of a ScrollViewer).

![ScrollBar](https://github.com/microsoft/microsoft-ui-xaml-specs/blob/user/chigy/roundedcorner/active/RoundedCorner/ImageFiles/ScrollBar.png)
- When flyout UI element that is connected to a UI that invokes the flyout on one side.
![AutoSuggest](https://github.com/microsoft/microsoft-ui-xaml-specs/blob/user/chigy/roundedcorner/active/RoundedCorner/ImageFiles/AutoSuggest.png)

## Note on keyboard focus rect and shadow
Currently our default design does not require any special work to round the corners of keyboard focus rectangle and shadow. Using higher corner radius value will not break them founcionally, however it is good to be aware of this to avoid unwanted visual glitches that you could introduce with a larger value.

# Rounded Corner and Performance
People may not aways be aware of the fact drawing rounded corner naturally use more drawing power compared to square corners. When selecting the value of corner radius, we not only considered the design principles but also were very careful to ensure developers who use our default controls are able to successfully deliver performant applications. 

To simplify this section, think of performance issues mentioned here to be mainly about page load time as well as app launch time.

Here are a few key pieces of information for people to be aware about the performance:
- Rounded corners on a larger surface UI are less performant. E.g. avoid drawing rounded corner on a full screen app UI. This is less of an issue if the UI is displayed briefly (e.g. ContentDialog).
- Drawing many rounded corners at once could be less performant. Many smaller UI with rounded corners perform better than many medium UI with rounded corners.


# Remarks

# API Notes and Details
When this "spec" becomes the external document, add API details to customize the Corner Radius to different values from #684.

# Appendix

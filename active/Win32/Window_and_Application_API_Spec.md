# Window and Application API Spec

This spec describes updates to the XAML Window and Application APIs to accommodate this move to support Desktop (win32) apps.

## Spec Status
In review. No scheduled yet for reviewing by the API Board. 


## Execution Notes

This API spec contains the the APIs under the following criteria:
1. Already exists in Window.UI.Xaml.Window. 
2. Are part of the Mininum Viable Product

There are other proposed APIs proposed that will be part of the future phases. 
> A Phase doesn't correspond with a product version, it's just a set of Sprints with allocated resources to execute this work.

##   Design Agreement
- The new Window implementation reuses the current Windows.UI.XAML.Window class and it will be placed under Microsoft.UI.XAML.Window. 
- Current methods, events, and properties should still work for UWP and add the new Win32 support. 
- These APIs use different code paths depending on whether WinUI 3 is running on Win32 or UWP. 
- There are some APIs that don’t work on Win32 or UWP yet in this phase. Those APIs could return **null** or **throw** an exception depending on the case. 

This agreement applies to the Application class too.

## Window Class
Namespace: Microsoft.UI.Xaml

```CS
public class Window : IClosable
```

### **Window management, appearance, and behaviors**

### Window() constructor **[NEW]** 
The constructor initializes the Window object. Windows are created in the same thread where they are instantiated. Frequently, this thread is the UI Thread.

```CS
public Window();
```
#### Samples
##### Code Sample
```CS
Window window = new Window();
window.Content = new TextBlock() { Text = "Hello" };
window.Activate(); 
```
##### Markup Sample

```XML
<Window 
    x:Class="MainWindow"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
>
    <Grid><Button Content=”Click” Click=”OnClick”/></Grid>
</Window>
```
```CS
public partial class MainWindow:Window
{
  public MainWindow()
  {
	InitializeComponent();
  }
}
```
##### Multiple Widndows Sample
Developers can create multi-window experiences with WinUI 3 in the same UI thread. 
```CS
Window w = new Window ();
Button b = new Button() { Content = "Click Me" };
b.Click += OnClick;
w.Content = b;
w.Activate();
```
```CS
private void OnClick(object sender, RoutedEventArgs e)
{
   Window w2 = new Window ();
   w2.Content = new TextBlock(){Text=”Window 2”}
   w2.Activate();
}
```
##### Create a Window in a separated thread
To create a Window in a separated thread, you should create the thread first and create a Window in this new Thread. 
```CS
private void Button_Click(object sender, RoutedEventArgs e)
{
    Thread thread = new Thread(() =>
    {
        Window w = new Window();
        w.Content = new TextBlock() { Text = "Separated Thread Window" };
        w.Activate();
        w.Closed += (sender1, e1) => w.Dispatcher.InvokeShutdown();

        System.Windows.Threading.Dispatcher.Run();

    });
    thread.SetApartmentState(ApartmentState.STA);
    thread.IsBackground = true; 
    thread.Start();
} 
```

 #### Remarks

- **Win32**: The constructor creates a top-level Win32 Window (HWnd). A top-level Window is a window that is not a child window or has no parent window. 

- **UWP**: There is already one CoreWindow created and UWP only allows one CoreWindow per thread, as a consequence, the contructor and the XAML Markup throw an exception when running in UWP.
    - Exception type: TBD
 
    - Exception Message: TBD. Something like "Only one Window per thread is allowed in UWP app model".

### Window.Bounds property
Gets a Rect value containing the height and width of the application window in logical pixels.

```CS
public Rect Bounds { get; }
```
#### Property value

Rect: A value that reports the height and width of the window.

#### Remarks

- **Win32**: The Window is created with the default size of the Window specify by the Window Shell.
- **UWP**: Keep the current behavior. To change the size of the CoreWindow in UWP, it’s required to use ApplicationView’s PreferredLaunchViewSize and PreferredLaunchWindowingMode.

### Window.Icon property **[NEW]** 
Gets or sets a window's icon. For each window, this icon is used in its title bar, its task bar button, and in its ALT+TAB application selection list entry.

```CS
public ImageSource Icon { get; set; }
```

#### Property value
Icon: An ImageSource object that represents the icon.

#### Sample
```XML
<Window
  xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
  xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
  x:Class="Sample.MainWindow"
    Icon="WinUI3Icon.ico">
</Window>
```

#### Remarks:
- **Win32**: The image is set as an Icon of the Window. 
- **UWP**: Do nothing. The property value is ignored.

### Window.Content property
Gets or sets the UIElement visual root of the window. 

```CS
[ContentProperty(“Content”)]
public UIElement Content { get; set; }
```
#### Property Value
UIElement: The visual root of a window.

#### Remarks
This property has the ContentProperty attribute so the XAML designer can display the content on the designer.

### Window.Visible property
Gets a value that reports whether the window is visible. Visibility does not consider the window being behind another window and not visible. It only detects if it is on the screen. This property matches the XamlRoot’s IsHostVisible property.

```CS
public bool Visible { get; } 
```

#### Property Value
bool: **true** if the window is visible; **false** if the window is not visible.

#### Remarks
- **Win32**: False when the Window is minimized or never displayed. 
- **UWP**: Keep the same current UWP behavior.


### Window.VisibilityChanged Event

Occurs when the value of the Visible property changes. Also, it’s fired when the XamlRoot’s IsHostVisible changes as a consequence of cascade change of the Window’s Visibility.

```CS 
Window w = new Window();
window.Content = new TextBlock() { Text = "Hello" };
window.VisibilityChanged += OnVisibilityChanged;
```	

```CS
private void OnVisibilityChanged(object sender, VisibilityChangedEventArgs e)
{
   if (e.Visible)
   {
      //
   }
}
```
#### Remarks
VisibilityChangedEventArgs includes a Boolean Visible property that you should check to determine the visibility state when the event was fired.

### Window.SetTitleBar(UIElement) Method
Set the custom XAML content that should act as the title bar.
```CS 
public void SetTitleBar(UIElement); 
```

#### Parameters
UIElement: Custom XAML content that should act as the title bar. To use multiple objects, wrap them in a container element such as one derived from Panel.

#### Remarks

- **Win32**: This method do nothing when running in Win32.
- **UWP**: Continue using the OS behavior. More info [here](https://docs.microsoft.com/en-us/uwp/api/windows.ui.xaml.window.settitlebar) 

### Window.Title property **[NEW]**

Set or get a string to display on the Title. 

```CS
public string Title { get; set; }
```
#### Sample
```XML
```XML
<Window 
    x:Class="MainWindow"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    Title="Main Window" 
>
    
</Window>
```

#### Remarks
This is not a DependencyProperty.

- **UWP**: This string is not displayed in the Window's Title bar when SetTitleBar method is used or set a not null content. The Title is still used in Alt+Tab scenarios,


### Window.Current property

Gets the window of the current thread.

```CS
public static Window Current { get; }
```

#### Remarks
- **UWP**: Gets the Window object for the current thread. This is based on the asumption that UWP only has just one CoreWindow.
- **Win32**: Returns null when running in Win32 given there could be multiple Windows on the same thread. 

### **Window lifetime management**

### Window.Activate method 
Attempts to activate the window by making it visible, bringing it to the foreground and setting the input focus to it. If the Window can’t be activated, it fails silently.

```CS
public void Activate()
```

### Window.Activated Event
 Occurs when the window has successfully been activated or deactivated. If the focus takes away from the Window, the deactivate event will occur. If the focus returns to the window the activate event occurs.
```CS
public event WindowActivatedEventHandler Activated
```

#### Samples

```CS
window.Actived += OnActivated;
```	

```CS
void OnActivated(object sender, WindowActivatedEventArgs e)
{
   if (e.WindowActivationState == WindowActivationState.CodeActivated)
    {
        // 
    }
    else if (e.WindowActivationState == WindowActivationState.Deactivated)
    {
        // 
    }
    else if(e.WindowActivationState == WindowActivationState.PointerActivated)
    {
       //
    }
}
```
#### Remarks
- **UWP**: Current behavior is [here](https://docs.microsoft.com/en-us/uwp/api/windows.ui.xaml.window.activated)

### Window.Close method

Closes and destroy the window. The Closed event occurs when a Window closes. 
```CS
public void Close()
```

#### Remarks
- **UWP**: UWP app typically use a single Window instance, and do not open and close additional Window instances. Additionally, you will not typically provide custom UI to enable users to close the app window.

### Window.Closed event

Occurs after the window is closed. The Closed event is fired when the user closes the window, or the window is closed programmatically. Also, this causes the Activated event fires. 


#### Samples

```CS
  window.Closed += OnClosed;
```	
```CS
void OnClosed(object sender, MUX.WindowEventArgs e)
{
   //
}
```

#### Remarks

If this was the last window to be closed for the app:
- **Win32**: The application ends and call Suspending event.
- **UWP**: The application is suspended, and the Suspending event will fire. 

### **Window placement management**

### Window.SizeChanged event

This event occurs whenever there are changes in the size of a window (e.g.: user resizes the window, user moves the window to a new display that has a different resolution and the window expands to fill it)

```CS
public event WindowSizeChangedEventHandler SizeChanged
```
#### Samples

```CS  
window.SizeChanged += OnSizeChanged;
```	

```CS
void OnSizeChanged(object sender, MUX.WindowSizeChangedEventArgs e)
{
    if(!e.Size.IsEmpty && e.Size.Height != e.Size.Width)
    {
       //This is not a square Window
    }
}
```
#### Remarks
- **UWP**: More info [here](https://docs.microsoft.com/en-us/uwp/api/windows.ui.xaml.window.sizechanged).

### **Get the underneath window implementation**
UWP apps use CoreWindow as a window implementation and Win32 apps use HWnds instead. The Microsoft.UI.Xaml.Window hides these detail implementations from the application developers and exposes a set of APIs that allow to create and manipulate Windows.
These properties expose these underneath objects to enable advanced (or basic but not supported yet) scenarios, so developers can get the CoreWindow or HWnd objects to apply setters, listening events, etc.

### Window.CoreWindow property: 

Get the CoreWindow of a Window when the Window was created in a UWP app model.

```CS
public CoreWindow CoreWindow { get; }
```

#### Remarks
- **Win32**: This property returns null when running in Win32 app model. 

### IWindowInterop Interface 
Exposes a COM interface (only callable from Win32) to get the HWnd of a Window in a Win32 app model.

```CS
Window w = new Window();
w.QueryInterface(IWindowInterop);
IntPtr hwnd = w.GetInterop<IWindowInterop>().Handle;
```

### **Others**
### Window.Dispatcher property
Gets the Dispatcher object for the Window.

```CS
public CoreDispatcher Dispatcher { get; }
```
#### OPEN ISSUE: 
We are Still figuring out what type return. There is no CoreDispatcher in Win32 (DisptacherQueue works) and the current UWP behavior gets the CoreDispatcher object for the Window.

Evaluating: 
1. Create a 'XamlDispatcher' that uses underneath the available dispatcher.
2. Make CoreDispatcher works in Win32
3. Returns DispatcherQueue in Win32 and UWP
4. Returns null in Win32 and keep using CoreDispatcher. Win32 devs should use DisptacherQueue.GetForCurrentThread()

### Window.UIContext property **[Remove it]**
Gets the context identifier for the window. Basically, this is used by InputPane and CoreInputView APIs to figure out where to display the Software Input Panel (SIP). 

```CS
public UIContext UIContext {get;}
```

#### Decision:
We should do here whatever we decide to do on UIElement. The current plan is to remove it.

### Window.Compositor property

 Gets the Compositor for this Window. This allows to access to the Visual Layer (the layer where the XAML framework sits) and use WinRT compositor APIs inside of the Window. 

```CS
public Microsoft.UI.Composition.Compositor Compositor { get; }
```
### Remarks
There is one Compositor per thread, that means that all the Windows in the same thread share the same Compositor.



## WindowEventArgs class
Namespace: Microsoft.UI.Xaml

Contains the set of arguments returned to an app after a window input or behavior event.

### Properties

- **WindowEventArgs.Handled**: Gets or sets whether the event was handled. **true** if the event has been handled by the appropriate delegate; **false** if it has not.
    ```CS
    public bool Handled { get; set; }
    ```


## WindowActivatedEventArgs class
Namespace: Microsoft.UI.Xaml

Contains the windows activation state information returned by the Window.Activated event.

```CS
public class WindowActivatedEventArgs
```

### Properties
- **WindowActivatedEventArgs.Handled**: Gets or sets whether the window activation changed event was handled. **true** if the event has been handled by the appropriate delegate; **false** if it has not.
    ```CS
    public bool Handled { get; set; }
    ```


- **WindowActivatedEventArgs.WindowActivationState**: Gets the activation state of the window at the time the event was raised.
    ```CS
    public CoreWindowActivationState WindowActivationState { get; }
    ```

## VisibilityChangedEventArgs class 
Namespace: Microsoft.UI.Xaml


Contains the arguments returned by the event fired when a Window instance's visibility changes.
```CS
public class VisibilityChangedEventArgs
```

### Properties:
- VisibilityChangedEventArgs.Handled: Gets or sets whether the window activation changed event was handled. **true** if the event has been handled by the appropriate delegate; **false** if it has not.
    ```CS
    public bool Handled { get; set; };
    ```

- VisibilityChangedEventArgs.Visible: Gets whether the window is visible or not. **true** if the window is visible; otherwise is **false**.
    ```CS
    public bool Visible { get; } 
    ```

## WindowSizeChangedEventArgs class
Namespace: Microsoft.UI.Xaml

Contains the argument returned by a window size change event.

### Properties

- WindowSizeChangedEventArgs.Handled: Gets or sets whether the  event was handled. **true** if the event has been handled by the appropriate delegate; **false** if it has not.
    ```CS
    public bool Handled { get; set; };
    ```

- WindowSizeChangedEventArgs.WindowSize: Gets the new size of the window in units of effective (view) pixels.
    ```CS
    public Size WindowSize { get; } 
    ```


## Application class
Namespace: Microsoft.UI.Xaml

XAML Application exposes special support for managing the Windows in an application. 



```CS
public class Application:IClosable
```
```XML
<Application />
 ```

> **NOTE**: It is not a goal of this doc to review all the members of this class. Just that ones that requires changes in Win32. You can find more inforation about Application class [here](https://docs.microsoft.com/en-us/uwp/api/windows.ui.xaml.application).

### Application.Start method

Provides the entry point and requests initialization of the application. Use the callback to instantiate the Application class.

```CS
public static void Start(ApplicationInitializationCallback callback)
```

#### Samples
```CS
[System.STAThreadAttribute()]
static void Main(string[] args)
{
    InitializeDetours();
    Microsoft.UI.Xaml.Application.Start((p) => new App());
} 
```
#### Remarks
- Start is used by the Main function that is autogenerated by the XAML compiler ( the .g files)

- This method initializes all the XAML environment. It creates the Window Xaml Manager, the CoreWindow (in UWP only), the messages pump (in Win32), etc.

### Application.OnLaunched(LaunchActivatedEventArgs) method

Invoked when the application is launched. Override this method to perform application initialization and to display initial content in the associated Window (UWP) or create a new Window (Win32). 

```CS
protected virtual void OnLaunched(LaunchActivatedEventArgs args)
```

#### Samples
The following code shows a sample of the OnLaunched implementation for Win32.

```CS
protected override void OnLaunched(MUX.LaunchActivatedEventArgs e)
{
     MainWindow window = new MainWindow();
     
     if(e.Arguments != null)
     {
        string[] arguments = e.Arguments.Split(' ');
 window.FileName = arguments[0]
     }	

     window.Activate();
           
}
```

#### Remarks
The Visual Studio project templates include a basic implementation for OnLaunched. For example, the UWP project templates, in OnLaunched, sets CoreWindow’s content with a  Frame, setup the navigation events, and activates the window. Also, this is point where the App can get the command arguments using the LaunchActivatedEventArgs’s Arguments property.

### Application.OnWindowCreated(WindowCreatedEventArgs) method
Invoked when the application creates a window.

```CS
protected virtual void OnWindowCreated(WindowCreatedEventArgs args)
```
#### Parameters
args: Event data for the event. The WindowCreatedEventArgs contains the Window object.

#### Remarks
- **UWP**: This method is called once for the creation of the main window, once for each hosted window, and once each time CreateNewView is called. 
- **Win32**: This method is called every time a Window object is created.


## LaunchActivatedEventArgs class

Namespace: Microsoft.UI.Xaml.Application

Provides event information when an app is launched.

### Properties

- LaunchActivatedEventArgs.Arguments: Gets the arguments that are passed to the app during its launch.
    ```CS
    public String Arguments { get; };
    ```
- LaunchActivatedEventArgs.UWPLaunchActivatedEventArgs: Gets the LaunchActivatedEventArgs from UWP. This property gets Null in Win32.
    ```CS
    public Windows.ApplicationModel.Activation.LaunchActivatedEventArgs UWPLaunchActivatedEventArgs { get; };
    ```

###	Other Application APIs 
#### These APIs should work on Win32 besides UWP in the same way as UWP.
- Application ()
- static Application Current { get; }
- DebugSettings DebugSettings { get; }
- FocusVisualKind FocusVisualKind { get;  set; }
- ApplicationHighContrastAdjustment HighContrastAdjustment { get;  set; }
- ApplicationTheme RequestedTheme { get;  set; }
- ResourceDictionary Resources { get;  set; }
- void Exit ()
- static void LoadComponent (Object component, Uri resourceLocator)
- static void LoadComponent (Object component, Uri resourceLocator, ComponentResourceLocation componentResourceLocation)
- SuspendingEventHandler Suspending;
- UnhandledExceptionEventHandler UnhandledException;

#### Application APIs that are no called on Win32 
- void overridable OnActivated(IActivatedEventArgs args)
void overridable OnBackgroundActivated (BackgroundActivatedEventArgs)
- void overridable OnCachedFileUpdaterActivated (CachedFileUpdaterActivatedEventArgs)
- void overridable OnFileActivated (FileActivatedEventArgs)
- void overridable OnFileOpenPickerActivated (FileOpenPickerActivatedEventArgs)
- void overridable OnFileSavePickerActivated (FileSavePickerActivatedEventArgs)
- void overridable OnSearchActivated (SearchActivatedEventArgs)
- void overridable OnShareTargetActivated (ShareTargetActivatedEventArgs)
- EnteredBackgroundEventHandler EnteredBackground;
- LeavingBackgroundEventHandler LeavingBackground;
- EventHandler<Object> Resuming;
- ApplicationRequiresPointerMode RequiresPointerMode {get;set;};


# Window and Application API updates 

This spec describes updates to the XAML Window and Application APIs to enable them to support Desktop apps in addition to UWP.

# Background

XAML has a [Window](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Window) 
class which in UWP wraps a [CoreWindow](https://docs.microsoft.com/uwp/api/Windows.UI.Core.CoreWindow), 
and an [Application](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Application) 
class which in UWP wraps a [CoreApplication](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Core.CoreApplication). 
For WinUI 3 Xaml Window and Application are being expanded to not require a CoreWindow or CoreApplication; 
Window can use an HWND, and Application can run a message pump, running as a Desktop app.
This spec has the API additions to Application and Window to support this.

Note that some existing APIs will also behave differently when running as a Desktop app. 
For example, the static 
[Window.Current](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Window.Current) 
property today returns the Window for the current (calling) thread, 
but in a Desktop app it will return null. 
Similarly the Window.CoreWindow property will be null when not running as UWP.

Aside from Desktop support, the new feature here is custom title bar support, in the
form of a `Window.ExtendsContentIntoTitleBar` API.

# API Pages

## Window class

Window represents a WinUI application window. It can be used in a 
[UWP](https://docs.microsoft.com/en-us/windows/uwp/get-started/universal-application-platform-guide) 
app or a Desktop app. When run in a UWP app there can only be one instance on a thread.

### Example: Set the content of a given window and give it keyboard focus.

The following sets the content of the calling thread's Window and gives it keyboard focus.

```CS
void InitializeAndActivateWindow(Window window)
{
    window.Content = new TextBlock() { Text = "Hello" };
    window.Activate();
}
```

### Example: Create a new window in a Desktop app

In a Desktop app you create each Window, and you can create more than one Window on a thread.

```CS
Window window = new Window();
window.Content = new TextBlock() { Text = "Hello" };
window.Activate(); 
```

You can also define a new Window in markup:

```XML
<Window 
    x:Class="MainWindow"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml">
    <TextBlock>Hello</TextBlock>
</Window>
```

with corresponding code-behind:

```CS
public partial class MainWindow : Window
{
  public MainWindow()
  {
	InitializeComponent();
  }
}
```

and then you can create/activate that custom window from code:

```CS
MainWindow window = new MainWindow();
window.Activate();

```

> Spec note  
No <Window.Content> tag is required because the Window.Content property is updating in this spec to become the
[ContentPropertyAttribute](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Markup.ContentPropertyAttribute).

In a UWP app any UI thread already has a Window on it, which you can retrieve using the static 
[Window.Current](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Window.Current) 
property. You can create additional windows by creating additional 
[CoreApplicationViews](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Core.CoreApplicationView), 
which are always created on a new thread, and which automatically create the following for the new thread: 
[ApplicationView](https://docs.microsoft.com/uwp/api/Windows.UI.ViewManagement.ApplicationView), 
[CoreWindow](https://docs.microsoft.com/uwp/api/Windows.UI.Core.CoreWindow), 
and the Xaml [Window](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Window).

# Create a new Window on a new thread

This creates a new Xaml Window on a new thread in a UWP app:

```CS
_ = CoreApplication.CreateNewView().DispatcherQueue.TryEnqueue(() =>
{
    // This code runs on the new thread
    Window.Current.Content = new TextBlock() { Text = "Hello" };
    Window.Current.Activate();
});
```

To create a new Window on a new thread in a Desktop app, create the thread first:

```CS
var thread = new Thread(() =>
{
    Window window = new Window();
    window.Content = new TextBlock() { Text = "Hello" };
    window.Activate();
    window.Closed += (_, __) => w.DispatcherQueue.InvokeShutdown();

    var syncContext = new DispatcherQueueSyncronizationContext();
    SynchronizationContext.SetSynchronizationContext(syncContext);

    this.ProcessEvents(); // Message pump
});

thread.SetApartmentState(ApartmentState.STA);
thread.Start(); 
```

> Spec note: The DispatcherQueueSyncronizationContext is a new API 
[described here](https://github.com/microsoft/microsoft-ui-xaml-specs/blob/master/winui3/DispatcherQueueUpdates.md)

### Remarks

The Window class can be used both in a UWP app or a Desktop app.

In a Desktop app you can create (`new`) a Window, even if the current thread already has a Window on it.
In a UWP app you must create a new 
[CoreApplicationView](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Core.CoreApplicationView),
which will automatically create a new thread and a Window on it. 
See the 'Create new Window in UWP application' example.

> Implementation note: Attempting to activate a new window in a UWP app will use 
[RoOriginateError](https://docs.microsoft.com/en-us/windows/win32/api/roerrorapi/nf-roerrorapi-rooriginateerror) 
to show an explanatory message in the debugger.

Creating a new Window in a Desktop app creates a new top level HWND.

## Window.ExtendsContentIntoTitleBar

Gets or sets a value that specifies whether the default window title bar should be removed, creating more space for content.

Setting this property to true causes the built-in title bar to be removed. 

> Spec note: this is analogous to 
[AppWindowTitleBar.ExtendsContentIntoTitleBar](https://docs.microsoft.com/uwp/api/Windows.UI.WindowManagement.AppWindowTitleBar.ExtendsContentIntoTitleBar),
and the implementation uses that property in UWP.

Setting this property only effects rendering, it does not affect pointer (such as mouse) behavior. 
For example, you can still use the mouse to click down in the area at the top of the window and 
drag the window around the desktop. To change that behavior use the `SetTitleBar` API.

Extending the content into the title bar does not impact the Window buttons 
(Minimize, Maximize, and Close).

See the `SetTitleBar` example below for an example of ExtendsContentIntoTitleBar

## Window.SetTitleBar 
_Behavior update to existing API when running as Desktop app_

Makes a XAML element interact with the system as if it’s the title bar.

For example, a user can move the window by dragging the XAML UIElement, 
or a user can invoke the window context menu by right-clicking it. As a consequence the app no longer
receives pointer input when the user interacts with the target UIElement or its children using touch, mouse, or pen. 
The UIElement does still receive keyboard input.

Only one UIElement can act as Title bar, so the last set wins. To use multiple objects, 
you need to wrap them in a container element (for example a Grid).

The rectangular area occupied by the element acts as the title bar for pointer purposes,
even if the element is blocked by another element,
even if the element is transparent.

This method is typically used with the Window's `ExtendContentIntoTitleBar` property 
set to true in order to hide the default system title bar.
However, even when the default system title bar is not hidden, SetTitleBar can be used to make 
additional regions in your app behave like the title bar.

The following shows a Window with a no built-in title bar. 
Instead it uses the whole window as its content area. To enable features such as window dragging, 
it designates a TextBlock as the title bar.

```xml
<Window ...>
    <Grid>
        <!-- ... -->

        <TextBlock x:Name="CustomTitleBar">Custom title text</TextBlock>

        <!-- ... -->
    </Grid>

</Window>
```

```CS
private Window m_window;

protected override void OnLaunched(Microsoft.UI.Xaml.XamlLaunchActivatedEventArgs args)
{
    m_window = new MainWindow();

    m_window.ExtendContentIntoTitleBar = true;
    m_window.SetTitleBar(m_window.CustomTitleBar);

    m_window.Activate();
}
```

Title bars should be have background and foreground colors based on
whether the window is current foreground (active) or background. Additionally, there's an 
option in Windows Settings to use the user's accent color as the foreground of the title bar.
See [link TBD] for a sample application that implements this.


## Window.Title property

Get or set a string to display as the window's title.

This title is displayed in the windows title bar (unless `ExtendsContentIntoTitleBar` is set),
as well as other locations such as when the user presses Alt-Tab to bring up the application switcher.

### Example

```XML
<Window 
    x:Class="MainWindow"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    Title="Main Window" >
    
</Window>
```

### Remarks

In a UWP app, this property is a wrapper for 
[ApplicationView.Title](https://docs.microsoft.com/uwp/api/Windows.UI.ViewManagement.ApplicationView.Title). 
In a Desktop app this is a wrapper for 
[SetWindowText](https://docs.microsoft.com/en-us/windows/win32/api/winuser/nf-winuser-setwindowtexta).

## Window.Current property
_Behavior update to existing API when running as Desktop app_

Gets the window of the current thread.

### Remarks

The value of this property depends on the thread from which it is called. 
If called from a UI thread of a UWP app, the value is the Window instance for that thread. 
On any other thread, the value is null.

## Window.CoreWindow property
_Behavior update to existing API when running as Desktop app_

Gets the [CoreWindow](https://docs.microsoft.com/uwp/api/Windows.UI.Core.CoreWindow) associated 
with this Window. In a Desktop app this is always null.

```CS
public CoreWindow CoreWindow { get; }
```

## Window.Dispatcher property
_Behavior update to existing API when running as Desktop app_

Gets the [CoreDispatcher](https://docs.microsoft.com/uwp/api/Windows.UI.Core.CoreDispatcher) 
object for the Window when called from a UWP app. Returns null when called from a Desktop app.

```CS
public CoreDispatcher Dispatcher { get; }
```

The preferred alternative to this property is the `Window.DispatcherQueue` property,
which provides a 
[DispatcherQueue](https://docs.microsoft.com/uwp/api/Windows.System.DispatcherQueue),
which similarly allows you to run code on the Window's UI thread.

Most members of the Window class can only be accessed when running on the thread the object was
created on. This property, though, can be called from any thread.

## Window.DispatcherQueue property

Gets the [DispatcherQueue](https://docs.microsoft.com/uwp/api/Windows.System.DispatcherQueue) 
object for the Window.

```CS
public DispatcherQueue DispatcherQueue { get; }
```

Most members of the Window class can only be accessed when running on the thread the object was
created on. This property, though, can be called from any thread.

> Spec note: This Window.DispatcherQueue property matches the new DependencyObject.DispatcherQueue property
[described here](https://github.com/microsoft/microsoft-ui-xaml-specs/blob/master/winui3/DispatcherQueueUpdates.md)


## IWindowNative COM Interface

This interface is implemented by `Window`, and in a Desktop app can be used to get 
the Window's underlying HWND.

```CS
protected override void OnLaunched(Microsoft.UI.Xaml.XamlLaunchActivatedEventArgs e)
{
   m_window = new MainWindow();
   m_window.Activate();
   
   // AS is an extension method in the WinRT.CastExtensions class. 
   // The WinRT namespace is needed to use the extension method.
   var windowNative = m_window.As<IWindowNative>();
   IntPtr HWND = windowNative.WindowHandle;
   ...
}

[ComImport]
[InterfaceType(ComInterfaceType.InterfaceIsIUnknown)]
[Guid("EECDBF0E-BAE9-4CB6-A68E-9598E1CB57BB")]
internal interface IWindowNative
{
   IntPtr WindowHandle { get; }
}
```

## Application class

> Spec note: Application is not new to this spec, what is new are the following remarks

### Remarks

The following events and virtual methods are not invoked when running in a Desktop app:

* void overridable OnActivated(IActivatedEventArgs args)
* void overridable OnBackgroundActivated (BackgroundActivatedEventArgs)
* void overridable OnCachedFileUpdaterActivated (CachedFileUpdaterActivatedEventArgs)
* void overridable OnFileActivated (FileActivatedEventArgs)
* void overridable OnFileOpenPickerActivated (FileOpenPickerActivatedEventArgs)
* void overridable OnFileSavePickerActivated (FileSavePickerActivatedEventArgs)
* void overridable OnSearchActivated (SearchActivatedEventArgs)
* void overridable OnShareTargetActivated (ShareTargetActivatedEventArgs)
* void overridable OnWindowCreated (WindowCreatedEventArgs)
* EnteredBackgroundEventHandler EnteredBackground;
* LeavingBackgroundEventHandler LeavingBackground;
* SuspendingEventHandler Suspending;
* EventHandler&lt;Object&gt; Resuming


## Application.Start method
_Behavior update to existing API when running as Desktop app_

Static method which provides the entry point and requests initialization of the application. Use the callback 
to instantiate the Application class.

In a Desktop app this will run a message pump internally, and not return until the application 
shuts down. In a UWP app it will return immediately.


## Application.OnLaunched(XamlLaunchActivatedEventArgs) method
_Behavior update to existing API when running as Desktop app_

Invoked when the application is launched. Override this method to perform application initialization 
and to display initial content in the associated Window (UWP apps) or create a new Window (Desktop apps).

```CS
protected virtual void OnLaunched(XamlLaunchActivatedEventArgs args)
```

> This method is typically created automatically when a project is created. 
You can then modify it or remove it.

### Examples

In a Desktop app, use OnLaunched to create the main window and pass it the first command line argument.

```CS
protected override void OnLaunched(XamlLaunchActivatedEventArgs e)
{
     MainWindow mainWindow = new MainWindow();
     
     if(e.Arguments != null)
     {
        string[] arguments = e.Arguments.Split(' ');
        // Assuming MainWindow has a public property called FileName 
        mainWindow.FileName = arguments[0];
     }	

     mainWindow.Activate();
}
```

In a UWP app,

```CS
protected override void OnLaunched(XamlLaunchActivatedEventArgs e)
{
     MainPage page = new MainPage();
     
     if(e.Arguments != null)
     {
        string[] arguments = e.Arguments.Split(' ');
	    // Assuming MainPage has a public property called FileName 
        page.FileName = arguments[0];
     }	

    Window.Current.Content = page;
    Window.Current.Activate();
    window.Activate();
}
```


## Application.RequiresPointerMode property
_Behavior update to existing API when running as Desktop app_

Gets or sets whether a UWP app supports mouse mode, which emulates pointer interaction experiences 
with non-pointer input devices such as an Xbox gamepad or remote control. (All nested elements inherit 
this behavior.)

> Note: this property is ignored in a Desktop app

## Application.OnWindowCreated(XamlWindowCreatedEventArgs) method
_Behavior update to existing API when running as Desktop app_

Invoked when the application creates a window.

```CS
protected virtual void OnWindowCreated(XamlWindowCreatedEventArgs args)
```

### Remarks
- In a UWP app, this method is called once for the creation of the main window, once for each hosted window, 
and once each time CreateNewView is called. 
- In a Desktop app, this method is called every time a Window object is created.
## XamlWindowActivatedEventArgs class

Contains the windows activation state information returned by the Window.Activated event.

> Issue: should rename these to avoid collision.

> Spec note: this is a new XAML version of the existing 
Windows.UI.Core.[WindowActivatedEventArgs](https://docs.microsoft.com/uwp/api/Windows.UI.Core.WindowActivatedEventArgs).

## XamlWindowActivatedEventArgs properties

**Handled**

Gets or sets whether the window activation changed event was handled.
 **true** if the event has been handled by the appropriate delegate; **false** if it has not.

```CS
public bool Handled { get; set; }
```

**WindowActivationState**

Gets the activation state of the window at 
the time the event was raised.

```CS
public CoreWindowActivationState WindowActivationState { get; }
```

## XamlVisibilityChangedEventArgs class

Contains the arguments returned by the event fired when a Window instance's visibility changes.

> Spec note: this is a new Xaml version of the existing 
Windows.UI.Core.[VisibilityChangedEventArgs](https://docs.microsoft.com/uwp/api/Windows.UI.Core.VisibilityChangedEventArgs).


### XamlVisibilityChangedEventArgs

Contains the arguments returned by the event fired when a Window instance's visibility changes.

> Spec note: this is a new XAML version of the existing 
Windows.UI.Core.[VisibilityChangedEventArgs](https://docs.microsoft.com/uwp/api/Windows.UI.Core.VisibilityChangedEventArgs).

**Handled**

Gets or sets whether the window activation changed event was handled. **true** 
if the event has been handled by the appropriate delegate; **false** if it has not.

**Visible**

Gets whether the window is visible or not. **true** if the window is visible; otherwise is **false**.

## XamlWindowSizeChangedEventArgs class

> Spec note: this is a new XAML version of the existing 
Windows.UI.Core.[WindowSizeChangedEventArgs](https://docs.microsoft.com/uwp/api/Windows.UI.Core.WindowSizeChangedEventArgs).

Contains the argument returned by a window size change event.

### XamlWindowSizeChangedEventArgs Properties

**Handled**

Gets or sets whether the  event was handled. **true** if the event has been handled 
by the appropriate delegate; **false** if it has not.

**Size**

Gets the new size of the window in units of effective (view) pixels.



## XamlLaunchActivatedEventArgs class

Provides event information when an app is launched.

> Spec note: this is a new XAML version of the existing Windows.ApplicationModel.Activation.
[LaunchActivatedEventArgs](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Activation.LaunchActivatedEventArgs).

## XamlLaunchActivatedEventArgs Properties

**Arguments**

Gets the arguments that are passed to the app during its launch. 
In a UWP app, this is equivalent to LaunchActivatedEventArgs.Arguments.

```CS
public String Arguments { get; };
```

**LaunchActivatedEventArgs**
Gets the 
[LaunchActivatedEventArgs](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Activation.LaunchActivatedEventArgs) 
in a UWP app. This property is null in a Desktop app.

```CS
public Windows.ApplicationModel.Activation.LaunchActivatedEventArgs LaunchActivatedEventArgs { get; };
```

# API Details

## Window class
Microsoft.UI.Xaml namespace

> The Window class' APIs in WinUI3 (**Microsoft**.UI.Xaml) match the existing system Window
API (**Windows**.UI.Xaml), except for the following differences.

```cs
[webhosthidden]
[contentproperty("Content")]
unsealed runtimeclass Window
{

    ...

    // These APIs match the system Xaml versions, except the event args are now from the Xaml namespace
    event Windows.Foundation.TypedEventHandler<Object,Microsoft.UI.Xaml.XamlWindowActivatedEventArgs> Activated;
    event Windows.Foundation.TypedEventHandler<Object,Microsoft.UI.Xaml.XamlWindowClosedEventArgs> Closed;
    event Windows.Foundation.TypedEventHandler<Object,Microsoft.UI.Xaml.XamlWindowSizeChangedEventArgs> SizeChanged;
    event Windows.Foundation.TypedEventHandler<Object,Microsoft.UI.Xaml.XamlWindowVisibilityChangedEventArgs> VisibilityChanged;

    // New APIs
    String Title;
    Boolean ExtendsContentIntoTitleBar;
    Microsoft.System.DispatcherQueue DispatcherQueue{ get; };
}
```

## Application class

Microsoft.UI.Xaml namespace

> The Application class' APIs in WinUI3 (**Microsoft**.UI.Xaml) match the existing system Application
API (**Windows**.UI.Xaml), except the OnLaunched virtual args is
is now a Xaml type rather than Windows.ApplicationModel.Activation

```cs
[webhosthidden]
unsealed runtimeclass Application
{
    ...
    overridable void OnLaunched(Microsoft.UI.Xaml.XamlLaunchActivatedEventArgs args);
};
```

## IWindowNative (COM) interface

```cs
[
    object,
    uuid( EECDBF0E-BAE9-4CB6-A68E-9598E1CB57BB ),
    local,
    pointer_default(unique)
]
interface IWindowNative: IUnknown
{
    [propget] HRESULT WindowHandle([out, retval] HWND* hWnd);
};
```

## Event args

```cs
[webhosthidden]
runtimeclass XamlLaunchActivatedEventArgs
{
    String Arguments{ get; };
    Windows.ApplicationModel.Activation.LaunchActivatedEventArgs UWPLaunchActivatedEventArgs{ get; };
};
```

```cs
[webhosthidden]
runtimeclass XamlWindowActivatedEventArgs
{
    Boolean Handled;
    Microsoft.UI.Xaml.WindowActivationState WindowActivationState{ get; };
};
```


```cs
[webhosthidden]
runtimeclass XamlWindowCreatedEventArgs
{
    Microsoft.UI.Xaml.Window Window{ get; };
};
```


```cs
[webhosthidden]
runtimeclass XamlWindowSizeChangedEventArgs
{
    Boolean Handled;
    Windows.Foundation.Size Size{ get; };
};
```

```cs
[webhosthidden]
runtimeclass XamlWindowVisibilityChangedEventArgs
{
    Boolean Handled;
    Boolean Visible{ get; };
};
```

```cs
[webhosthidden]
runtimeclass XamlWindowClosedEventArgs
{
    Boolean Handled;
};
```


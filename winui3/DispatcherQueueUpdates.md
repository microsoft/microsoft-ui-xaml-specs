
# DispatcherQueue updates for WinUI3

# Background

.NET has an abstract API to post to a thread:
[SynchronizationContext](https://docs.microsoft.com/dotnet/api/System.Threading.SynchronizationContext).
Calling the [SynchronizationContext.Post](https://docs.microsoft.com/dotnet/api/System.Threading.SynchronizationContext.Post) method
runs a delegate on whatever thread the context is associated with. 
You can get the current thread's context using the static 
[SynchronizationContext.Current](https://docs.microsoft.com/dotnet/api/System.Threading.SynchronizationContext.Current)
property.

For example, the c# `await` keyword and `co_await` in c++/winrt work by capturing the Current sync context, 
then posting back to it in the async operation's completion handler.

SynchronizationContext can be subclassed and customized, for example using WPF's
[DispatcherSynchronizationContext](https://docs.microsoft.com/dotnet/api/System.Windows.Threading.DispatcherSynchronizationContext)
posts to the WPF [Dispatcher](https://docs.microsoft.com/dotnet/api/System.Windows.Threading.Dispatcher).

In .NET Native and .NET Framework, there's always a SynchronizationContext on a thread, a default
one if it's not been overridden (by calling 
[SynchronizationContext.SetSynchronizationContext](https://docs.microsoft.com/dotnet/api/System.Threading.SynchronizationContext.SetSynchronizationContext)). 
And that default sync context understands when a thread has a 
[CoreDispatcher](https://docs.microsoft.com/uwp/api/Windows.UI.Core.CoreDispatcher)
or [DispatcherQueue](https://docs.microsoft.com/uwp/api/Windows.System.DispatcherQueue)
associated with it. So by default, the .NET Native/Framework sync context works automatically on the UI
thread of a UWP app, as well as any thread that has a DispatcherQueue running on it
(a DispatcherQueue can be put onto a thread using the
[DispatcherQueueController.CreateOnDedicatedThread](https://docs.microsoft.com/uwp/api/Windows.System.DispatcherQueueController.CreateOnDedicatedThread)
method or [CreateDispatcherQueueController](https://docs.microsoft.com/en-us/windows/win32/api/dispatcherqueue/nf-dispatcherqueue-createdispatcherqueuecontroller)
function).

In .NET5, however, there is no default SynchronizationContext on every thread, and no built-in 
understanding of CoreDispatcher or DispatcherQueue. 

This spec adds this functionality for WinUI3 to work with SynchronizationContext, by defining a subclass of it:
`DispatcherQueueSynchronizationContext`. This can be set as a thread's SynchronizatonContext, and when posted to
will post to the underlying DispatcherQueue. Note that this is a .NET API, not a WinRT API.

Also described here is a WinRT method to create a DispatcherQueue, providing an alternative to the current COM API.

All of these APIs are for WinUI3/Renuion, not being added to the OS.

# Description

## DispatcherQueueSynchronizationContext

Use the `DispatcherQueueSynchronizationContext` to queue work to another thread, 
which has an associated [DispatcherQueue](https://docs.microsoft.com/uwp/api/Windows.System.DispatcherQueue).
See the base class
[SynchronizationContext](https://docs.microsoft.com/dotnet/api/System.Threading.SynchronizationContext)
for more information on how to use a SynchronizationContext.

### Remarks

This class is only available to .NET5 or above applications.

The Send method is not supported and throws a NotSupportedException.

## DispatcherQueueController.CreateOnCurrentThread

Creates a [DispatcherQueueController](https://docs.microsoft.com/uwp/api/Windows.System.DispatcherQueueController) 
on the caller's thread. Use the created DispatcherQueueController to create and manage the 
lifetime of a [DispatcherQueue](https://docs.microsoft.com/uwp/api/Windows.System.DispatcherQueue)
to run queued tasks in priority order on the Dispatcher queue's thread.

This method will fail with an error if the current thread already has a DispatcherQueue associated with it.
You can use [DispatcherQueue.GetForCurrentThread](https://docs.microsoft.com/uwp/api/Windows.System.DispatcherQueue.GetForCurrentThread)
to see if this is the case.

See also [CreateDispatcherQueueController](https://docs.microsoft.com/en-us/windows/win32/api/dispatcherqueue/nf-dispatcherqueue-createdispatcherqueuecontroller)


# Examples

## Creating a new thread controlled by a DispatcherQueue

Create a new thread and run a Window on it.

> Note that this sample requires WinUI3 running in a Desktop app

```cs
var controller = DispatcherQueueController.CreateOnDedicatedThread();

_ = controller.DispatcherQueue.TryEnqueue(() =>
{
    var syncContext = new DispatcherQueueSyncronizationContext();
    SynchronizationContext.SetSynchronizationContext(syncContext);

    var window = new Window();
    window.Content = new TextBlock() { Text = "Hello" };
    window.Activate();
});
```

## Creating a DispatcherQueue for the current thread

This example shows a thread ensuring it has a DispatcherQueue, setting a Synchronization
context onto the thread that uses that queue, and then goes into a message pump.

> In a WinUI you typically don't need to write this code for the initial UI thread, as it's
automatically generated.

```cs
static void Run()
{
    // Ensure we have a DispatcherQueue on this thread. 
    if(DispatcherQueue.GetForCurrentThread() == null)
    {
        DispatcherQueueController.CreateOnCurrentThread();
    }

    var syncContext = new DispatcherQueueSyncronizationContext();
    SynchronizationContext.SetSynchronizationContext(syncContext);

    ProcessEvents();
}

void ProcessEvents()
{
    // Message pump
    while(true)
    {
        // GetMessage
        // DispatchMessage
    }
}
```

# API Details

## DispatcherQueueSynchronizationContext

```cs
namespace Microsoft.System
{
    public class DispatcherQueueSynchronizationContext : SynchronizationContext
    {
    }
}
```

> Issue: What DLL is this in? (the DLL name doesn't matter for WinRT APIs, but this is a managed API)

## DispatcherQueueController

```cs
runtimeclass DispatcherQueueController
{
    // Existing members
    // ...
      static DispatcherQueueController CreateOnCurrentThread();

}
```

# Appendix

_This is a proposal to make the sync context automatic on all threads_

For the DispatcherQueueSynchronizationContext to be associated on a thread, something
somewhere needs to set it. In the example here the thread is created in c# and the sync
context is immediately set. But what if you have a c++ app creating a UI thread and
it's running a c# component?

A possible solution:
* Add a static event named something like DispatcherQueue.AttachedToThread
which is raised whenever a DQ is put onto a thread (and raised on that thread).
* In DispatcherQueueSynchronizationContext, add [module initialization code](https://github.com/dotnet/csharplang/issues/2608)
that registers for this event.
* In the handler, call SetSynchronizationContext (after checking that there isn't one already set).


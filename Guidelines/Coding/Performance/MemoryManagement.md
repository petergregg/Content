## Memory Management

## Who?

## What is it?

## When should it be used?

When using unmanaged resources that the garbage collector does not know about. For example;

When using any calls to api code in Win32 dlls or a call to a dll written in C++, that wraps low level functions or assembler instructions which actually execute the operations (on the operating system)

- Database connections
- Open files
- Bitmaps
- File streams
- Open network connections
- Unmanaged memory
- Allocated memory

## Where should it be implemented?

- Classes
- Base Classes


## Why should it be implemented?

So you don't get the following issues;

- Memory leaks
- Locked resources

## How do we implement it?

## Do's and Dont's
✔️ DO use using

Since dotnet x you can use the using pattern
```c#


```

✔️ DO implement IDiposable

✔️ DO call GC.SuppressFinalize(this); if Dispose(disposing: true); is called

## IDisposable Pattern Class Example
```c#

    // Implement IDisposable.
    // Do not make this method virtual.
    // A derived class should not be able to override this method.
    public void Dispose()
    {
        Dispose(disposing: true);
        // This object will be cleaned up by the Dispose method.
        // Therefore, you should call GC.SuppressFinalize to
        // take this object off the finalization queue
        // and prevent finalization code for this object
        // from executing a second time.
        GC.SuppressFinalize(this);
        }

```

✔️ DO implement IDiposable on sealed classes, however only implement cleanup in a Dispose() method
```c#

public sealed class DisposeSealed : IDisposable
{
    public void Dispose()
    {
        // clean up
    }
}
````

❌ DO NOT implement a finalizer (destructor) unless you directly reference unmanaged resources

❌ DO NOT call Dispose(true) from a destuctor ()

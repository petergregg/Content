# Using Standard Exception Types
This section describes the standard exceptions provided by the Framework and the details of their usage.

## Exception and SystemException

❌ DO NOT throw System.Exception or System.SystemException.

❌ DO NOT catch System.Exception or System.SystemException in framework code, unless you intend to rethrow.

❌ AVOID catching System.Exception or System.SystemException, except in top-level exception handlers.

## ApplicationException
❌ DO NOT throw or derive from ApplicationException.

InvalidOperationException
✔️ DO throw an InvalidOperationException if the object is in an inappropriate state.

## ArgumentException, ArgumentNullException, and ArgumentOutOfRangeException
✔️ DO throw ArgumentException or one of its subtypes if bad arguments are passed to a member. Prefer the most derived exception type, if applicable.

✔️ DO set the ParamName property when throwing one of the subclasses of ArgumentException.

This property represents the name of the parameter that caused the exception to be thrown. Note that the property can be set using one of the constructor overloads.

✔️ DO use value for the name of the implicit value parameter of property setters.

## NullReferenceException, IndexOutOfRangeException, and AccessViolationException
❌ DO NOT allow publicly callable APIs to explicitly or implicitly throw NullReferenceException, AccessViolationException, or IndexOutOfRangeException. These exceptions are reserved and thrown by the execution engine and in most cases indicate a bug.

Do argument checking to avoid throwing these exceptions. Throwing these exceptions exposes implementation details of your method that might change over time.

## StackOverflowException
❌ DO NOT explicitly throw StackOverflowException. The exception should be explicitly thrown only by the CLR.

❌ DO NOT catch StackOverflowException.

It is almost impossible to write managed code that remains consistent in the presence of arbitrary stack overflows. The unmanaged parts of the CLR remain consistent by using probes to move stack overflows to well-defined places rather than by backing out from arbitrary stack overflows.

## OutOfMemoryException
❌ DO NOT explicitly throw OutOfMemoryException. This exception is to be thrown only by the CLR infrastructure.

## ComException, SEHException, and ExecutionEngineException
❌ DO NOT explicitly throw COMException, ExecutionEngineException, and SEHException. These exceptions are to be thrown only by the CLR infrastructure.

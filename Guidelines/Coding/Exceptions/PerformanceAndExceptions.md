# Performance and Exceptions

## Throwing too many exceptions and logging them
## Don't throw too many exceptions
- ==DO NOT== use a `try`/`catch` unless absolutely necessary
- Exceptions are much slower than normal code flow!
- ==DO== use the Tester-Doer Pattern or the Try-Parse Pattern to improve performance

## Tester-Doer Pattern
```c#
private static string ParseDateWithTryParse() 
{ 
	string dateTimeString = '2022-01-18'; 
	if (DateTime.TryParse(dateTimeString, out DateTime dateTime)) 
		dateTimeString = dateTime; 
	else 
		dateTimeString = "There is no valid date time value"; 
	return $" Final date time: {dateTimeString}"; 
}
```

##
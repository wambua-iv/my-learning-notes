
When returning errors from function return type definition should be the `error interface`


### Design Pattern for Errors

* Errors should be fully logged
* Avoid propagating errors [*passing error information up the call stack* so that the caller of a function or method can decide how to handle it] outside the functions handling them
	* Stop the error where its being created
	* Errors should provide context
	* Use Structured Error types
* 
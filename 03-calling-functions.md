# Calling functions

* Name lookup
* Template argument deduction
* Overload resolution
* Access check
* function template specialization
* virtual dispatch
* deleting functions

## Name lookup

There are two types of how specify a name of function or any value. If you reference a name, it must be existing prior the point of referencing. Names defined after the point of usage can't be seen. 

The result of name lookup is a set of considered functions and templates to be called.

### Unqualified lookup

Looking only in current or closest scope (downward).

#### Hiding functions

If there is a function with same name within any visible scope, the compiler will not look for lower namespaces.

### Qualified lookup

You need to specify explicit namespace to look there.

`ns::` ... specifies which namespace
`::` ... specifies global namespace 

#### Adding another scope to current scope for unqualified call

```
using ::name;
// or
using ns::name;
```

This will add an explicit name to the current scope for unqualified lookups.

### Argument dependent lookup

If one or more parameters of a functions contains a non-built-in type (for `std::` types defined in the standard library it works too). ADL will add the type's scope to the list of scopes to be inspected by the unqualified lookup.

ADL works only for unqualified lookups.

#### Two-Phase lookup

For templated function, the lookup rules for ADL are based on visibility from point of calling. It doesn't apply for built-in types.

## Template type deduction

When considering overload set of functions to be called, all templated function are added if their template arguments can be deduced.

## Overload resolution

Algorithm which will take at all functions from previous step, and look if the functions can be called (are viable candidates). At the end only one function should be selected, otherwise the name is *ambigious* (error).  

#### Viable candidates

* Exact match
* Trivial conversion
* Standard conversion
* User-defined conversion
* Default arguments
* Matching constraints (concepts)
* Ellipsis argument

#### Non-viable candidates

* Less parameters than arguments
* More parameters than arguments
* Without available conversion
* Violating constraints

### Finding the best match

#### Ranks of argument

* Rank 1
	* Exact match
	* Trivial conversion (adding const or reference)
* Rank 2
	* Promotion (possible with trivial conversion)
* Rank 3
	* Standard conversions (possible with trivial conversion)
* Rest
	* User-defined conversion 
		* with possible trivial conversion
		* with possible standard conversion
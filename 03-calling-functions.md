# Calling functions

The algoritm of selecting a function do step by step and never returns!

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
* Worst case
	* User-defined conversion 
		* with possible trivial conversion
		* with possible standard conversion
* Last resort
	* Ellipsis argument
	
Functions are sorted by rank, and if there is only such function which is in the best rank is selected, if there are multiple function, the call is *ambigous*.

#### Templated functions

Templated functions has a bit lower priority, in case of *ambigious* call, the non-template function should be selected.

## Access check

After function is selected, then compiler checks if the function is accessible from your current context. 

### Public methods

Methods and members available to anyone. `struct` types are public by default.

### Protected methods

Methods and members available only to friends and inherited types. `class` types are protected by default.

### Private methods

Methods and members available only to itself and friends.

### Friends 

Things marked `friend` can access internals of class even if there is an access specifier diallowing it.

#### Functions

You can mark function declaration inside a class scope, and provide definition outside of the class.

```c++
struct something {
	friend int get(something & s);
};

int get(something & s) {
	// ...
}
```

#### Classes

You can mark a class/struct as a friend, all member methods will be available to access the class internals.

```c++
struct something {
	friend struct very_close_friend;
	friend struct also_close_friend;
};

int get(something & s) {
	// ...
}
```

#### Methods

Methods marked `friend` are standalone function even if they are defined inside a class.

```c++
struct something {
    int a;
    
    friend int get(something & s) {
        return s.a;
    }
};
```

## Function template specialization

Template functions can be specialized. 
TODO: more

## Virtual dispatch

If a function is marked `virtual` a virtual table is created for the type. And dispatch of the function is than done dynamically during runtime (but it can be optimized). 

```c++
struct animal {
	virtual void make_sound() { beep(); }
};
```

If a type is inherited from original base type, it can provide it's implementation. 

```c++
struct dog: animal {
	virtual void make_sound() overload { woof(); }
};
```

Make sure you always mark function with `overload` if is your intention to overload the original. In case your function has a different signature the keyword `overload` will help by giving you an error. If the keyword is not present, you won't get an error (maybe it was your intention to provide new method).

You can't store different types in a container, you must make an effort and keep their lifetime somehow differently. 

```c++
std::vector<animal> vec; 
vec.push_back(dog{}); // will never work
```

This is happening because inherited type can have different size than base. Use `std::unique_ptr` instead.

To pass a polymorphic type to a function, always use reference or some pointer type:

```c++
void foo(animal & a) {
	a.make_sound(); // will call right make_sound based on real type
}
```

If you pass by value `void foo(animal a)` your type will be silently converted to `animal`.

### Abstract classes

You can mark a method abstract with `=0`:

```c++
struct animal {
	virtual void make_sound() = 0;
};
```

Which will block instantion of object of such type, and in order to use the interface `animal` (or so called "abstract" class) you need to inherit from it, and use inherited type instead.

## Deleting functions

If a function is marked deleted, you will get a compile-time error if it's selected.

```c++
void foo(int) = delete;
void foo(char) { ... }
```

Here if you call `foo('a')` it will call the `char` variant, but if you call it with an `int` you will get error. But if you didn't provide `void foo(int)` variant, it will be silently converted.



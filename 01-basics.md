# Basics

I do expect you to be at least familiar with C-like syntax.

## Values

## Lifetime

Object can have multiple types of lifetime:

* Expression (temporary)
* Scope
* Manually controlled lifetime on a heap
* Thread-local global<sup>*</sup>
* Lazy initialized global<sup>*</sup>
* Global<sup>*</sup>

<sup>*</sup> We will ignore these for now.

Lifetime specifies when the object is created and when it will be destroyed.

### Expression lifetime (temporary)

```c++
40 + 2; // this will create an integer value which will cease to exists at the end of expression
```

#### Value moving and copying

Value can't be moved, it's always created in their scope and you can only copy it (store) somewhere.

### Scope lifetime

```c++
int a = 40 + 2;
```

The temporary value 42 from previous example is now given name `a` and it's copied into the variable. Which will cease to exists at the end of scope.

#### Scope

Generally speaking anything inside current `{` `}` block or inside a condition or cycle implicit block.


### Manually controlled lifetime on a heap

In case you need to have a value to life longer than any available scope can provide, you can create the value on a heap. For that you need operators `new`/`delete` and you are responsible for controlling its lifetime.

```c++
int * a = new int{42};
//... some code
delete a;
```

It's useful when you know the size of the value is really big, but you still need to keep your pointer (handle pointing) to your value. 

<div style="color: red">Most modern C++ code doesn't need to use naked `new`/`delete` in code, and its considered anti-pattern in an application code.</div>

#### If you forget to call `delete` or if you loose pointer pointing at your value

You will get a leak, which can be serious problem if you have a lots of leaked memory. You should avoid such situation.

#### If you call `delete` multiple times for same object

Your application will probably crash, don't do that.

## Pointer and references

Pointer is marked with `*` (star) in its declaration:

```c++
int * a;
```

Which is read *pointer to int* named `a`. 

Value of the pointer is an address of the object in a memory, it's an opaque value and you shouldn't do any arithmetic with it, only in a special cases (which will be shown later).

Pointer can have a special value `nullptr` which says it doesn't point anywhere. To point to an object you need to take its address with `&` operator:

```c++
int a = 42;
int * b = &a; // a is pointing at b 
```

### References

Are special type of pointer which can't be changed once it's set. And it can't have `nullptr` value. So if you have a reference, you can be sure it's pointing on an object.

Reference must be set when it's created and it doesn't need operator `&` to do that:

```c++
int a = 42;
int & b = a; // b is referencing a
```

Reference can be considered an alias to the value it's pointing at, but it doesn't change lifetime of the pointed value.

### Dangling pointer / reference

Sometimes a reference / pointer can point at no-longer existing value. These pointer / references are considered *dangling* and it's not an error, unless you tried to access its content.

## Move semantics


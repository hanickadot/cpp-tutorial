#2: Types and implicit conversions

Each value has a type, it can be built-in type or user provided type.

## Build-in types

### Numerical types
* `bool` contains `true` or `false` value
* `int` contains a number which is signed and starting from C++20 is to be guaranteed to be based 2-complement (overflow is not defined)
* `float` contains a 32 bits precision floating value based on IEEE-754 
* `double` continss a 64 bits precision floating value based also on IEEE-754
* `signed` / `unsigned` is signess modifier to a type which can be used alone itself, and it that case it's implictly `int`
* `short` / `long` / `long long` size modifier to a type which can be used alone or with `int`, `float`, or `double`
	* `short` is at least 16 bits
	* `long` is at least 32 bits
	* `long long` is at least 64 bits

Standard specifies this:

```
1 == sizeof(char) <= sizeof(short) <= sizeof(int) <= sizeof(long) <= sizeof(long long)
```

### Character types

* `char` contains a 7bit character (on most platform it can hold 8bits, also the signess is not specified, but it's usually signed)
* `wchar_t` contains a "wide" character, which in theory should be enough to store a unicode, but different platforms implement it differently, avoid it if possible :)
* `char8_t` type designed to contain a UTF-8 code unit (part of multibyte character)
* `char16_t` type designed to contain a UTF-16 code unit
* `char32_t` type designed to contain a UTF-32 code unit

Character types can be modifier with `signed` / `unsigned`. Historically `unsigned char` was used to represent a byte, but you should use `std::byte` type instead.

### `void` type

A special type which doesn't contain anything, it's used in places where you need to provide syntactically a type, but you don't have any, for example functions which doesn't return anything.

### Pointer types

If you add one or more `*` (star) to a type specifier, you are not creating value of your type, or pointer to your type, and if your type was a pointer (with one or more stars) you creating a pointer to pointer :)

### Reference types

Basically a pointer but it can't contain `NULL` value, and it can't be rebind. In API it can be used as intent, that the value must exists and be provided. Also you can't do pointer arithmetic over reference. For most purposes a reference or a type with reference semantic can be imagine as a alias to a value. Both pointer and reference doesn't prolong the value lifetime. And it's UB to access a pointed value if it's past its lifetime.

### Additional numerical types

* `size_t` unsigned integral type which must contain largest allocable size (8 bytes on 64 bit platform, 4 bytes on 32 bit)
* `uintptr_t` a type which can contain a pointer type (it's same size as a pointer)
* ...

## User provided types

User can provide its own type by defining:

* enumerations, which gives a name to values
	* unscoped `enum`
	* scoped `enum class`
* structures, which binds multiple values together
	* `struct`
	* `class`
* or `union` which can one type from a specified sets of types

## Conversions

A type can be converted to another type implicitly or explicitly. It's considered safe to pass a value of type to a bigger type of same domain (`int` to `long`).

### Integral promotions

A conversion which results in change of representation but not its value.

* `signed char` or `signed short` can be converted to `int`
* `unsigned char`, `char8_t`, or `unsigned short` can be converted to `int`
* `char` can be converted to `int`
* `wchar_t`, `char16_t`, `char32_t` can be converted to a integer type if it can hold it's value
* unscoped enum types (`enum`) without fixed type can be converted to a integer type if it can hold it's value
* a bitfield can be converted to `int` if it can represent all possible values, or it can be converted to `unsigned int` with same rule, if the bitfield is bigger than both of the types, it can't be promoted
* `bool` can be converted to `int` (`false` is `0` and `true` is `1`)

#### Floating pointer promotion

* `float` can be converted to `double` without change of value

## Integral conversion

A conversion which results in change of value and its representation.

* conversion from `signed` type to `unsigned`, the resulting value is wrapped around with module 2<sup>n</sup> (where n is bit width of resulting type)
* conversion to `signed` type can result to change value as the `signed` type is 2-complement

#### Floating point conversion

* if a value can be represented in a different floating point type, it will be
* if a value is between two representable value, it will be rounded to nearest possible value (IEEE arithmetic needs to be supported, but generally it is)
* other conversions are undefined

#### Floating-Integral conversions

* a floating point can be converted to any integer type if the value is in boundaries of the target type, the fraction part is truncated (no rounding)
* if the value is outside of the limits of the integer type, behavior is undefined

#### Integer-Floating conversion

* if the integer value can be represented with the target floating type, it's converted and it's implementation defined if it will be rounder to closest higher or lower representable value

#### Boolean conversion

* Any value of integral, floating point, unscoped enumeration, pointer can be converted to a boolean value
* if the value is zero or `null`, it become `false`
* all other values became `true`

## Explicit conversion
 
When there is a loss of precession you should design you code, to mark all such conversion `explicit` so they can't be triggered automatically. It leads to much readable code.



# List

Let's implement our first data structer, a list.

To make it generic, so we won't need to implement a multiple list for each type, we will use `template`. Don't be scared :)

```c++
template <typename T> struct list {
	// ...
};
```

## What is the template?

It's a feature of C++, which allows you write code once, and use it multiple types, imagine the `T` is a placeholder for a type which will be used, instead of any specific type, we are doing it for **all** types.

```c++
list<int> l;
```

This is how we instantiate the list for integers.

## What do we want our list to do?

* insert a value at the begin of the list
* insert a value at the end of the list
* check if the list is empty
* look at first/last element of the list
* remove a value from the beginning or the end of the list

Also we should make sure how to manage life-time of the list, as it's content will be on heap.

## API

```c++
template <typename T> struct list {
	void insert_front(T item);
	void insert_back(T item);
	bool empty();
	T & front();
	T & back();
	void pop_front();
	void pop_back();
};
```

## Attributes

There is a thing called attributes in C++, you can mark a type or a function with it, and they are spelled `[[attribute]]`. They don't change semantic of the program, but it can help you write readable and safer program.

With attribute `[[nodiscard]]` you can mark function that caller must do something with its return value.

```c++
template <typename T> struct list {
	void insert_front(T item);
	void insert_back(T item);
	[[nodiscard]] bool empty();
	[[nodiscard]] T & front();
	[[nodiscard]] T & back();
	void pop_front();
	void pop_back();
};
```

This make sure, that no one will call `.empty()` method thinking it will erase the content of the structure. But it's just a check if the content is empty.

## const-ness

Sometimes you want to make sure someone won't modify your data structure when they are not allowed to do it. Mark some functions `const` because user is only allowed to call `const` methods if they got the data structure as a constant, or constant reference, or constant pointer.

```c++
template <typename T> struct list {
	void insert_front(T item);
	void insert_back(T item);
	[[nodiscard]] bool empty() const;
	[[nodiscard]] T & front() const;
	[[nodiscard]] T & back() const;
	void pop_front();
	void pop_back();
};
```

It's a best practice to mark all non-modifying methods `const`.

## TODO
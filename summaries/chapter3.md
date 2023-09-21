### Pointers 
- At run time, a reference to an i32 is a single machine word holding the address of the i32, which may be on the stack or in the heap.
- Rust references are never null
- Rust references come in two flavors
	- &T is an immutable, shared reference
	- &mut T is a mutable, exclusive reference 
- The simplest way to allocate a value in the heap is to use Box::new
```rust
let t = (12, "eggs");
let b = Box::new(t);
```
- Rust also has the raw pointer types *mut T and *const T.
- 
### Array, Slices and Vectors 
- The types &[T] and &mut [T], called a shared slice of Ts and mutable slice of Ts
- You'll see this syntax used for fixed-size buffers: [0u8; 1024]
	- a buffer is temporary storage for data
- Rust has no notation for an uninitialized array.
- Rust implicitly converts a reference to an array to a slice when searching for methods, so you can call any slice method on an array directly.
	- In fact, the len method we mentioned earlier is a slice method as well.
- Another possibility is to build a vector from the values produced by an iterator: 
```rust
let v: Vec<i32> = (0..5).collect();
assert_eq!(v, [0, 1, 2, 3, 4]);
```
- A Vec<T> consists of three values: a pointer to the heap-allocated buffer for the elements, which is created and owned by the Vec<T>;  the number of elements that buffer has the capacity to store; and the number it actually contains now(in other words, its length);
	- When the buffer has reached its capacity, adding another element to the vector entails allocating a larger buffer, copying the present contents into it, updating the vector's pointer and capacity to describe the new buffer, and finally freeing the old one.
	- If you know the number of elements a vector will need in advance, instead of `Vec::new` you can call `Vec::new_capacity` .... without causing any reallocations.
	- The `capacity` method tells returns the capacity of a vector
- Despite its fundamental role, `Vec` is an ordinary type defined in Rust, not built into the language.
- Since a slice can be any length, slices can't be stored directly in variables or passed as functions arguments. 
- Slices are always passed by reference;
- Whereas an ordinary reference is a non-owning pointer to a single value, a reference to a slice is a non-owning pointer to a range of consecutive values in memory.
	- This makes slices references a good choice when you want to write a function that operates on either an array or a vector.

### String Types 
- A string literal with the b prefix is a byte string.
```rust
// The type of method is &[u8; 3]
let method = b"GET";
assert_eq!(method, &[b'G', b'E', b'T']);
```
- Rust strings are sequences of Unicode characters, but tey are not stored in memory as arrays of chars.
	- Instead, they are stored using UTF-8, a variable-width encoding. 
	- Each ASCII character in a string is stored in one byte, other characters take up multiple bytes.
- A String has a resizable buffer holding UTF-8 text.
	- The buffer is allocated on the heap, so it can resize its buffer as needed.
	- You can think of a String as Vec<u8> that is guaranteed to hold well-formed UTF-8.
- Like other slice references, a &str is a fat pointer, containing both the address of the actual data and its length.
	- You can think of a &str as being nothing more than a &[u8] that is guaranteed to hold well-formed UTF-8
- A string literal is a &str that refers to preallocated text, typically stored in read-only memory along with the program's machine code.
	- A String or &str's .len() method returns its length and the length is measured in bytes, not characters.
- It is impossible to modify a &str 
- The type &mut str does exist, but it is not very useful, since almost any operation on UTF-8 can change its overall byte length.
	- And a slice cannot reallocate its referent
	- In fact, the only operations available on &mut str are `maek_ascii_uppercase` and `make_ascii_lowercase`, which modify the text in place and affect only single-byte characters, by definition.
- &str is very much like &[T]: a fat pointer to some data.
- String is analogous to Vec<T>
- Like a Vec, each String has its own heap-allocated buffer that isn't shared with any other String.
	- When a String variable goes out of scope, the buffer is automatically freed, unless the String was moved.
- &str is more appropriate for function arguments when the caller should be allowed to pass either kind of String 
- Strings support the `==` and `!=` operators and also the `<`, `<=` and `>=` as well
- When working with filenames, use `std::path::PathBuf` and `&Path` instead.
- When working with binary data that isn't UTF-8 encoded at all, use `Vec<u8>` and `&[u8]`.
- When working with environment variable names and command-line arguments in the native form presented by the operating system, use `OsString` and `&OsStr`.
- 


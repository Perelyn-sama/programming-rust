### Ownership
- Every value has a single owner that determines its lifetime.
	- A variable owns it value.
- A Box<T> is a pointer to a value of type `T` stored on the heap.
	- Calling `Box::new(v)` allocates some heap space, moves the value `v` into it, and returns a `Box` pointing to the heap space.
	- Since a Box owns the space it points to, when the `Box` is dropped, it frees the space too.
- Just as variables own their values, structs own their fields, and tuples, arrays, and vectors own their elements
- In a certain sense, Rust is less powerful than other languages: every other practical programming language lets you build arbitrary graphs of objects that point to each other in whatever way you see fit.
	- But it is exactly because Rust is less Powerful that the analyses the language can carry out on your programs can be more powerful.
- Very simple types like integers, floating-point numbers, and characters are excused from the ownership rules. These are called Copy types.
- Rust programs build up and tear down complex structures one value at a time, one move at a time.
- In Rust, assignments of most types move the value from the source to the destination, leaving the source uninitialized.
- Rust prudently prohibits using uninitialized values
- Like Python, the assignments is cheap: the program simply moves the value from one spot to another. But like C++, ownership is always clear: the program doesn't need reference counting of garbage collection to know when to free the value.
- If you move a value into a variable that was already initialized, Rust drops the variable's prior value.
- Passing arguments to functions moves ownership to the function's parameters
	- Returning a value from a function moves ownership to the caller
	- Building a tuple moves the values into the tuple.
- The moves always apply to the value proper, not the heap storage they own
	- For vectors and strings, the value proper is the three-word header alone
- If its possible for a variable to have had its value moved away and it hasn't definitely been given a new value since, it's considered uninitialized 
```
let x = vec![10, 20, 30];
if c {
	f(x); // ... ok to move from x here
} else {
	g(x); // ... and ok to also move from x here
}
h(x); // bad: x is uninitialized here if either path uses it
```
- When you put a variable into an if statement you are allowed to only to use it in its branches, if you try to use it outside the compiler with deem the variable uninitialized
- You cannot move a variable to a loop unless you can assign a new value for the variable at the end of each loop
- Moves leave its source uninitialized, as the destination takes ownership of the value
	- But not every kind of value owner is prepared to become uninitialized e.g a vec with its indexes 
- Collection types like Vec also generally offer methods to consume all their elements in a loop
- When we pass the vector to the loop directly, as in for ... in v, this moves the vector out of v, leaving v uninitialized.
```
let v = vec![
"liberté".to_string(),
"égalité".to_string(),
"fraternité".to_string(),
];

for mut s in v {
s.push('!');
println!("{}", s);
}

println!("{v:?}"); // Will result in an error because v in uninitialized 
```
- If you do find yourself needing to move a value out of an owner that the compiler can't track, you might consider changing te owner's type to something that can dynamically track whether it has a value or not. e.g an Option.
- The `take` method on the `Option` type can be used to move a value out of variable/index and leave `None` in its place.

### Copy Types
- An i32 is simply a patten of bits in memory; it doesn't own any heap resources or really depend on anything other than the bytes it comprises.
	- By the time we've moved its bits to `num2`, we've made a completely independent copy of `num1`
- Assigning a value of a Copy type copies the value, rather than moving it
	- The source of the assignment remains initialized and usable, with the same value it had before.
- The standard Copy types include all the machine integer and floating-point numeric types, the char and bool types, and a few others.
	- A tuple or fixed-size array of Copy types is itself a Copy type.
- Only types for which a simple bit for bit copy suffices can be Copy
	- String is not a copy type, because it owns a heap-allocated buffer.
	- For similar reasons, Box<T> is not copy; it owns its heap-allocated referent.
	- The File type, representing an operating system file handle, is not Copy; duplicating such a value would entail asking the operating system for another file handle 
	- The MutexGuard type, representing a locked mutex, isn't Copy: this type isn't meaningful to copy at all, as only one thread may hold a mutex at a time.
- As a rule of thumb, any type that needs to do something special when a value is dropped cannot be Copy
	- A Vec needs to free its elements 
	- A File needs to close its file handle
	- A MutexGuard needs to unlock its mutex
	- Bit for bit duplication of such types would leave it unclear which value was not responsible for the original's resources.
- Struct and enum types are not Copy types
- User-defined types being non-Copy is only the default 
	- If all the fields of your struct are themselves Copy, then you can make the type Copy as well by placing the attribute `#[derive(Copy, Clone)]` above the definition. 
	- But if the field of a struct is not a Copy type then you can't make copies of them
- Copy types are very limited in which types they can contain, whereas non-Copy types can use heap allocation and own other sorts of resources.
- In Rust, every move is a byte-for-byte, shallow copy that leaves the source uninitialized
	- Copies are the same, except that the source remains initialized

### Rc and Arc: Shared Ownership
- For any type T, an Rc<T> value is a pointer to a heap-allocated T that has ad a reference count affixed to it.
	- Cloning an Rc<T> value does not copy the T; instead, it simply creates another pointer to it, and increments the reference count
- The usual ownership rules apply to the Rc pointers themselves
- You can use any of type T's usual methods directly on an Rc<T>
- A value owned by an Rc pointer is immutable 
- Rust's memory and thread-safety guarantees depend on ensuring that no value is ever simultaneously shared and mutable.
- 
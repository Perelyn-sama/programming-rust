- All the pointer types we've seen so far, the simple Box<T> heap pointer, and the pointers internal to String and Vec values -- are owning pointers: when the owner is dropped the referent goes with it
	- Rust also has non-owning pointer types called references, which have no effect on their referent's lifetimes.
- In fact, it's rather the opposite: references must never outlive their referents.
- References themselves are nothing special under the hood, they're just addresses.
- A shared reference lets you read but not modify its referent.
	- However, you can have as many shared references to a particular value at a time as you like
	- Shared references are Copy
- If you have a mutable reference to a value, you may both read and modify the value
	- However, you may not ave any other references of any sort to that value active at the same time.
	- Mutable references are not Copy
- You can think of the distinction between shared and mutable references as a way to enforce a multiple readers or single writer rule at compile time.
	- As long as there are shared references to a value, not even its owner can modify it; the value is locked down.
	- If there is a mutable reference to a value, it has exclusive access to the value; you can't use the owner at all, until the mutable reference goes away.
- When we pass a value to a function in a way that moves ownership of the value to the function, we say that we have passed it by value.
	- If we instead pass the function a reference to the value, we say that we have passed the value by reference.
- In Rust, references are created explicitly with the`&`  operator and dereferenced explicitly with the `*` operator.
- The `.` operator implicitly dereferences its left operand
	- The println! macro used in the show function expands to code that uses the `.` operator, so it takes advantage of this implicit dereference as well.
-  The `.` operator can also implicitly borrow a reference to its left operand, if needed for a method call.
- In Rust you use the `&` and `*` operators to create and follow references, with the exception of the `.` operator, which borrows and dereferences implicitly.
- The `.` operator follows as many references as it takes to find its target 
- Rust's comparison operators "see through" any number of references 
- If you actually want to know whether two references point to the same memory, you can use `std::ptr::eq`, which compares them as addresses
- Rust references are never null
- In Rust, if you need a value that is either a reference to something or not, use the type Option<&T>
- At the machine level, Rust represents None as a null pointer
### Borrowing references to arbitrary expressions 
- Rust lets you borrow a reference to the value of any sort of expression at all
```
fn factorial(n: usize) -> usize {
(1..n+1).product()
}

let r = &factorial(6); // Arithmetic operators can see through one level of references.
```
- In situations like this, Rust simply creates an anonymous variable to hold the expression's value and makes the reference point to that

### References to slices and trait objects
- Rust also includes two kinds of fat pointers 
	- two-word values carrying the address of some value, along with some further information necessary to put the value to use
	- A reference to a slice is a fat pointer, carrying the starting address of the slice and its length.
	- Rust's other kind of fat pointer is a trait object, a reference to a value that implements a certain trait.
	- A trait object carries a value's address and a pointer to the trait's implementation appropriate to tat value, for invoking the trait's methods.
- Aside from carrying this extra data, slice and trait object references behave just like the other sorts of references:
	- They don't own their referents 
	- They are not allowed to outlive their referents 
	- They may be mutable or shared 

- Rust's equivalent of a global variable is called a static: it's a value that's created when the program starts and lats until it terminates.
- Every static must be initialized
- Mutable statics are inherently not thread-safe(after all, any thread can access a static at any time)
	- You may access a mutable static only within an unsafe block 
- `fn f<'a>(p: &'a i32) {....}` 
	- Here, the lifetime 'a (pronuced "tick A") is a lifetime parameter of f.
	- You can read `<'a>fn f<'a>(p: &'a i32)`, we’re defining a function that takes a reference to an i32 with any given lifetime 'a.
- In Rust, a function's signature always exposes the body's behavior.
- When a function takes a single reference as an argument and returns a single reference, Rust assumes that the two must have the same lifetime
- Lifetimes in function signatures let Rust assess the relationship between the references you pass to the function and those the function returns, and they ensure they're being used safely.
- If there's only a single lifetime that appears among your function's parameters, then Rust assumes any lifetimes in your return value must be that one.



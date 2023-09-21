- Rust is what is called an expression language 
- In Rust, if and match can produce values
	- An if statement can be used to initialize a variable
	- A match expression can be passed as an argument to a function or macro
- All of the operators that can usefully be chained are left-associative 
	- That is, a chain of operators such as `a - b - c` is grouped as ( a - b ) - c, not a - ( b - c ).
	- The comparison operators, the assignment operators, and the range operators `..` and `..=` can't be chained at all.
	
- Blocks are the most general kind of expression
	- A block produces a value and can be used anywhere a value is needed.
```rust
let msg = {

// let-declaration: semicolon is always required
let dandelion_control = puffball.open();

// expression + semicolon: method is called, return value dropped
dandelion_control.release_all_seeds(launch_codes);

// expression with no semicolon: method is called,
// return value stored in `msg`
dandelion_control.get_status()

};
```
- A block can also contain item declarations. 
	- An item is simply any declaration that could appear globally in a program or module, such as a `fn` , `struct` or `use`.
- When a `fn` is declared inside a block, its scope is the entire block 
	- but a nested `fn` cannot access local variables or arguments that happen to be in scope.
- A block can even contain a whole module
- In a if expression, each condition must be an expression of type bool
	- An if expression with no else block behaves exactly as thought it had an empty else block 
- Placing a `_` pattern before other patterns means that it will have precedence over them in the match expression
- The general form of a match expression is 
```rust
match value {
	pattern => expr,
	...
}
```
- Rust checks the given value against each patten in turn starting with the first. When a patten matches, the corresponding `expr` is evaluated, and the `match` expression is complete 
	- At least one of the patterns must match
	- Rust prohibits `match` expressions that do not cover all possible values
- All blocks of an `if` expression must produce values of the same type 
	- Similarly, all arms of a `match` expression must have the same type.
- There is one ore if form, the if let expression 
```rust
if let pattern = expr {
	block1
} else {
	block2
}
```
- It never strictly necessary to use `if let`, because match can do everything `if let` can do. 
	- An `if let` expression is shorthand for a `match` with just one pattern.
- There are four looping expressions
```rust 
while condition {
	block
}

while let pattern = expr {
	block
}

loop {
	block
}

for pattern in iterable {
	block
}
```
- Loops are expressions in Rust, but the value of a `while` or `for loop` is always ()
- The `while let` is analogous to `if let`. 
	- At the beginning of each loop iteration, the value of expr matches the given pattern, in which case the block runs, or doesn't, in which case the loop exits.
- Use `loop` to write infinite loops
	- It executes the block repeatedly forever (or until a break or return is reached or the thread panics)
- `0..20` is the same as `std::ops::Range { start: 0, end: 20 }`
- The standard collections are all iterable, as are arrays and slices
- A break expression exits an enclosing loop. 
	- In Rust, break works only in loop
	- It is not necessary in match expressions which are unlike switch statements in this regard
	- Within the body of a loop, you can give `break` an expression, whose value becomes that of the loop
- A `continue` expression jumps to the next loop iteration 
	- In a `for` loop, `continue` advances to the next value in the collection
	- In a `while loop` , `continue` rechecks the loop.
- A loop can be labelled with a lifetime 
- A return expression exits the current function, returning a value to the caller.
	- `return` without a value is shorthand for `return ()`
- Expressions that don't finish normally are assigned the special type `!`
	- You can see `!` in the function signature of `std::process:exit()`
	- The `!` means that exit() never returns. It's a divergent 
- Rust's `.` operator automatically dereferences a type or borrows a reference to it as needed.
- A third syntax is used for calling type-associated functions, like `Vec::new()`
	- Ordinary methods are called on values like `my_vec.len()` and type associated functions are called on types like `Vec::new()`
- If the value to the left of the dot is a reference or smart pointer type, it is automatically dereferenced, just as for method calls.
	- e.g `game.black_pawns` // struct field 
	- e.g `coords.1` // tuple element 
- `peices[i]` // array element 
	- The value to the left of the bracket is automatically dereferenced
- Expressions like the ones above are called `Ivalues` because they can appear on the left side of an assignment
- Integer division rounds toward zero, and dividing an integer by zero triggers a panic even in release builds.
- `-` negates a number. It is supported for all the numeric types except unsigned integers
- Note that `%` can be used on floating-point numbers as well as integers 
- Rust uses `!` instead of `~` for bitwise **Not**
- The purpose of `Deref` coercion is to make smart pointer types like `Box`, behave much like the underlying value as possible.
- If you specify a return type for a closure, then the body of the closure must be a block
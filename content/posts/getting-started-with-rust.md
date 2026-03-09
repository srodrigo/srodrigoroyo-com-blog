---
_cmplz_remote_scanned_post: "1"
_cmplz_scanned_post: "1"
_edit_last: "1"
_thumbnail_id: "830"
author: wpx_srodrigoroyo_admin
categories:
  - programming
cmplz_hide_cookiebanner: ""
cover:
  alt: getting-started-with-rust
  image: /wp-content/uploads/2023/03/getting-started-with-rust-scaled.jpg
date: "2023-03-22T16:38:58+00:00"
guid: https://srodrigoroyo.com/?p=783
parent_post_id: null
post_id: "783"
rank_math_analytic_object_id: "18"
rank_math_focus_keyword: getting started with rust
rank_math_internal_links_processed: "1"
rank_math_primary_category: "6"
rank_math_seo_score: "82"
title: 'Getting Started With Rust: A Simplified Hands-on Guide'
url: /getting-started-with-rust/

---
In a previous [Introduction To Rust](/introduction-to-rust), we discussed what Rust is about. In this article, we will go through a hands-on guide on how to get started with Rust. This isn't meant to be an exhaustive guide, but a place for Rust newcomers to start. I changed the course halfway (see confession below) to make it more engaging. I hope I succeeded 🙂

## **How to install Rust**

Before getting our hands dirty with Rust, we need to install Rust and its tooling on our computers. Since the Rust team even provides a [tool](https://www.rust-lang.org/tools/install) to install Rust depending on your operating system, I won't repeat the wheel here. Follow the official guide, it is pretty good.

## **Getting started with Rust**

Now, we are finally going to write some code (that’s what we are here for, right?).

Full disclaimer: I have to confess that I rewrote this section entirely. I wasn’t happy with a boring summary of other resources. It was even painful to write. So I spared you the pain of reading it and turned it into a less thorough but hopefully more engaging piece. I hope you will agree with me!

Let’s roll our sleeves and get hands-on.

### **Hello World**

I refuse to use the classical `Hello World`, so we will go with a `Hello Rust` here (consider this a Hello World 1.1, or something like that 🙂).

First, we need to create a new rust project. For this task, we can use `cargo`, Rust’s package manager. `cargo new` is the `cargo` command that creates a new project for us.

Go ahead and open a terminal, then type the following:

ShellScript

```
cargo new hello_rust
```

Cargo will create a new `hello_rust` folder with the new project source code. If we inspect the project, we should see something like this:

```
hello_rust/

├── Cargo.toml

├── src/

│ └── main.rs
```

`Cargo.toml` is the file where `cargo` defines the project package and all its dependencies, among other things.

For now, we are interested in `main.rs`. Go ahead and open this file in NeoVim your favorite editor.

As you can see, there is already a `Hello, world` program.

Rust

```
fn main() {
    println!("Hello, world!");
}
```

The first thing we see in this file is `fn main`. Rust defines a function with the `fn` keyword. The main function in a Rust program is called, well, `main`. It takes no parameters.

The next interesting line is `println!("Hello, Rust!");`, Rust’s [macro](https://doc.rust-lang.org/std/macro.println.html) to output a line to the standard output. For the sake of simplicity, let’s say that `println!` takes one string parameter. Notice that the macro needs to be called with `!` (bang) to expand the macro. If this doesn’t make sense yet, we will look into macros in detail later.

For now, just _fix_ the program by replacing `world` with `Rust`.

Rust

```
fn main() {
    println!("Hello, Rust!");
}
```

On the terminal, type the following to run our improved version of the program:

ShellScript

```
cargo run
```

If everything went okay, we should see a `Hello, Rust!` message.

Achievement unlocked! You have written your first program in Rust.

### **Rust’s basic constructs**

Our `Hello Rust` is a good start, but we need something a bit more substantial if we want to master Rust.

One thing to note is that Rust uses semicolons at the end of each line (yay!). Unfortunately, there are a few exceptions where semicolons are omitted. In my view, this was done with the best intentions (brevity) but it creates unnecessary confusion.

Having said that, let’s explore some of the basic constructs in Rust.

#### **Variables**

Variables in Rust are similar to the ones in other programming languages, such as C++ or TypeScript. To create a variable, just prefix it with `let`, then assign a value.

Let’s take our `Hello Rust` program and extract the string on `line 2` into a variable:

Rust

```
fn main() {
    let message = "Hello, Rust!";

    println!("{message}");
}
```

If we run the program, it should print out the exact same message as before.

Rust favors immutability by default. Re-assigning a variable will result in an error. Just for fun and exploration, let’s replace our `message` variable with the following:

Rust

```
fn main() {
    let message = "Hello, Rust!";
    message = "Hello, C++ (huh!)";

    println!("{message}");
}
```

After some warnings, we should see an error:

```
error[E0384]: cannot assign twice to immutable variable `message`
 --> src/main.rs:3:5
  |
2 |     let message = "Hello, Rust!";
  |         -------
  |         |
  |         first assignment to `message`
  |         help: consider making this binding mutable: `mut message`
3 |     message = "Hello, C++ (huh!)";
  |     ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ cannot assign twice to immutable variable
```

What Rust is telling us is that `message` is immutable, so we cannot re-assign it. The way to fix this issue is to declare the variable as mutable. We can do this in Rust by adding the `mut` keyword in front of the variable name:

Rust

```
fn main() {
    let mut message = "Hello, Rust!";
    message = "Hello, C++ (huh!)";

    println!("{message}");
}
```

The code above compiles, meaning we can now re-assign `message` to the new string.

Another thing to note is the naming convention. Variables in Rust use the `snake_case` convention. For example: `let my_message = "Held by a loooong variable";`

One interesting thing to mention is that Rust can infer the type of the variable that we just declared. `let message = "Hello, Rust!";` is enough for Rust to figure out that the type of this variable should be `&str`.

NOTE: If you are wondering what `&str` means, it is a reference ( `&`) to an `str` primitive type (a _string_, to simplify things). The reason it needs to be a reference is beyond the scope of this introductory article, but we’ll get there eventually.

If we want, we can specify the type, by adding `&str` after the variable name: `let message: &str = "Hello, Rust";`.

This flexibility is nice to have. Short-lived variables don’t benefit much from having the explicit type declared, but long-lived variables can be easier to follow if they have it.

The last trick to look at is **shadowing**. In Rust, you can re-declare a variable, effectively shadowing it. Example:

Rust

```
fn main() {
    let message = "Hello, Rust!";
    let message = "I got a new value!";

    println!("Hey, {message}");
}
```

The snippet above will print `Hey, I got a new value!` out. The second declaration of `message` shadows the first one. Note the `let` keyword on `line 2`.

Shadowing can be a useful feature for short scopes. However, I recommend being careful and using it sparingly, as it tends to cause more confusion than clarity.

#### **Constants**

Rust provides a terser way to store immutable data, called `constants`. They are similar to immutable variables, but constants:

- Are declared with `const` instead of `let`. For example: `const PI: f32 = 3.14159;`
- Require the type to be specified.
- Need to be computed at compile-time, so they cannot store values computed at runtime.
- Can be declared at the global scope (I wouldn’t say this is best practice, but you can do it).
- Cannot be mutated, even with `mut`.

Let’s go ahead and modify our program to replace the immutable variable with a constant (yes, constants work with `str` references).

Rust

```
fn main() {
    const MESSAGE: &str = "Hello, Rust!";

    println!("{MESSAGE}");
}
```

#### **Data Types**

Rust is a statically-typed language, so types are known at compile-time (including the `dyn` dynamic type, but that one is for a future article).

As we have seen in our `Hello Rust` example, Rust provides **type inference**, letting developers omit types under certain conditions. This is a list of things that Rust can infer the type from:

##### **Variables**

Rust can infer numeric, boolean, and string types, as we have already seen.

##### **Generic types**

Inferred based on the type of the values passed into the generic types. `let array: Vec<_> = (0..10).collect();` means that `array` will be of some specific `Vec` type that will be inferred from the right hand of the assignment.

NOTE: Remember that `Vec<i32>` and `Vec<f64>` are two entirely different types in Rust.

##### **Expressions**

The return value of control-flow statements. For example, `let result: bool = if n == 3 { true } else { false };` can be written as `let result = if n == 3 { true } else { false };`.

As a reminder, Rust cannot infer `constant` types.

#### **Control flow**

Rust control flow constructs are what you would expect if you come from a language such as C++. There are some differences though.

A few things to note about if-else statements and loops:

- They are _expressions_ in Rust, and their value can be assigned to a variable.
- Parentheses are usually omitted unless necessary to group conditionals.

#### **If-else statements**

Your typical if-else statement in many other programming languages.

Rust

```
fn main() {
    let n = 3;

    if n % 2 == 0 {
        println!("true!")
    } else {
        println!("false!")
    }
}
```

The most eventful thing in this example is the lack of parentheses to wrap the condition tested on `line 4`.

#### **Loops**

Basic loops in Rust resemble loops in C++:

Rust

```
fn main() {
    let mut index = 1;

    while index <= 10 {
        println!("Iteration {index}");
        index += 1;
    }
}
```

As you would expect, the example above prints the value of `index` from `1` to `10`. The variable has to be declared as mutable, as it gets incremented inside the `while` block.

`while` predicates also support _patterns_. For example, we can iterate over an array, popping its elements, one by one.

Rust

```
fn main() {
    let mut array = vec![1, 2, 3, 4, 5];

    while let Some(element) = array.pop() {
        println!("Popped element {element}");
    }
}
```

`line 4` is where the magic happens:

1. `pop` returns a `Result`.
1. The loop keeps running until `pop` returns `None`, meaning no elements are left.
1. `Some` will provide the value of the `element`.

This is a more concise way of exhausting the elements in an array, and less error-prone than the classical `for (initialization, condition, increment)` loop where it is easy to use the wrong `condition`.

Rust also provides another intriguing way of looping: **iterators**. Another way of rewriting our last example is:

Rust

```
fn main() {
    let array = vec![1, 2, 3];

    for element in array {
        println!("Reading element {element}");
    }
}
```

This new version uses an _iterator_. I you aren't familiar with iterators, think of them as a compact way to traverse the elements in an array, or other _iterable_ types. The good thing about our new version is that it doesn't mutate the array.

You can even create [your own iterators](https://doc.rust-lang.org/stable/std/iter/trait.Iterator.html). We'll leave explaining iterators in detail for another day.

#### **Functions**

Functions in Rust are similar to the ones in languages such as Scala or TypeScript.

Here is an example:

Rust

```
fn multiply(x: i32, y: i32) -> i32 {
    x * y
}

fn main() {
    let result = multiply(3, 5);

    println!("3 * 5 is {result}");
}
```

- Parameters specify a type after the variable name ( `line 1`).
- Return types have to be specified, unless the function does not return a value ( `line 1`).
- The `return` keyword can be omitted to return the result of the function ( `line 2`).
- To call a function, we pass its required parameters inside parenthesis ( `line 6`).

#### **Comments**

Comments in Rust are preceded with `//`, as in Java, C++, or JavaScript. No surprise here.

Rust

```
fn main() {
    // This is a very much needed comment. Or maybe not o__o
    println!("Hello, Rust!");
}
```

### **Ownership**

WARNING: I couldn’t find a way to explain _ownership_ without a longer text introduction. Apologies in advance!

We won’t go very deep into this section, as ownership deserves a whole article (or rather a couple of them). But we will still play around with some code, to get a grasp of it.

Rust’s ownership model is the power stone of what Rust brings to the table. **The ownership model enables programs that are**:

- **Memory-safe**
- **Free from race conditions**

Oh, I forgot to mention: **at compile time**. Yeah, Rust won’t compile if you introduce a memory leak or a race condition.

This is a game-changer. Say goodbye to two of the worst programmer’s nightmares. Well, unless you use `unsafe`, then you can keep having fun ;)

Fireworks and confetti aside, I will try to explain ownership in a simple way that gives us a general, shallow idea.

Every variable has a **lifetime**. For example:

- A variable declared at the beginning of the `main` function lives until the end of the program.
- A variable declared inside a function lives until the end of a function.
- A variable declared inside an inner scope block ( `{ … }`) lives until the end of the block.

Basically, a variable lives for as long as the scope where it is declared. Rust enforces that a piece of code that doesn’t own a variable doesn’t use the variable after its lifetime has expired. The **borrow checker** is the mechanism that performs these checks.

The final pieces are the concept of **ownership** (who owns a variable) and **borrowing** (who asks the owner to _borrow_ a variable for a while):

- Ownership can be transferred from one variable to another.
- Borrowing is a different way of using a variable without getting actual ownership.

Let’s try an example:

Rust

```
fn main() {
    let first = 3;
    let second = first;

    println!("first {first}");
    println!("second {second}");
}
```

This program works as expected, printing both variables. This is because `second` **copies** the value in `first`. Basic types are assigned by copy.

Now, let’s create a `String` and try to do the same:

Rust

```
fn main() {
    let first = String::from("hey!");
    let second = first;

    println!("first {first}");
    println!("second {second}");
}
```

The compiler complains on `line 5`:

```
error[E0382]: borrow of moved value: `first`
 --> src/main.rs:5:22
  |
2 |     let first = String::from("hey!");
  |         ----- move occurs because `first` has type `String`, which does not implement the `Copy` trait
3 |     let second = first;
  |                  ----- value moved here
4 |
5 |     println!("first {first}");
  |                      ^^^^^ value borrowed here after move
  |
  = note: this error originates in the macro `$crate::format_args_nl` which comes from the expansion of the macro `println` (in Nightly builds, run with -Z macro-backtrace for more info)
help: consider cloning the value if the performance cost is acceptable
  |
3 |     let second = first.clone();
  |                       ++++++++
```

A lot of new information here.

Rust talks about `first` being _moved_. Types that aren’t primitive types don’t have copy capabilities by default. Therefore, Rust changes the ownership of the variable. On `line 3`, `second` is the owner of the `String` declared on `line 2`.

What happens next is that `line 5` tries to use `first`, but this variable doesn’t own the `String` anymore. So Rust lets us know and stops us from blowing the program up at runtime.

To fix the program above, we can do the following:

Rust

```
fn main() {
    let first = String::from("Hey!");
    let second = &first;

    println!("first {first}");
    println!("second {second}");
}
```

Notice the `&` on `line 3`. `second` doesn’t get the ownership of the value held by `first`, but asks to **borrow a** **reference** instead. `first` is still the owner of the `String`, so it can be printed out on `line 5`. Finally, `second` can safely be used on `line 6` because it holds a reference to the `String`.

There are many nuances regarding _ownership_. As mentioned earlier, _ownership_ deserves a dedicated article, and going through it in detail is way out of the scope of this first contact with Rust.

### **Pattern Matching**

Pattern Matching is one of the most powerful tools we developers could ask for. In particular, **exhaustive pattern matching** can be a lifesaver, forcing us to handle all cases explicitly instead of implicitly (a.k.a. forgetting to handle them, in some cases).

Rust offers a very advanced pattern matching toolset. Among others, you can match: literals, arrays, wildcards, and even placeholders. Pattern matching deserves an in-depth article, but we can take a quick look at it to get an idea.

We can use the most basic pattern matching in `if` statements:

Rust

```
fn main() {
    let optional = Some(3);

    if let Some(value) = optional {
        println!("Has value {value}");
    } else {
        println!("Has no value");
    }
}
```

If we execute the code above, we will get `Has value 3` printed out to the console. Rust _matches_ the `optional` variable, then it figures out whether it is `Some` or `None`.

NOTE: The `Option` type in Rust can be either `Some` or `None`. It is a great way of avoiding the need for _Null_.

The above example is non-exhaustive pattern matching. In fact, nothing stops us from removing the `else` statement, effectively not handling the `None` case branch.

But Rust can handle exhaustive pattern matching as well.

Rust

```
fn main() {
    let optional = Some(3);

    match optional {
        Some(value) => println!("Has value {value}"),
        None => println!("Has no value"),
    }
}
```

This example is exhaustive pattern matching. If we try to remove the `None` branch, we will get an error telling us to include `None`:

Rust

```
fn main() {
    let optional = Some(3);

    match optional {
        Some(value) => println!("Has value {value}"),
    }
}
```

```
error[E0004]: non-exhaustive patterns: `None` not covered
   --> src/main.rs:4:11
    |
4   |     match optional {
    |           ^^^^^^^^ pattern `None` not covered
    |
note: `Option<i32>` defined here
   --> /home/srodrigo/.rustup/toolchains/nightly-x86_64-unknown-linux-gnu/lib/rustlib/src/rust/library/core/src/option.rs:566:5
    |
562 | pub enum Option<T> {
    | ------------------
...
566 |     None,
    |     ^^^^ not covered
    = note: the matched value is of type `Option<i32>`
help: ensure that all possible cases are being handled by adding a match arm with a wildcard pattern or an explicit pattern as shown
    |
5   ~         Some(value) => println!("Has value {value}"),
6   ~         None => todo!(),
    |
```

You can imagine the issues that can be avoided with exhaustive pattern matching.

Rust’s pattern matching is very flexible, and we can even mix different types in the predicates. Going into detail is out of the scope of this article.

### **Error-Handling**

Rust has a modern way of handling error handling. When a function can error, it returns a [Result](https://doc.rust-lang.org/std/result/) type. If you are familiar with Functional Programming, `Result` is equivalent to `Either` where:

- The **left** (or first) _variant_ (think of a variant as a _type_, to make it easier to understand) is `Ok` (meaning _everything went well, here is the result value_)
- The **right** (or second) one is `Err` (meaning _something went wrong, here is the error_).

NOTE: `Either` variants could potentially represent other things, but `Result` constrains them to `<Happy, Error>` kind of variant.

Let’s see an example:

Rust

```
fn main() {
    let result: Result<i32, std::io::Error> = Ok(3);

    match result {
        Ok(value) => println!("The value is {value}"),
        Err(error) => println!("Ops! An error occurred: {error}"),
    }
}
```

Here, we are creating an `Ok` result that contains an `i32`. We can manipulate `Result` types with pattern matching, similar to the `Option` type we studied in a previous section. This is good because we have to handle the error; otherwise, the compiler will complain.

NOTE: Under the hood, `Result` is an enum that can be either `Ok` or `Err`, each taking a variant to specify the type we want.

We can also _unwrap_ the value or a `Result` without handling the error explicitly. This is sometimes convenient when errors cannot be recovered, and we just want to write our flow a bit more concisely. For example, calling `parse::<i32>` on a string will return a `Result<i32, ParseIntError>`. We can try calling `unwrap` to get the value inside `Ok` if we are confident or if the error is unrecoverable anyway.

Rust

```
fn main() {
    let number = "sorry, not a number".parse::<i32>().unwrap();

    println!("Parsed number {number}");
}
```

The above code errors because the string cannot be parsed into an `i32`. Since `Err` is not handled, Rust will _panic_, then the program will terminate.

NOTE: I would typically handle the above error properly, as it probably doesn't stop us from continuing our logic (it depends on the context). But `parse` is a pretty simple example, so it does the job for illustration purposes. Errors such as being unable to initialize the window on a GUI application are more problematic and can justify letting Rust _panic_.

We can also extend from the [Error](https://doc.rust-lang.org/std/error/trait.Error.html#) trait when we want to handle custom errors.

Rust also has a `panic!` macro, which is a way of aborting the program explicitly under unrecoverable circumstances,

Rust

```
fn main() {
    panic!("Sorry, not in the mood today...");
}
```

giving us a nice termination error:

```
thread 'main' panicked at 'Sorry, not in the mood today...', src/main.rs:2:5
```

## **Conclusion**

Rust is a mix of imperative and functional programming. Actually, Rust is closer to [ML languages](https://en.wikipedia.org/wiki/ML_(programming_language)) than to languages such as C++ or Java. This makes Rust a non-trivial programming language, with concepts that aren’t necessarily simple to grasp.

The Rust compiler is both our friend and our teacher. As a good friend, it won’t let us shoot our feet. But, as a good teacher, it will be unforgiving. Believe me, this is for good.

We have only scratched the surface of what we can do in Rust. There is so much more that we could talk about. But this is a beginner’s introduction, I never aimed to cover the whole programming language. Maybe in future articles 🙂

Happy Rusting!

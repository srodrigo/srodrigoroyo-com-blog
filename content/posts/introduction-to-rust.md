---
_cmplz_remote_scanned_post: "1"
_cmplz_scanned_post: "1"
_edit_last: "1"
_ez-toc-alttext: ""
_ez-toc-disabled: ""
_ez-toc-exclude: ""
_ez-toc-heading-levels: []
_ez-toc-insert: ""
_thumbnail_id: "732"
author: wpx_srodrigoroyo_admin
categories:
  - programming
cmplz_hide_cookiebanner: ""
cover:
  alt: introduction-to-rust
  image: /wp-content/uploads/2023/03/introduction-to-rust.png
date: "2023-03-16T15:00:00+00:00"
guid: https://srodrigoroyo.com/?p=727
parent_post_id: null
post_id: "727"
rank_math_analytic_object_id: "17"
rank_math_focus_keyword: introduction to rust
rank_math_internal_links_processed: "1"
rank_math_primary_category: "6"
rank_math_seo_score: "80"
title: A Comprehensive Codeless Introduction To Rust
url: /introduction-to-rust/

---
Rust is a relatively new programming language that has gained the right to be the **most loved programming language** on [StackOverflow surveys](https://survey.stackoverflow.co/2022/?utm_source=so-owned&utm_medium=announcement-banner&utm_campaign=dev-survey-2022&utm_content=results#section-most-loved-dreaded-and-wanted-programming-scripting-and-markup-languages) for a few consecutive years. In this codeless introduction to Rust, we will explore what all the fuss is about.

Rust started as an idea after a [frustrating](https://www.techspot.com/news/97654-how-broken-elevator-led-one-most-loved-programming.html) experience after a long work day. I can understand why Graydon Hoare ( [@graydon\_pub](https://twitter.com/graydon_pub)) opened his laptop and started designing a new programming language that helps people write code that does not mess up our beloved elevators.

## What is Rust?

Rust is an expressive, modern, safe, and fast programming language that has been gaining popularity among developers in recent years. Rust appeared in the programming scene for the first time in 2010, after the hundreds of steps the author climbed unexpectedly. While Rust shines in Systems Programming, and it often classifies as a Systems Programming language, it is, in reality, a **general-purpose programming language** used in a wide range of areas such as Web Development or Game Development.

Since its humble start, Rust has evolved into a mature and robust language that is a game changer in the programming world. I can say, without a doubt, that **Rust is one of the most important pieces of technology in the last 20 years**. Many programming languages have seen the light over the decades, but most were similar to one of the already established mainstream languages. Rust did not invent most of the concepts it builds on, but it merges them in a way that tackles actual problems developers face, in a modern way that lives up to today’s standards.

## What are Rust’s key features?

### 1\. Reliable Memory Safety

The world of Systems Programming is plagued with **memory-related errors** such as buffer overflows, null pointer dereferences, or use-after-free bugs. Horror stories can be found on the internet, relating pain and suffering. Those kinds of errors are among the most annoying and difficult to find and fix.

Rust provides mechanisms to avoid the above errors, which cause crashes, security vulnerabilities, and similar programmers’ nightmares. More importantly, it achieves this **at compile time**:

1. **Ownership and Borrowing**: One way to avoid two pieces of code incorrectly using references at once is to define an owner and borrowers. There can only be one owner of a value at a time. Borrowers can either take ownership or use a reference, but only one can be mutable simultaneously. This ownership model is the primary pillar of Rust’s memory safety.
1. **Lifetimes**: Rust uses lifetimes to enforce that references exist for as long as they are borrowed.
1. **Null Safety**: There is no such thing as Null in Rust ( [for good](https://www.infoq.com/presentations/Null-References-The-Billion-Dollar-Mistake-Tony-Hoare/)). Instead, Rust uses an [Option type](https://en.wikipedia.org/wiki/Option_type) to ensure that programs cannot dereference null pointers.
1. **Safe Wrappers**: Rust allows programmers to write unsafe code in a way that is isolated from the rest of the code. The `unsafe` keyword helps find suspicious but sometimes necessary code that can cause trouble.

### 2\. Blazing Fast Performance

**Rust is fast**, for real. And it does this without letting you shoot yourself in the foot (looking at you, C and C++). What does this mean?

1. **Zero-cost abstractions**: Rust provides high-level abstractions that compile into efficient machine code. There is no need for cryptic code that only your computer will understand.
1. **Control over memory allocation**: Developers can fine-tune the memory usage in their code, which allows for optimizations that help achieve the desired performance.
1. **Safe concurrency**: The ownership model helps write safe programs at compile time. More on this later.
1. **Small runtime**: Rust brings very little overhead to the table. That is important in environments with constrained resources, such as embedded devices.

### 3\. Fearless Concurrency and Parallelism

Rust allows developers to write concurrent programs aggressively without worrying about race conditions, deadlocks, other rabbit holes, or gray hair makers. In the same way, as per memory safety, Rust enforces **correctness at compile time**. It achieves this via:

1. **Threads**: Rust provides a threads library that features lightweight abstractions.
1. **Ownership and Borrowing**: It allows multiple threads to access the same data safely, freeing developers up from most synchronization concerns.
1. **Channels**: Threads communicate via an asynchronous channel following a Sender/Receiver pattern.
1. **Atomic Types**: These are wrappers around the basic types (booleans, numbers, etc.) that are safe to use between threads.
1. **Async/Await**: Rust borrows the Async/Await model from languages such as JavaScript or C#, helping developers understand code that runs asynchronously.

The above is a summary of Rust features to write **safe concurrent and parallel code** without losing too much sleep. The language provides even more features than what we have just described.

There are also crates (libraries) that provide extra functionality to write concurrent and parallel code, such as [Tokyo](https://docs.rs/tokio/latest/tokio/#), [futures](https://docs.rs/futures), or [Rayon](https://github.com/rayon-rs/rayon).

### 4\. Functional Programming Nuggets

Rust enforces Functional Programming aspects such as **immutability** and explicit mutability at compile time. On top of this, **functions are first-class citizens**, opening the door for higher-level Functional Programming features.

While Rust is not a pure Functional Programming language, it has some Functional Programming constructs, such as:

- **Iterators**: Rust provides both sync and async iterators.
- **Closures**: They are anonymous functions that capture the environment within a particular scope.
- **Pattern-Matching**: Rust provides powerful pattern-matching capabilities, making it easier to write exhaustive code that handles all the cases required by the matched type.
- **Type Classes**: Achieved with generic type parameters. Each different parameter in a generic type changes the actual type. For example, `Vec<f32>` and `Vec<char>` are two different types, not a single type ( `Vec`) with two parameters.
- **Option and Either**: Rust includes Option and Either types in its standard library. These types replace the need for Nulls and Exceptions in the language.

Rust’s Functional Programming features help write **declarative, instead of imperative, code**, helping some developers reason about their code easily.

However, I don’t think Rust should aim to become a fully Functional Programming language. Rust offers a concurrency and parallelism model that does not need immutability, which is one of the holy grails of Functional Programming. In other words: code written in Rust does not necessarily need some of the properties that define Functional code, because Rust has a **different way of achieving safety when manipulating shared mutable states**.

### 5\. Great Cross-Platform Compatibility

Rust code can run on different platforms and hardware architectures with barely any modifications. Its tooling can help target and generate platform-specific code when needed:

1. **Cargo**: Rust’s package manager handles dependencies and build targets, downloading and compiling dependencies for different platforms.
1. **cfg\_attr attribute**: Specifies the configurations that the code will apply to.
1. **cfg macro**: Helps with conditional compilation by targeting specific platforms.

On top of this, Rust features **interoperability with C**, which allows for using C libraries that target specific architectures.

### 6\. Modern and Robust Error-Handling

Rust offers **error and result types** instead of Null and exceptions.

Developers can build errors in Rust by implementing the Error trait. The new trait implementations can include information about the error, then extract it with pattern-matching.

Both Result and Error can be used with pattern-matching, helping us ensure that we handle all errors explicitly and that our code is resilient.

Rust also allows propagating errors up the call stack if needed, to help developers handle errors at the appropriate level.

### 7\. Modern Package Manager

**Cargo** is the official Rust package manager. It is a modern package manager, similar to NPM, tailored to Rust’s needs to accommodate low-level concerns. In a nutshell, Cargo offers:

1. **Managing dependencies**: Cargo downloads the dependencies in Cargo.toml then compiles the packages.
1. **Assembling distributables**: Cargo bundles the dependencies and the code written by the developer and creates the executables. At this stage, Cargo manages platform-specific code depending on the target.
1. **Distributing executables**: You can upload your crates to [crates.io](https://crates.io/), Rust’s official package registry, and manage their versioning.
1. **Running tests**: It is crucial to have an automated test suite in modern software development. Cargo helps configure and run automated tests.

Compared to languages that lack a C++ dedicated package manager, Rust provides a great developer experience to tame the intricate details that Bare Metal Programming can throw at us.

### 8\. Active and Passionate Community

While you can find unwelcoming people in every community, and Rust is not an exception, I find the Rust community to be overall:

1. **Knowledgeable**: Many Rust developers come from languages like C++ and have extensive experience.
1. **Helpful**: I found people willing to teach me, pair with me for fun, or help out as a volunteer on platforms such as [exercism.org](https://exercism.org/). I can only be grateful to them for their time and dedication.
1. **Passionate**: You can feel that people in the Rust community firmly believe in Rust being a ground-breaking technology in the future. I do agree with them.
1. **Innovative**: Rust started fresh not so long ago. Rust developers are trying to push the boundaries of Programming, and look for ways to innovate.

## What are some real-world examples of Rust projects?

Rust is not an academic language with good intentions and low adoption. Rust is backed by some of the biggest names in the software industry, for a good reason. Here is a non-exhaustive list of **5 real-world projects that use Rust.**

### 1\. Dropbox Nucleus Sync Engine

The Dropbox crew struggled to refactor and improve their existing Sync Engine Classic, up to the point that only a rewrite would help.

They [wrote a new engine](https://dropbox.tech/infrastructure/rewriting-the-heart-of-our-sync-engine#-so-what-did-we-build) called Nucleus. Here is what they say about using Rust to write the new engine:

“\[It\] was one of the best decisions we made. **More than performance, its ergonomics and focus on correctness has helped us tame sync’s complexity**. We can encode complex invariants about our system in the type system and have the compiler check them for us.”

### 2\. Amazon Firecracker

Firecraker is the virtualization technology that improves AWS services such as [AWS Lambda](https://aws.amazon.com/lambda/) and [AWS Fargate](https://aws.amazon.com/fargate/). Firecracker improves efficiency and resource utilization. **Rust’s safety features helped Amazon achieve its security and performance standards**.

### 3\. Figma “Multiplayer” Sync Engine

Figma’s Synchronization engine written in TypeScript suffered from latency spikes. This was mainly due to TypeScript running on a single thread.

They [replaced the problematic parts of the old engine](https://www.figma.com/blog/rust-in-production-at-figma/) with a new one written in Rust. They took advantage of **Rust’s concurrency and parallelism features**, spinning up a new process per document, to improve the server-side performance dramatically.

### 4\. Coursera

Coursera [used Rust](https://medium.com/coursera-engineering/rust-docker-in-production-coursera-f7841d88e6ed) to avoid several security issues that are hard to avoid in C. **Rust type system enforces invariants** that are otherwise hard to achieve with classic Systems Programming languages like C.

### 5\. Servo

[Servo](https://servo.org/) is [Mozilla](https://www.mozilla.org/)’s parallel browser engine. Servo’s focus is on using multi-core hardware to improve performance. Servo takes advantage of **Rust's safety and concurrency features** to build a [parallel architecture](https://github.com/servo/servo/wiki/Design) that delivers high rendering performance.

## Rust's applications in Systems Programming, Web Development, and beyond

As we saw in the real-world examples, Rust is a programming language used in **critical systems**.

Despite being a general-purpose language, Rust is typically used in Systems Programming. From the [Linux kernel](https://www.kernel.org/doc/html/next/rust/index.html) to embedded devices, or just applications close to the metal, Rust excels in helping developers deliver fast and robust code.

One of the areas where developers have higher hopes is **Game Development**. While still young, Rust’s Game Development ecosystem is growing and improving. [Some studios](https://www.youtube.com/watch?v=TJ3w-pZ7FMI) have adopted Rust in its early Game Development stages.

Game engines such as [Bevy](https://bevyengine.org/) are growing and building on the valuable lessons learned from [previous attempts](https://github.com/amethyst/amethyst) to create new Rust Game Development tech. Although I am a hobbyist, I plan to use Rust for Game Development soon.

Another application of Rust is **Web Development**.

- Rust can help write safe and performant backend code when requirements are tight.
- [Web Assembly](https://webassembly.org/) is where the efforts are to use Rust in the browser. The promise of Web Assembly is secure, almost-native performance. I have worked on some experiments to run Rust code in the browser with Web Assembly. The results were satisfactory despite some limitations around loading and saving files.

## Conclusion

I hope that you enjoyed this codeless introduction to Rust. To summarize this article, here is a short wrap-up.

If you liked this article, please check the hands-on followup [here](/getting-started-with-rust/).

### Summary of Rust's key features and benefits

To summarize, these are the key points where Rust excels:

1. **Memory safety**
1. **Performance**
1. **Concurrency**
1. **Cross-platform**
1. **Community**
1. **Compatibility**
1. **Productivity**
1. **Security**

Overall, Rust is a powerful language that provides a unique and pragmatic approach to software development, especially in Systems Programming.

### Final thoughts on Rust's future and potential

Rust is here to stay. I don’t have a crystal ball, and Rust might vanish in a few years. But I highly doubt so. Rust does not look like a temporary fad. It is a great technology that solves problems developers working on low-level projects have suffered for decades. Rust helps tame those problems, and is backed by the support of the big players. Only time will say, but I can’t see Rust going anywhere.

Game Development is, without a doubt, one of the areas where Rust could shine. There is some resistance from developers. For decades, C++ has been the industry standard. Also, **Rust is a very terse language**, which sometimes does not work well with the iterative nature of Game Development. I can agree with this. At the same time, Rust is incredibly useful for programming game engines, leaving the ever-evolving gameplay logic for more flexible languages such as Lua.

Another field where Rust could be a game changer is the scientific field. If I were to launch a rocket, I would rather trust the launcher code written in Rust than in C or even Python.

The last areas where Rust could be a key player are AI and Machine Learning. A good amount of code is written in C and Python. However, new projects could benefit from Rust’s safety and concurrency features, to create more resilient and performant code. Rust’s interoperability with Python is not as first-class as with C, but this might be worth improving soon. AI is here to stay, so it might as well stay with Rust ;)

### Where to start learning Rust?

Here are a few resources to start learning Rust:

1. [The Rust Programming Language](https://doc.rust-lang.org/book/)
1. [Programming Rust](https://www.oreilly.com/library/view/programming-rust-2nd/9781492052586/)
1. [Rust in Action](https://www.manning.com/books/rust-in-action)
1. [Rust Cookbook](https://rust-lang-nursery.github.io/rust-cookbook/)
1. [Hands-On Rust](https://pragprog.com/titles/hwrust/hands-on-rust/)

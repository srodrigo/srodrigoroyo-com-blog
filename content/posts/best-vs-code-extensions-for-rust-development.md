---
_cmplz_remote_scanned_post: "1"
_cmplz_scanned_post: "1"
_edit_last: "1"
_thumbnail_id: "988"
author: wpx_srodrigoroyo_admin
categories:
  - programming
cmplz_hide_cookiebanner: ""
cover:
  alt: Best VSCode extensions for Rust development
  image: /wp-content/uploads/2023/08/fotis-fotopoulos-DuHKoV44prg-unsplash.jpg
date: "2023-08-21T17:35:37+00:00"
guid: https://srodrigoroyo.com/?p=978
parent_post_id: null
post_id: "978"
rank_math_analytic_object_id: "23"
rank_math_focus_keyword: best vs code extensions for rust development,rust development
rank_math_internal_links_processed: "1"
rank_math_primary_category: "6"
rank_math_seo_score: "79"
title: 5 Best VS Code extensions for Rust development
url: /best-vs-code-extensions-for-rust-development/

---
## Introduction

Rust is a relatively new programming language. Development tools are still growing, but there are a couple of useful VS Code extensions for Rust development. In this (shorter than usual) article, we’ll look at the state-of-the-art and what extensions can raise your Rust development to the next level.

## 5 Best VS Code extensions for Rust development

### 1\. rust-analyzer

This extension is the **cornerstone extension for Rust developers**. [rust-analyzer](https://marketplace.visualstudio.com/items?itemName=rust-lang.rust-analyzer) uses Language Server Protocol (LSP) to do what you expect from an official language extension. Here are a few examples:

- Semantic syntax highlighting.
- Code completion: add braces automatically, code snippets, auto import.
- Code navigation: find references, go to definition or implementation.
- Code assistance: inlay hints, code actions.
- Refactoring tools: rename symbols, extract functions and variables.
- Documentation: hover or open the official documentation.
- Run programs and tests.

{{< figure src="/wp-content/uploads/2023/08/word-image-978-1.png" alt="Best VS Code extensions for Rust development" caption="Best VS Code extensions for Rust development" >}}

If you don’t like bloating your VS Code, and only want to have one Rust extension, this is the one, without a doubt. For the complete set of features, visit the [official website](https://rust-analyzer.github.io/manual.html).

### 2\. crates

Keeping crates up-to-date can be complicated. [crates](https://marketplace.visualstudio.com/items?itemName=serayuzgur.crates) helps **manage dependencies at a glance**.

One of the most interesting features is visualizing **which dependencies are on their latest version** and which are lagging.

{{< figure src="/wp-content/uploads/2023/08/word-image-978-2.png" alt="Outdated crates" caption="Outdated crates" >}}

It also allows **opening the documentation** or changing to a different, existing version.

{{< figure src="/wp-content/uploads/2023/08/word-image-978-3.png" alt="Open create documentation" caption="Open create documentation" >}}

Lastly, it provides several handy commands to fetch, replace, and update dependencies.

{{< figure src="/wp-content/uploads/2023/08/word-image-978-4.png" alt="Crates commands" caption="Crates commands" >}}

### 3\. Even Better TOML

TOML files can be dry to work with. [Even Better TOML](https://marketplace.visualstudio.com/items?itemName=tamasfe.even-better-toml) provides a good amount of **features to work with TOML files** with ease:

- Syntax highlighting
- Semantic highlighting
- Syntax validation
- Refactors
- Completion
- Formatting

Along with `crates`, this extension is all you need to keep your TOML files in good shape.

### 4\. CodeLLDB

LLDB is a powerful debugger that supports Rust. It provides **extended debugging capabilities** compared to the default debugger. [CodeLLDB](https://marketplace.visualstudio.com/items?itemName=vadimcn.vscode-lldb) includes:

- Conditional breakpoints
- Function breakpoints
- Logpoints
- Hardware data access breakpoints
- Disassembly view
- Loaded modules view
- Python scripting
- HTML rendering (useful to create visualizations)
- Launch configurations defaults
- Remote debugging

{{< figure src="/wp-content/uploads/2023/08/word-image-978-5.png" alt="LLDB debugging" caption="LLDB debugging" >}}

It also provides commands to access these features _a la_ VS Code:

{{< figure src="/wp-content/uploads/2023/08/word-image-978-6.png" alt="LLDB commands" caption="LLDB commands" >}}

### 5\. Rust Flash Snippets

The [Rust Flash Snippets](https://marketplace.visualstudio.com/items?itemName=lorenzopirro.rust-flash-snippets) extension offers a wide range of snippets. It has **snippets for the main keywords and for more complex constructs**, such as implementing types with a `new` method, implementing the `Display` trait, or testing methods.

My only complaint is that it’s not as user-friendly as I’d like. For example, creating a function allows tabbing to the parameters but not tabbing to the result type or the body.

Despite a few glitches, it is a versatile collection of snippets that expands on non-trivial constructs.

## Conclusion

Rust is an [amazing programming language](/introduction-to-rust/) complex enough to benefit from good IDE tools. I hope that the list of extensions above will help improve your developer flow when Rust-ing 🙂

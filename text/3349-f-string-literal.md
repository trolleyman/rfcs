- Feature Name: `f_string_literal`
- Start Date: (fill me in with today's date, YYYY-MM-DD)
- RFC PR: [rust-lang/rfcs#0000](https://github.com/rust-lang/rfcs/pull/0000)
- Rust Issue: [rust-lang/rust#0000](https://github.com/rust-lang/rust/issues/0000)

# Summary
[summary]: #summary
<!-- One paragraph explanation of the feature. -->

This RFC proposes the addition of f-strings (`f""`) to Rust, allowing for more concise and readable string formatting.

```rust
let name = "World";
let greeting = f"Hello, {name}!";
assert_eq!(greeting, "Hello, World!");
```

# Motivation
[motivation]: #motivation
<!-- Why are we doing this? What use cases does it support? What is the expected outcome? -->

<!-- The benefits are:
- It is shorter than `format!` (though admittedly this does evapourate when you are using `println!`, `panic!`, etc. anyway)
- It is more powerful than `format!` - it can take arbitrary expressions, allowing more conciseness
- The arguments are more local than `format!` - i.e. they are exactly where they are in the final string, rather than at the end - which can cause some confusion when you remove an argument or add one, and forget to update the string, or vice versa.
- It can be more easily converted to/from a regular string - just add a `f` prefix, whereas you have to write `format!(`, and add a closing parenthesis.
- It is more like other languages - improving ease of developer adoption and understanding
    - Also, the popularity of these other language changes should indicate the popularity of this feature in general. -->

The introduction of f-strings into Rust is driven by a desire to enhance the developer experience in string formatting by providing a more concise, powerful, and intuitive syntax. Currently, Rust utilizes the `format!` macro, which, while robust, can become verbose and somewhat non-intuitive, especially in complex formatting scenarios. F-strings, on the other hand, offer a succinct syntax that allows developers to embed expressions directly within string literals, thereby reducing boilerplate code and enhancing readability. For instance, instead of writing `format!("Hello, {}!", name)`, developers can simply write `f"Hello, {name}!"`, making the code shorter and arguably more readable.

F-strings cater to a variety of use cases by supporting arbitrary expressions within the string literals, thereby offering more than just variable substitution (as the current `format!` macro uses). This capability allows developers to perform operations like method calls, arithmetic, or logical operations directly within the string, fostering code conciseness. For example, `f"The sum of {x} and {y} is {x + y}"` directly embeds an arithmetic operation within the string, eliminating the need for pre-calculating or using additional formatting lines. The expected outcome is a streamlined string formatting approach that simplifies codebases, making them easier to read, write, and maintain, especially in applications where string operations are prevalent.

Moreover, aligning Rust’s string formatting capabilities with those of other popular programming languages, such as Python, C#, and JavaScript, which already support similar features, can potentially lower the barrier to entry for developers transitioning to Rust. The familiarity of f-string-like syntax could improve developer adoption and understanding, as they can leverage their knowledge from other languages. The popularity and widespread use of f-string formatting in these languages also serve as an indicator of the potential positive reception and utility of this feature within the Rust community, providing a user-friendly way to format strings while keeping code clean and readable.


# Guide-level explanation
[guide-level-explanation]: #guide-level-explanation
<!-- Explain the proposal as if it was already included in the language and you were teaching it to another Rust programmer. That generally means:

- Introducing new named concepts.
- Explaining the feature largely in terms of examples.
- Explaining how Rust programmers should *think* about the feature, and how it should impact the way they use Rust. It should explain the impact as concretely as possible.
- If applicable, provide sample error messages, deprecation warnings, or migration guidance.
- If applicable, describe the differences between teaching this to existing Rust programmers and new Rust programmers.
- Discuss how this impacts the ability to read, understand, and maintain Rust code. Code is read and modified far more often than written; will the proposed feature make code easier to maintain?

For implementation-oriented RFCs (e.g. for compiler internals), this section should focus on how compiler contributors should think about the change, and give examples of its concrete impact. For policy RFCs, this section should provide an example-driven introduction to the policy, and explain its impact in concrete terms. -->

F-strings are a new way to format strings in Rust by embedding expressions directly within string literals. The expressions are enclosed in curly braces {} and are evaluated at runtime, with the resulting values converted to strings and concatenated with the string literal.

Here's an example of an f-string:

```rust
let name = "Alice";
let age = 30;
let message = f"Hello, my name is {name} and I am {age} years old.";
println!("{}", message);
```

This would output:

```
Hello, my name is Alice and I am 30 years old.
```

F-strings can contain arbitrary expressions, including arithmetic operations, function calls, and more:

```rust
let x = 5;
let y = 10;
let message = f"The sum of {x} and {y} is {x + y}.";
println!("{}", message);
```

This would output:

```
The sum of 5 and 10 is 15.
```

F-strings would also support the same formatting options as the format! macro, using the familiar syntax:

```rust
let pi = 3.141592;
let message = f"Pi is approximately {pi:.3}.";
println!("{}", message);
```

```
Pi is approximately 3.14.
```

# Reference-level explanation
[reference-level-explanation]: #reference-level-explanation
<!-- This is the technical portion of the RFC. Explain the design in sufficient detail that:

- Its interaction with other features is clear.
- It is reasonably clear how the feature would be implemented.
- Corner cases are dissected by example.

The section should return to the examples given in the previous section, and explain more fully how the detailed proposal makes those examples work. -->

F-strings will be implemented as a new macro, f, which takes a string literal as its argument. The macro will parse the string literal at compile time, identifying expressions within curly braces, and generating code to evaluate these expressions at runtime.

The macro will then generate a new formatted string by concatenating the string literal with the evaluated expressions. The formatting options specified within the expressions will be applied using the existing formatting infrastructure used by the format! macro.

The implementation will need to ensure that f-strings work correctly with existing Rust features, such as string escapes, raw strings, and character escapes within the curly braces.

# Drawbacks
[drawbacks]: #drawbacks
<!-- Why should we *not* do this? -->

- Introducing f-strings adds complexity to the language, as there will now be two ways to format strings (format! macro and f-strings).
- There might be a learning curve for developers who are not familiar with similar features in other languages.
- Implementation may introduce performance overhead in some cases due to the runtime evaluation of expressions.

# Rationale and alternatives
[rationale-and-alternatives]: #rationale-and-alternatives
<!-- - Why is this design the best in the space of possible designs?
- What other designs have been considered and what is the rationale for not choosing them?
- What is the impact of not doing this?
- If this is a language proposal, could this be done in a library or macro instead? Does the proposed change make Rust code easier or harder to read, understand, and maintain? -->

- The main rationale for this feature is improved ergonomics and readability of string formatting in Rust.
- Alternatives include sticking with the existing format! macro or exploring other string interpolation libraries and macros.
- The impact of not doing this is that Rust developers will continue to use the more verbose and less readable format! macro for string formatting.

# Prior art
[prior-art]: #prior-art
<!-- Discuss prior art, both the good and the bad, in relation to this proposal.
A few examples of what this can include are:

- For language, library, cargo, tools, and compiler proposals: Does this feature exist in other programming languages and what experience have their community had?
- For community proposals: Is this done by some other community and what were their experiences with it?
- For other teams: What lessons can we learn from what other communities have done here?
- Papers: Are there any published papers or great posts that discuss this? If you have some relevant papers to refer to, this can serve as a more detailed theoretical background.

This section is intended to encourage you as an author to think about the lessons from other languages, provide readers of your RFC with a fuller picture.
If there is no prior art, that is fine - your ideas are interesting to us whether they are brand new or if it is an adaptation from other languages.

Note that while precedent set by other languages is some motivation, it does not on its own motivate an RFC.
Please also take into consideration that rust sometimes intentionally diverges from common language features. -->

- Python f-strings: This proposal is directly inspired by Python's f-string feature.
- JavaScript template literals: JavaScript has a similar feature called template literals, which use backticks and `${}` syntax for embedding expressions.
- Swift string interpolation: Swift also has a string interpolation feature that allows embedding expressions inside string literals using a backslash and parentheses `\(expression)`.

# Unresolved questions
[unresolved-questions]: #unresolved-questions
<!-- - What parts of the design do you expect to resolve through the RFC process before this gets merged?
- What parts of the design do you expect to resolve through the implementation of this feature before stabilization?
- What related issues do you consider out of scope for this RFC that could be addressed in the future independently of the solution that comes out of this RFC? -->

- How will f-strings interact with other language features like lifetimes, borrowing, and reference counting?
- Are there any potential security concerns or edge cases that need to be addressed in the implementation?
- Will there be any limitations on the types of expressions that can be used within f-strings?

# Future possibilities
[future-possibilities]: #future-possibilities

<!-- Think about what the natural extension and evolution of your proposal would
be and how it would affect the language and project as a whole in a holistic
way. Try to use this section as a tool to more fully consider all possible
interactions with the project and language in your proposal.
Also consider how this all fits into the roadmap for the project
and of the relevant sub-team.

This is also a good place to "dump ideas", if they are out of scope for the
RFC you are writing but otherwise related.

If you have tried and cannot think of any future possibilities,
you may simply state that you cannot think of anything.

Note that having something written down in the future-possibilities section
is not a reason to accept the current or a future RFC; such notes should be
in the section on motivation or rationale in this or subsequent RFCs.
The section merely provides additional information. -->

- Extending f-strings to support more advanced formatting options, such as custom formatting traits or internationalization support.
- Providing additional tooling and linting support for f-strings, such as syntax highlighting, autocompletion, and error detection in popular Rust IDEs and editors.
- Exploring performance optimizations for f-strings to minimize overhead and ensure they are suitable for use in performance-critical scenarios.

While the introduction of f-strings to Rust will already provide a more concise and readable way of formatting strings, there are many other possible improvements and extensions that can be explored in the future, based on the needs of the Rust community and the experience of other programming languages with similar features.

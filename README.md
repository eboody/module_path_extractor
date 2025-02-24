# Pseudo Rust Module Path Detector

This Rust utility provides a way to **determine the module path** of a procedural macro invocation. It is particularly useful when writing **procedural macros** that need to **infer their context** within a Rust module hierarchy.

## ✨ Features
- ✅ **Detects the module path** where the macro is invoked (e.g., `"crate::task::workflow"`).
- ✅ **Supports nested modules**, both inline (`mod foo {}`) and external (`mod foo;`).
- ✅ **Handles `pub mod`, `pub(crate) mod`, and `pub(super) mod`** declarations correctly.
- ✅ **Works with procedural macros** to automatically infer where they are used.
- ✅ **Cross-platform compatibility** (handles both `/` and `\` in paths).

---

## 📦 Installation

This module requires **Rust nightly** because it uses the `proc_macro_span` feature.

1. Add the following to `Cargo.toml`:

   ```toml
   [dependencies]
   proc-macro2 = "1"
   ```

2. In your Rust crate, enable the nightly feature:

   ```rust
   #![feature(proc_macro_span)]
   ```

---

## 🚀 Usage

This module provides a function `get_pseudo_module_path()` that **returns the inferred module path** as a `String`.

### **Example 1: Simple Module**
```rust
mod task {
    #[some_macro]  // This macro should return "crate::task"
    fn example() {}
}
```
**Output:**
```
"crate::task"
```

---

### **Example 2: Nested Modules**
```rust
pub mod task {
    pub mod workflow {
        #[some_macro] // Should return "crate::task::workflow"
        fn example() {}
    }
}
```
**Output:**
```
"crate::task::workflow"
```

---

### **Example 3: Procedural Macro Usage**
When used inside a procedural macro:

```rust
#[proc_macro]
pub fn example_macro(_input: TokenStream) -> TokenStream {
    let module_path = get_pseudo_module_path();
    println!("Macro invoked in module: {}", module_path);
    TokenStream::new()
}
```

Invoking the macro inside `src/workflow.rs`:
```rust
workflow::example_macro!();
```
**Output:**
```
Macro invoked in module: "crate::workflow"
```

---

## 🛠️ How It Works

### **1️⃣ Extracts File and Line Number**
It uses `proc_macro::Span` to get:
- The **file path** where the macro was invoked.
- The **line number** where it was called.

### **2️⃣ Reads the File and Finds Modules**
- Scans the source file **above the macro call** for module declarations (`mod foo {}`).
- Tracks **nested modules** to build a **hierarchical path** (`crate::task::workflow`).
- Supports **both inline (`mod foo {}`) and external (`mod foo;`) modules**.

### **3️⃣ Constructs the Full Module Path**
- Uses the **module hierarchy** if found (`crate::task::workflow`).
- Otherwise, **derives the path from the file structure**.

---

## ⚠️ Known Limitations
1. **Requires nightly Rust** (`proc_macro_span` feature).
2. Does not handle **generated code** (e.g., if a macro generates a module).
3. Assumes that **module paths match the file structure** (standard Rust convention).

---

## 👨‍💻 Contributing
Pull requests and issues are welcome! Feel free to improve the logic or add more test cases.

---

## 📜 License
This project is licensed under the **MIT License**.

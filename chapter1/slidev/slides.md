---
layout: section
background: black
theme: dracula
---

# Crafting Clickable Hyperlinks: From Bash to Rust

---
layout: default
---

# Hyperlinks in Bash

**Bash Command:**

```bash
echo -e '\033]8;;https://www.patreon.com/bytesandbalance\033\\Support Bytes and Balance on Patreon!\033]8;;\033\\'
```

**Explanation:**
- **`\033]8;;https://www.patreon.com/bytesandbalance\033\\`**: This escape sequence starts the hyperlink, setting the URL to the desired page.
- **`Support Bytes and Balance on Patreon!`**: This is the text that will be displayed as the clickable link.
- **`\033]8;;\033\\`**: This sequence ends the hyperlink formatting.


---
layout: default
---

# Creating Hyperlinks in Rust

**Step 1: Define the Struct**

```rust
#[derive(Clone, Debug, PartialEq)]
pub struct HyperLink {
    text: String,
    link: Url,
}
```

**Step 2: Implement a Constructor**

```rust
impl HyperLink {
    pub fn new(text: impl AsRef<str>, link: impl AsRef<str>) -> Result<Self, ParseError> {
        let text = text.as_ref().to_owned();
        let link = link.as_ref().try_into()?;
        Ok(Self { text, link })
    }
}

```

---
layout: default
---

**Step 3: Implement the `Display` Trait**

Implement the `Display` trait for `HyperLink` to format it with ANSI escape codes:

```rust
impl Display for HyperLink {
    fn fmt(&self, f: &mut fmt::Formatter<'_>) -> fmt::Result {
        write!(
            f,
            "\x1b]8;;{}\x1b\\{}\x1b]8;;\x1b\\",
            self.url, self.text
        )
    }
}
```

**Explanation:**
- **`\x1b]8;;{}`**: Starts the hyperlink with the URL.
- **`{}`**: Placeholder for the display text.
- **`\x1b]8;;\x1b\\`**: Ends the hyperlink formatting.

---
layout: default
---

# Using `println!` with HyperLink

```rust
fn main() {
    let hyperlink =
        HyperLink::new("Kudos to Nick Larsen for the code! Follow him on GitHub Sponsors!", "https://github.com/sponsors/NickLarsenNZ");

    println!("{}", hyperlink);
}
```

**Explanation of `println!`:**
- **`println!` Macro**: `println!` is a macro in Rust designed for formatting and printing text. It automatically handles formatting by calling the `Display` trait’s `fmt` method on the provided objects.
- **Macro Expansion**: When `println!("{hyperlink}");` is executed, the macro expands to code that calls `hyperlink.fmt(&mut formatter)`, where `formatter` is used to build the output string.
- **Trait Resolution**: Rust uses trait resolution to determine which `fmt` method to call. For `HyperLink`, it uses the `Display` trait implementation to format the output string.

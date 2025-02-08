# MiniGrep

MiniGrep is a simplified implementation of the classic Unix grep command written in Rust. This tool allows you to search for specific text patterns within files, demonstrating fundamental Rust concepts like error handling, file I/O, and command-line argument parsing.

## Features

This implementation includes several key features that make it both practical and educational:

- Case-sensitive and case-insensitive search modes
- Environment variable configuration
- Error handling with detailed messages
- File content searching with line-by-line results
- Command-line interface

## Installation

To use MiniGrep, you'll need to have Rust installed on your system. If you haven't installed Rust yet, you can do so by following the instructions at [https://rustup.rs](https://rustup.rs).

Once Rust is installed, clone this repository and build the project:

```bash
git clone [your-repository-url]
cd minigrep
cargo build --release
```

The compiled binary will be available in `target/release/minigrep`.

## Usage

The basic syntax for using MiniGrep is:

```bash
cargo run <query> <file_path>
```

For example, to search for the word "pattern" in a file named "example.txt":

```bash
cargo run pattern example.txt
```

### Case Sensitivity

By default, MiniGrep performs case-sensitive searches. You can enable case-insensitive searching by setting the `IGNORE_CASE` environment variable:

```bash
# Unix/Linux/macOS
IGNORE_CASE=1 cargo run pattern example.txt

# Windows PowerShell
$env:IGNORE_CASE=1; cargo run pattern example.txt
```

## Examples

Here are some practical examples of using MiniGrep:

### Case-Sensitive Search
```bash
$ cargo run duct poem.txt
safe, fast, productive.
```

### Case-Insensitive Search
```bash
$ IGNORE_CASE=1 cargo run RUST poem.txt
Rust:
Trust me.
```

## Project Structure

The project is organized into two main files:

- `src/main.rs`: Contains the command-line interface and argument parsing logic
- `src/lib.rs`: Contains the core search functionality and configuration

This separation follows Rust's best practices for binary projects, making the code more maintainable and testable.

## Understanding the Code

### Configuration

The `Config` struct handles program configuration:

```rust
pub struct Config {
    pub query: String,
    pub file_path: String,
    pub case_sensitive: bool,
}
```

Configuration is built using the builder pattern, which provides clear error handling:

```rust
let config = Config::build(&args).unwrap_or_else(|err| {
    eprintln!("Problem parsing arguments: {}", err);
    process::exit(1);
});
```

### Search Implementation

The project includes two search functions:

1. Case-sensitive search using Rust's iterator methods:
```rust
pub fn search<'a>(query: &str, contents: &'a str) -> Vec<&'a str> {
    contents
        .lines()
        .filter(|line| line.contains(query))
        .collect()
}
```

2. Case-insensitive search with explicit string manipulation:
```rust
pub fn search_case_insensitive<'a>(query: &str, contents: &'a str) -> Vec<&'a str> {
    let query = query.to_lowercase();
    let mut results = Vec::new();

    for line in contents.lines() {
        if line.to_lowercase().contains(&query) {
            results.push(line);
        }
    }

    results
}
```

## Testing

The project includes a comprehensive test suite. Run the tests using:

```bash
cargo test
```

The tests cover both case-sensitive and case-insensitive search functionality:

```rust
#[test]
fn case_sensitive() {
    let query = "duct";
    let contents = "\
Rust:
safe, fast, productive.
Pick three.
Duct tape.";
    assert_eq!(vec!["safe, fast, productive."], search(query, contents));
}
```

## Error Handling

MiniGrep implements robust error handling throughout:

- Command-line argument validation
- File reading errors
- Search operation errors

Errors are reported with helpful messages to stderr, making it easier to diagnose issues.

## Future Enhancements

Here are some potential improvements that could be made to the project:

1. Regular expression support for more powerful pattern matching
2. Recursive directory searching
3. Multiple file input support
4. Match highlighting in the output
5. Context lines (showing lines before and after matches)

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## License

This project is licensed under the MIT License - see the LICENSE file for details.

## Acknowledgments

This project was inspired by the grep command-line utility and the Rust Programming Language Book's tutorial on building a grep clone.
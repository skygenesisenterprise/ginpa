# GINPA Protocol - Agent Guidelines

## Build Commands

### Rust Projects
- **Build all**: `cargo build --release`
- **Test single module**: `cargo test --bin <binary_name> <module_name>`
- **Test specific function**: `cargo test <test_name>`
- **Lint**: `cargo clippy -- -D warnings`
- **Format**: `cargo fmt`
- **Check**: `cargo check`

### Godot/Flumi Project
- **Build**: `godot --export "Linux/X11" ./build/ginpa.x86_64`
- **Run**: `godot --run .`

### Docker Services
- **DNS**: `cd dns && ./build.sh`
- **Search Engine**: `cd search-engine && ./build.sh`

## Code Style Guidelines

### Rust
- Use `cargo fmt` for formatting
- Use `cargo clippy` for linting
- Follow Rust naming conventions (snake_case for functions/variables, PascalCase for types)
- Use `Result<T, E>` for error handling with `anyhow` or `thiserror`
- Prefer `async/await` with tokio runtime
- Use `tracing` for logging, not `println!`

### GDScript
- Follow Godot naming conventions (snake_case for functions/variables, PascalCase for classes)
- Use strong typing where possible
- Prefer signals over polling
- Use autoloaded singletons for global state

### General
- Write tests for all public functions
- Use descriptive commit messages
- Keep functions small and focused
- Document public APIs with comments
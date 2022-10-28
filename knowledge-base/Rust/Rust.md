## cargo

- Cargo is Rust’s build system and package manager.
- `cargo run` command compiles and executes your program
- `cargo fmt` formats code
- it is idiomatic to track cargo.lock in a vcs only for executables , but omitted for libraries.
- the idiomatic (it never gets old!) place to find packages is [crates.io](https://crates.io/) .

## Code conventions

- developers should use spaces, not tabs

## Imports

You don't need imports to run struct function:

```
let now = std::time::SystemTime::new()
```

But for readability sake you should use impors like this:

```
use std::time::SystemTime;
...
let now = SystemTime::now();
```

To import multiple structs use curly brackets:

```
use std::time::{SystemTime, Duration};
```

To import any struct or module  use * :
```
use std::time::*;
```

### External crates

1. add it first to `cargo.toml`
2. import `extern crate chrono;` -  where `chrono` our crate name
3. `use chrono;`

## Modules and structs

- `std::time::SystemTime` can be read as `module::module::struct`
- idiomatic naming: modules are `lowercase` . structs are `camelcase` . variables, methods, and functions are `snake_case` .
- 

## Specifics

- variables are immutable by default. To make it mutable add `mut` after `let`
- represents time as elapsed time since unix epoch.
- `+` operator can be used not primitive types(SystemTime for ex), if `SystemTime::add` implements the `add<duration>` trait. From that, rust can determine how to apply `SystemTime + Duration`
- `traits` are like supercharged go interfaces. implementing a trait in rust produces approximately the same effect as implementing an interface in go. in rust, though, implementing a trait must be done explicitly ( `impl mytrait for mytype {}` ).
- Fornatting:
	- `{}` will print the thing in its "display mode" (e.g. with the intention of displaying it to users). Structs must implement `display`
	- `{:?}` will print the thing in its "debug mode". Sturcts must implement `debug`
- several returns should be wrapped in round brackets: `let (is_pm, _) = Local::now().hour12();`
- you can directly take any element of returned tuple by `now.hour12().0`, where 0 is an element position.
- no initializer in `if-else` clause.
- but you can assign `if-else` result to variable(note semicolums absence):
```    
let day_part = if is_pm {
	"afternoon"
} else {
	"morning"
};
```
- semicoloms are required, but should be skipped in `if-else` statements with assignments
- `match { => EXPRESSION,}` is a `switch{ case: EXPRESSION}` replacement
- Methods are defined within the context of a struct and their first parameters is _always_ `self`.
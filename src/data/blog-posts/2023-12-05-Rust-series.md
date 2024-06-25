---
title: Series in Rust
publishDate: 05 Dec 2023
description: Pandas-like Series, but in Rust
---


# Manipulating data 

Coming from the world of python and pandas, you know that the basic object to 
deal with data is a Series, coming right before the DataFrame.
Let's see how Rust can deal with those. 


## Series
To stay as close as possible to the data scientist's workflow, I'll work in a notebook.
For Rust that means that you installed [evcxr](https://github.com/evcxr/evcxr/tree/main). 

```rust
:dep polars = {version = "0.34.2"}
:dep polars-core = {version = "0.15"}


```
Let's create a polars series from a list:

```rust
use polars_core::prelude::*;
let s = Series::new("a", [0i32, 1, 8]);
s
```


```rust
shape: (3,)
Series: 'a' [i32]
[
	0
	1
	8
]
```

and make a quick length check:
```rust
assert_eq!(s.len(), 3);

```

A Series from a range now:
```rust
// series from a range
let from_iter: Series = (0..10)
    .into_iter()
    .collect();
from_iter
```
```rust
shape: (10,)
Series: '' [i32]
[
	0
	1
	2
	3
	4
	5
	6
	7
	8
	9
]
```

### Making more series

```rust
// Make series from primitive types
let int_series = Series::new("Integers", &[1, 2, 3, 4]);
let float_series = Series::new("Floats", &[1.0, 2.0, 3.0, 4.0]);
let bool_series = Series::new("Booleans", &[true, false, true]);

println!("Integers:\n{}", int_series);
println!("Floats:\n{}", float_series);
println!("Booleans:\n{}", bool_series);
```
```rust

Integers:
shape: (4,)
Series: 'Integers' [i32]
[
    1
    2
    3
    4
]
Floats:
shape: (4,)
Series: 'Floats' [f64]
[
    1
    2
    3
    4
]
Booleans:
shape: (3,)
Series: 'Booleans' [bool]
[
    true
    false
    true
]

```

Making series from strings
```rust
let str_series =Series::new("Strings", &["a", "b", "c"]);
let string_series = Series::new("StringVec", vec!["a".to_string(), "b".to_string(), "c".to_string()]);

println!("Strings:\n{}", str_series);
println!("StringVec:\n{}", string_series);
```

```rust


Strings:
shape: (3,)
Series: 'Strings' [str]
[
    "a"
    "b"
    "c"
]
StringVec:
shape: (3,)
Series: 'StringVec' [str]
[
    "a"
    "b"
    "c"
]

```

```rust
let string_series_2 = Series::new("StringVec", vec!["a", "b", "c"]);
string_series_2

```

```rust

shape: (3,)
Series: 'StringVec' [str]
[
    "a"
    "b"
    "c"
]
```




```rust
// from option types
let nullable_series = Series::new("Nullable", &[Some(1), None, Some(3)]);
nullable_series

```

```rust

shape: (3,)
Series: 'Nullable' [i32]
[
    1
    null
    3
]

```






## Contents of a series

Actually what lies behind the curtain of a Series? The answer is: a ChunkedArray
```rust
// get the ChunkedArray from the Series
s.i32().unwrap()
```

```rust
shape: (3,)
ChunkedArray: 'a' [Int32]
[
	1
	2
	3
]
```

## Slicing in a series

Sometimes you only want a slice of the pie, not the whole pie:
```rust
let series = Series::new("values", &[1, 2, 3, 4, 5]);

// Slice the series starting from index 1 (second element) and take 3 elements
let sliced_series = series.slice(1, 3);

println!("Original Series:\n{}", series);
println!("Sliced Series:\n{}", sliced_series);
```
```rust

Original Series:
shape: (5,)
Series: 'values' [i32]
[
	1
	2
	3
	4
	5
]
Sliced Series:
shape: (3,)
Series: 'values' [i32]
[
	2
	3
	4
]
```

Or may be get a single element:

```rust
s.get(2)
```
```rust
Int32(8)

```
## Aggregations in a series


```rust
s
```



```rust
shape: (3,)
Series: 'a' [i32]
[
    0
    1
    8
]

```
    







## Operations on series
Series are great as data containers. But sometimes you want to operate on them:

```rust
// aggregation
let s = Series::new("a", [1, 2, 3]);

// min returns an option
assert_eq!(s.min::<u64>(), Some(1   ));
```


```rust
// comparing series
s.series_equal_missing(&Series::new("a", [1, 2, 3]))
```

```rust
true
```




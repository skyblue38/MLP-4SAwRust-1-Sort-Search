# 1. Bubble Sort
The basic idea of this sort algorithm is to loop through the vector comparing adjacent values and swap them if they are out of order. The effect is that out-of-place higher values quickly float to the top, while lower values may take longer to sink to the bottom... The graph below shows sequential data with item values plotted on the y-axis with their collating sequence order on the x-axis. Note that the highest values float up in a single pass, but it takes many passes for low values to sink, as they only move down one position on each pass.

![](https://upload.wikimedia.org/wikipedia/commons/3/37/Bubble_sort_animation.gif)_Credit: https://commons.wikimedia.org/wiki/User:Hydrargyrum_

## WORKFLOW
First create the rust project using commands:
``` bash
$ cargo new MLP/FSAwRust-1-SortSearch/m1-bubblesort
     Created binary (application) `MLP/FSAwRust-1-SortSearch/m1-bubblesort` package
$ geany MLP/FSAwRust-1-SortSearch/m1-bubblesort/src/main.rs
```
### 1. The `get_i32` function
This function reads a line of text from the standard input channel (usually the keyboard) and converts it to a 32-bit integer (i32) and returns this value. It does not handle non-numeric character... ie. it will cause a Rust panic if something other than an integer is entered.
``` rust
use std::io;
use std::io::Write;
// ...
// Prompt the user for an i32.
fn get_i32(prompt: &str) -> i32 {
    print!("{prompt}");
    io::stdout().flush().unwrap();
    let mut str_value = String::new();
    io::stdin()
        .read_line(&mut str_value)
        .expect("Error reading input");
    let trimmed = str_value.trim();
    return trimmed.parse::<i32>()
        .expect("Error parsing integer");
}
```
### 2. Pseudo-Random number generator- `prng`
A PRNG produces numbers that appear random-ish but that are not truly random. This project uses a simple _linear congruential generator_ (LCG) that uses an equation with the following form to generate a sequence of numbers.
``` text
X[N+1] = (A × X[N] + C) mod M
```
Here A, C, and M are constants that you pick for your generator. You start with some initial _seed_ value as X[0] and then use each value X[N] to generate the next value X[N+1].

Here is the Rust code for a PRNG function:
``` rust
use std::time::{SystemTime, UNIX_EPOCH};
// ************
// *** Prng ***
// ************
struct Prng {
    seed: u32,
}
impl Prng {
    fn new() -> Self {
        let mut prng = Self { seed: 0 };
        prng.randomize();
        return prng;
    }
    fn randomize(&mut self) {
        let millis = SystemTime::now()
            .duration_since(UNIX_EPOCH)
            .expect("Time went backwards")
            .as_millis();
        self.seed = millis as u32;
    }
    // Return a pseudorandom value in the range [0, 2147483647].
    fn next_u32(&mut self) -> u32 {
        self.seed = self.seed.wrapping_mul(1_103_515_245).wrapping_add(12_345);
        self.seed %= 1 << 31;
        return self.seed;
    }
    // Return a pseudorandom value in the range [0.0, 1.0).
    fn next_f64(&mut self) -> f64 {
        let f = self.next_u32() as f64;
        return f / (2147483647.0 + 1.0);
    }
    // Return a pseudorandom value in the range [min, max).
    fn next_i32(&mut self, min: i32, max: i32) -> i32 {
        let range = (max - min) as f64;
        let result = min as f64 + range * self.next_f64();
        return result as i32;
    }
}
```
### 3. The `make_random_vec` function
This function creates a vector and populates it with randomly generated numbers. This is used to create numbers to be sorted...
``` rust
// Make a vector of random i32 values in the range [0 and max).
fn make_random_vec(num_items: i32, max: i32) -> Vec<i32> {
    // Prepare a Prng.
    let mut prng = Prng::new();

    let mut vec: Vec<i32> = Vec::with_capacity(num_items as usize);
    for _ in 0..num_items {
        vec.push(prng.next_i32(0, max));
    }
    return vec;
}
```
### 4. The `print_vec` function
This function is  used to display the contents of a Rust vector
``` rust
// Print at most num_items items.
fn print_vec(vec: &Vec<i32>, num_items: i32) {
    let mut max = vec.len();
    if max > num_items as usize {
        max = num_items as usize;
    }

    let mut string = String::new();
    string.push_str("[");

    if max > 0usize {
        string.push_str(&vec[0].to_string());
    }

    for i in 1usize..max {
        string.push_str(" ");
        string.push_str(&vec[i].to_string());
    }
    string.push_str("]");
    println!("{string}");
}
```

### 5. The `bubblesort` function
``` rust
// Use bubble sort to sort the vector in place.
fn bubble_sort(vec: &mut Vec<i32>) {
	let mut n = vec.len();
	let mut changed = true;
	while changed {
		changed = false;
		for i in 1..n {
			if vec[i - 1] > vec[i] {
				vec.swap(i - 1, i);
				changed = true;
			}
		}
		n = n - 1;
	}
}
```

### 6. The `check_sorted` function
``` rust
// Verify that the Vec is sorted.
fn check_sorted(vec: &Vec<i32>) {
	let mut ok = true;
	for i in 1..vec.len() {
		if vec[i - 1] > vec[i] {
			ok = false;
			break;
		}
	}
	if ok {
		println!("The vector is sorted!");
	} else {
	println!("The vector is NOT sorted!");
	}
```
### 7. The `main` function
``` rust
// START HERE
fn main() {
    let num: i32 = get_i32("Enter how many numbers to sort: ");
    let max: i32 = get_i32("Enter maximum value: ");
    let mut v: Vec<i32> = make_random_vec(num, max);
    print_vec(&mut v, num);
    bubble_sort(&mut v);
    print_vec(&mut v, num);
    check_sorted(&mut v);
}
```
### 8. Test Run
The following commands were used to successfully run the Rust code:
``` bash
[sysadmin@centos8s ~]$ cd MLP/FSAwRust-1-SortSearch/m1-bubblesort/
[sysadmin@centos8s m1-bubblesort]$ cargo run --release
   Compiling m1-bubblesort v0.1.0 (/home/sysadmin/MLP/FSAwRust-1-SortSearch/m1-bubblesort)
    Finished release [optimized] target(s) in 0.79s
     Running `target/release/m1-bubblesort`
Enter how many numbers to sort: 5
Enter maximum value: 100
[26 22 28 36 25]
[22 25 26 28 36]
The vector is sorted!
[sysadmin@centos8s m1-bubblesort]$ cargo run --release
    Finished release [optimized] target(s) in 0.01s
     Running `target/release/m1-bubblesort`
Enter how many numbers to sort: 100
Enter maximum value: 100
[57 9 89 57 52 42 81 72 37 77 64 86 0 9 95 65 87 29 94 25 82 25 40 73 73 75 35 80 91 26 9 11 8 64 22 37 92 59 17 37 70 97 90 37 22 86 39 70 62 29 32 31 9 80 89 89 14 52 93 33 48 3 32 27 15 71 1 44 98 76 36 47 90 68 39 85 30 35 56 4 4 50 53 96 1 74 70 7 16 87 95 39 75 74 33 48 65 34 7 79]
[0 1 1 3 4 4 7 7 8 9 9 9 9 11 14 15 16 17 22 22 25 25 26 27 29 29 30 31 32 32 33 33 34 35 35 36 37 37 37 37 39 39 39 40 42 44 47 48 48 50 52 52 53 56 57 57 59 62 64 64 65 65 68 70 70 70 71 72 73 73 74 74 75 75 76 77 79 80 80 81 82 85 86 86 87 87 89 89 89 90 90 91 92 93 94 95 95 96 97 98]
The vector is sorted!
```

## RESOURCES
* [Wikipedia - Cocktail shaker sort](https://en.wikipedia.org/wiki/Cocktail_shaker_sort)
* [Wikipedia - BubbleSort Pseudocode implementation](https://en.wikipedia.org/wiki/Bubble_sort#Pseudocode_implementation)
* [Rosetta Code - BubbleSort - Rust](https://rosettacode.org/wiki/Sorting_algorithms/Bubble_sort#Rust)
* [Wikipedia - Optimizing bubble sort](https://en.wikipedia.org/wiki/Bubble_sort#Optimizing_bubble_sort)
* [Rust Docs - Crate rand](https://docs.rs/rand/latest/rand/)
* [Wikipedia - Pseudo-random number generator - PRNG](https://en.wikipedia.org/wiki/Pseudorandom_number_generator)
* [Wikipedia - Linear congruential generator - LCG](https://en.wikipedia.org/wiki/Linear_congruential_generator)

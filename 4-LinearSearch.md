# 4. Linear Search
A linear search is a simple iterative process where every item in a list is examined until either the required items is found or the end of the list is reached, in which case the search has failed! It is simple but not very efficient for large numbers of items.

## WORKFLOW
### 1. Write `linear_search`
``` rust
// Return the target's location in the vector and the number of tests.
// If the item is not found, return -1 and the number of tests.
fn linear_search(vec: &Vec<i32>, target: i32) -> (i32, i32) {
	let max = vec.len();
	let mut s = 0i32;
	let mut res = -1i32;
	for i in 0..max {
		s += 1;
		if target == vec[i] {
			res = i as i32;
			break;
		}
	}
	return (res, s);
}
```
### 2. Write `main`
This will reuse many support functions from previous projects:
``` rust
fn main() {
	println!("Welcome to Linear Search.");
    let num: i32 = get_i32("Enter number of Items: ");
    let max: i32 = get_i32("Enter maximum value: ");
    let mut v: Vec<i32> = make_random_vec(num, max);
    print_vec(&mut v, num);
    loop {
		let targ: i32 = get_i32("Enter target (-1 to quit): ");
		if targ == -1 { break }
		let (targ_index, num_searched) = linear_search(&mut v, targ);
		if targ_index == -1 {
			println!("Target {} not found, {} tests", targ, num_searched);
		} else {
			println!("Target [{}] = {}, {} tests", targ, targ_index, num_searched);
		}
	}
}
```
### 3. Test Run
The entire program is visible at: [linearsearch.rs](linearsearch.rs.md)

Try this:
``` bash
[sysadmin@centos8s ~]$ cd MLP/FSAwRust-1-SortSearch/m4-linearsearch/
[sysadmin@centos8s m4-linearsearch]$ cargo run --release
   Compiling m4-linearsearch v0.1.0 (/home/sysadmin/MLP/FSAwRust-1-SortSearch/m4-linearsearch)
    Finished release [optimized] target(s) in 0.64s
     Running `target/release/m4-linearsearch`
Welcome to Linear Search.
Enter number of Items: 50
Enter maximum value: 50
[26 34 39 41 31 34 15 26 21 13 27 37 23 24 32 22 0 18 26 31 5 0 27 19 27 15 37 41 37 14 36 9 14 22 41 21 26 32 4 42 28 24 7 39 23 10 15 46 33 16]
Enter target (-1 to quit): 60
Target 60 not found, 50 tests
Enter target (-1 to quit): 30
Target 30 not found, 50 tests
Enter target (-1 to quit): 31
Target [31] = 4, 5 tests
Enter target (-1 to quit): 34
Target [34] = 1, 2 tests
Enter target (-1 to quit): 23
Target [23] = 12, 13 tests
Enter target (-1 to quit): -1
```
## RESOURCES
* [iq.opengenius - Linear search in Rust](https://iq.opengenus.org/linear-search-in-rust/)
* [Pavan Kumar - Linear search in Rust](https://chercher.tech/rust/linear-search-rust)
* [HackerTouch - Linear search in Rust](https://www.hackertouch.com/linear-search-in-rust.html)

### (end)


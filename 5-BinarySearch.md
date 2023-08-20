# 5. Binary Search
This milestone will reuse most of the `quicksort` code and perform in a similar way to the `linear_search` test system, to repeatedly search a sorted random array.

Binary search is an efficient method of locating a target value in a pre-sorted array. The algorithm proceeds by successive comparisons, reducing the size of the search area by half each time. The search area is divided into two almost equal parts and the higher or lower parts is discarded when it does not contain the target value.

![](https://upload.wikimedia.org/wikipedia/commons/c/c1/Binary-search-work.gif)_Credit:WikimediaCommons:Mazen.Embaby_

## WORKFLOW
### 1. Copy `quicksort` program
The `quicksort` submission was copied into a new project. The easiest way to do this was to create a new empty project and manually copy the `main.rs` file in place of the default one.
``` bash
[sysadmin@centos8s ~]$ cargo new MLP/FSAwRust-1-SortSearch/m5-binarysearch
[sysadmin@centos8s ~]$ cd MLP/FSAwRust-1-SortSearch/m5-binarysearch/src
[sysadmin@centos8s src]$ cp ~/MLP/FSAwRust-1-SortSearch/m2-quicksort/src/main.rs .
```
### 2. Modify `main` to generate a sorted random array
Here is the modified main function:
``` rust
// START HERE...
fn main() {
	println!("Welcome to Binary Search.");
    let num: i32 = get_i32("Enter number of Items: ");
    let max: i32 = get_i32("Enter maximum value: ");
    let mut v: Vec<i32> = make_random_vec(num, max);
    quicksort(&mut v);
    print_vec(&mut v, num);
    loop {
		let targ: i32 = get_i32("Enter target (-1 to quit): ");
		if targ == -1 { break }
		let (targ_index, num_searched) = binary_search(&mut v, targ);
		if targ_index == -1 {
			println!("Target {} not found, {} tests", targ, num_searched);
		} else {
			println!("Target [{}] = {}, {} tests", targ, targ_index, num_searched);
		}
	}
}
```
### 3. Write `binary_search`
``` rust
// Perform binary search.
// Return the target's location in the vector and the number of tests.
// If the item is not found, return -1 and the number of tests.
fn binary_search(vec: &Vec<i32>, target: i32) -> (i32, i32) {
	let max = vec.len() as usize;
	let mut upper = max - 1;
	let mut lower = 0usize;
	let mut mid: usize = (upper + lower) / 2;
	let mut found = false;
	let mut count = 0i32;
	while !found {
		if lower > upper { break }	// target not found
		mid = (upper + lower) / 2;
		count += 1;
		if vec[mid] == target {
			found = true			// target found
		} else {
			if vec[mid] < target {
				lower = mid + 1		// search upper part
			} else {
				upper = mid -1		//search lower part
			}
		}
	}
	if found {
		return (mid as i32, count)
	} else {
		return (-1i32, count)
	}
}
```
### 4. Change `main` to use `binary_search`
``` rust
// START HERE...
fn main() {
	println!("Welcome to Binary Search.");
    let num: i32 = get_i32("Enter number of Items: ");
    let max: i32 = get_i32("Enter maximum value: ");
    let mut v: Vec<i32> = make_random_vec(num, max);
    quicksort(&mut v);
    print_vec(&mut v, num);
    loop {
		let targ: i32 = get_i32("Enter target (-1 to quit): ");
		if targ == -1 { break }
		let (targ_index, num_searched) = binary_search(&mut v, targ);
		if targ_index == -1 {
			println!("Target {} not found, {} tests", targ, num_searched);
		} else {
			println!("Target [{}] = {}, {} tests", targ, targ_index, num_searched);
		}
	}
}
```
### 5. Test run
The complete program source code is available HERE.

Here are some test runs:
``` bash
[sysadmin@centos8s ~]$ cd MLP/FSAwRust-1-SortSearch/m5-binarysearch/src
[sysadmin@centos8s src]$ cargo run --release
   Compiling m5-binarysearch v0.1.0 (/home/sysadmin/MLP/FSAwRust-1-SortSearch/m5-binarysearch)
    Finished release [optimized] target(s) in 0.71s
     Running `/home/sysadmin/MLP/FSAwRust-1-SortSearch/m5-binarysearch/target/release/m5-binarysearch`
Welcome to Binary Search.
Enter number of Items: 50
Enter maximum value: 50
[0 1 1 3 8 9 9 10 10 12 13 16 16 22 22 22 23 24 24 24 25 26 26 27 27 28 28 30 30 31 32 32 32 32 34 37 38 42 42 42 44 45 45 46 47 47 48 49 49 49]
Enter target (-1 to quit): 40
Target 40 not found, 6 tests
Enter target (-1 to quit): 42
Target [42] = 37, 2 tests
Enter target (-1 to quit): -1
[sysadmin@centos8s src]$ 
```

## RESOURCES
* [Wikipedia -Binary Search](https://en.wikipedia.org/wiki/Binary_search_algorithm)
* [Khan Academy - Binary Search](https://www.khanacademy.org/computing/computer-science/algorithms/binary-search/a/binary-search)
* [Khan Academy - Implementing Binary Search of an Array](https://www.khanacademy.org/computing/computer-science/algorithms/binary-search/a/implementing-binary-search-of-an-array)

### (end)

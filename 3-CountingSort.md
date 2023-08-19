# 3. Counting Sort
Counting sort is an algorithm for sorting a collection of objects according to keys that are small positive integers. It is an integer sorting algorithm. It operates by counting the number of objects that possess distinct key values, and applying prefix sum on those counts to determine the positions of each key value in the output sequence. Its running time is linear in the number of items and the difference between the maximum key value and the minimum key value, so it is only suitable for direct use in situations where the variation in keys is not significantly greater than the number of items.

Counting sort is not a comparison sort; it uses key values as indexes into an array and the Ω(n log n) lower bound for comparison sorting will not apply.

This animation illustrates counting sort:

![](https://d18l82el6cdm1i.cloudfront.net/uploads/hrUDdYC7OH-countingsort.gif)_Credit:https://brilliant.org/wiki/counting-sort/_

## Workflow
### 1. Create `Customer` structure with `fmt` function
The following code was added:
``` rust
use std::fmt;
// Custome data structure
struct Customer {
    id: String,
    num_purchases: i32,
}
impl fmt::Display for Customer {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        write!(f, "({}, {})", self.id, self.num_purchases)
    }
}
```
### 2. Modify `make_random_vec` to use `Customer` structure
Based on the structure `Prng` from previous projects, the modified code for `make_random_vec` is shown below. Note that the number and maximum value of the random items is specifed as a parameter and uses the `get_i32` function called in `main`. `get_i32` was also inherited from a previous project:
``` rust
fn make_random_vec(num_items: i32, max: i32) -> Vec<Customer> {
    // Prepare a Prng.
    let mut prng = Prng::new();

    let mut vec: Vec<Customer> = Vec::with_capacity(num_items as usize);
    for i in 0..num_items {
		let cust = Customer {
			id: String::from(format!("C{}",i),
			num_purchases: i32::from(prng.next_i32(0, max))
			};
        vec.push(cust);
    }
    return vec;
}
```
### 3. Modify `print_vec`to use `Customer` structure
The `print_vec` function used in previous projects was modified to display a vector array of `Customer` structures instead of `i32` integers...

Code for the modified function is shown below:
``` rust
// Print at most num_items items using Customer::fmt trait.
fn print_vec(vec: &Vec<Customer>, num_items: i32) {
    let mut max = vec.len();
    if max > num_items as usize {
        max = num_items as usize;
    }
    print!("[");
    if max > 0usize {
		for i in 0usize..max {
			print!("{} ", vec[i];
		}
    println!("]");
}
```
### 4. Modify `check_sorted` to use `Customer` structure
`check_sorted` was also modified to verify the the `Custermer` array was sorted in ascending sequence of Number of Purchases (`num_purchases`):
``` rust
// Verify that the Vec is sorted.
fn check_sorted(vec: &Vec<Customer>) {
	let mut ok = true;
	for i in 1..vec.len() {
		if vec[i-1].num_purchases > vec[i].num_purchases {
			ok = false;
			break;
		}
	}
	if ok {
		println!("The vector is sorted!");
	} else {
	println!("The vector is NOT sorted!");
	}
}
```
### 5. Write `counting_sort`
``` rust
// Sort a vector array of Customers in to ascending order of number of Purchases.
fn counting_sort(cust_vec: &mut [Customer]) -> Vec<Customer> {
	let max = cust_vec.len() as usize;
	let mut counts = vec![0i32; max];
	let mut cust_sorted = vec![Customer{id: "_".to_string(), num_purchases: 0i32}; max];
	for i in 0..max {
		let items_purchased = cust_vec[i].num_purchases;
		counts[items_purchased as usize] += 1;
	}
	for i in 1..max {
		counts[i] += counts[i-1];
	}
	for i in 0..max {
		let j = max - i - 1;
		let c_id = (&cust_vec[j].id).to_string();
		let c_purch = cust_vec[j].num_purchases;
		let si = c_purch as usize;
		cust_sorted[(counts[si] as usize)-1].id = c_id;
		cust_sorted[(counts[si] as usize)-1].num_purchases = c_purch;
		counts[si] -= 1;
	}
	return cust_sorted;
}
```
### 6. Write `main` function
``` rust
// START HERE
fn main() {
    let num: i32 = get_i32("Enter how many numbers to sort: ");
    let max: i32 = get_i32("Enter maximum value: ");
    let mut v: Vec<Customer> = make_random_vec(num, max);
    print_vec(&mut v, num);
    let mut v_sorted: Vec<Customer> = counting_sort(&mut v);
    print_vec(&mut v_sorted, num);
    check_sorted(&mut v_sorted);
}
```
### 7. Test run
The complete program code is available here: [CountingSort.rs](CountingSort.rs.md).

A test run for 10 items with a range of values between 0 and 5 is shown below:
``` bash
[sysadmin@centos8s ~]$ cd MLP/FSAwRust-1-SortSearch/m3-countingsort/
[sysadmin@centos8s m3-countingsort]$ cargo run --release
   Compiling m3-countingsort v0.1.0 (/home/sysadmin/MLP/FSAwRust-1-SortSearch/m3-countingsort)
    Finished release [optimized] target(s) in 0.69s
     Running `target/release/m3-countingsort`
Enter how many numbers to sort: 10
Enter maximum value: 5
[(C0, 4) (C1, 3) (C2, 3) (C3, 0) (C4, 4) (C5, 1) (C6, 0) (C7, 1) (C8, 1) (C9, 0) ]
[(C3, 0) (C6, 0) (C9, 0) (C5, 1) (C7, 1) (C8, 1) (C1, 3) (C2, 3) (C0, 4) (C4, 4) ]
The vector is sorted!
```
Another test run with 100 items in a range from 0 to 99 is shown below:
``` bash
[sysadmin@centos8s m3-countingsort]$ cargo run --release
    Finished release [optimized] target(s) in 0.01s
     Running `target/release/m3-countingsort`
Enter how many numbers to sort: 100
Enter maximum value: 99
[(C0, 48) (C1, 25) (C2, 55) (C3, 48) (C4, 49) (C5, 84) (C6, 64) (C7, 16) (C8, 96) (C9, 5) (C10, 22) (C11, 51) (C12, 90) (C13, 40) (C14, 49) (C15, 31) (C16, 15) (C17, 62) (C18, 60) (C19, 49) (C20, 74) (C21, 95) (C22, 32) (C23, 18) (C24, 87) (C25, 17) (C26, 83) (C27, 84) (C28, 38) (C29, 57) (C30, 45) (C31, 86) (C32, 23) (C33, 31) (C34, 33) (C35, 50) (C36, 12) (C37, 34) (C38, 5) (C39, 38) (C40, 43) (C41, 37) (C42, 12) (C43, 56) (C44, 64) (C45, 22) (C46, 34) (C47, 45) (C48, 70) (C49, 81) (C50, 1) (C51, 29) (C52, 23) (C53, 26) (C54, 81) (C55, 77) (C56, 3) (C57, 24) (C58, 67) (C59, 48) (C60, 96) (C61, 23) (C62, 12) (C63, 52) (C64, 64) (C65, 64) (C66, 54) (C67, 25) (C68, 73) (C69, 56) (C70, 98) (C71, 35) (C72, 60) (C73, 7) (C74, 67) (C75, 18) (C76, 12) (C77, 76) (C78, 1) (C79, 68) (C80, 7) (C81, 68) (C82, 36) (C83, 77) (C84, 22) (C85, 8) (C86, 18) (C87, 43) (C88, 51) (C89, 19) (C90, 76) (C91, 43) (C92, 25) (C93, 65) (C94, 44) (C95, 6) (C96, 85) (C97, 59) (C98, 28) (C99, 62) ]
[(C50, 1) (C78, 1) (C56, 3) (C9, 5) (C38, 5) (C95, 6) (C73, 7) (C80, 7) (C85, 8) (C36, 12) (C42, 12) (C62, 12) (C76, 12) (C16, 15) (C7, 16) (C25, 17) (C23, 18) (C75, 18) (C86, 18) (C89, 19) (C10, 22) (C45, 22) (C84, 22) (C32, 23) (C52, 23) (C61, 23) (C57, 24) (C1, 25) (C67, 25) (C92, 25) (C53, 26) (C98, 28) (C51, 29) (C15, 31) (C33, 31) (C22, 32) (C34, 33) (C37, 34) (C46, 34) (C71, 35) (C82, 36) (C41, 37) (C28, 38) (C39, 38) (C13, 40) (C40, 43) (C87, 43) (C91, 43) (C94, 44) (C30, 45) (C47, 45) (C0, 48) (C3, 48) (C59, 48) (C4, 49) (C14, 49) (C19, 49) (C35, 50) (C11, 51) (C88, 51) (C63, 52) (C66, 54) (C2, 55) (C43, 56) (C69, 56) (C29, 57) (C97, 59) (C18, 60) (C72, 60) (C17, 62) (C99, 62) (C6, 64) (C44, 64) (C64, 64) (C65, 64) (C93, 65) (C58, 67) (C74, 67) (C79, 68) (C81, 68) (C48, 70) (C68, 73) (C20, 74) (C77, 76) (C90, 76) (C55, 77) (C83, 77) (C49, 81) (C54, 81) (C26, 83) (C5, 84) (C27, 84) (C96, 85) (C31, 86) (C24, 87) (C12, 90) (C21, 95) (C8, 96) (C60, 96) (C70, 98) ]
The vector is sorted!
```
Note that the range of values must always be less than the number of items to be sorted, otherwise the algorithm fails. this is demonstrated when a test run with 10 items with range between 0 and 100 is tried:
``` bash
[sysadmin@centos8s m3-countingsort]$ cargo run --release
    Finished release [optimized] target(s) in 0.01s
     Running `target/release/m3-countingsort`
Enter how many numbers to sort: 10
Enter maximum value: 100
[(C0, 81) (C1, 62) (C2, 91) (C3, 45) (C4, 32) (C5, 48) (C6, 70) (C7, 49) (C8, 50) (C9, 47) ]
thread 'main' panicked at 'index out of bounds: the len is 10 but the index is 81', src/main.rs:125:9
note: run with `RUST_BACKTRACE=1` environment variable to display a backtrace
```

## Resources
* [Brilliant.org - Counting Sort](https://brilliant.org/wiki/counting-sort/)
* [Wikipedia - Counting Sort](https://en.wikipedia.org/wiki/Counting_sort)
* [University of San Francisco (Computer Science) - Counting Sort demo](https://www.cs.usfca.edu/~galles/visualization/CountingSort.html)

### (end)

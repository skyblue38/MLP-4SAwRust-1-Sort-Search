``` rust
// Binary Search by Chris Freeman (20AUG23)

use std::time::{SystemTime, UNIX_EPOCH};
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

// Divide data into two partitions
fn partition(ar: &mut [i32]) -> usize {
	let lo = 0;
	let hi = ar.len() - 1;
	let pivot = ar[hi];		// Choose the last element as the pivot
	let mut i = -1i32;		// Temporary pivot index
	for j in lo..hi {
		// If the current element is less than or equal to the pivot
		if ar[j] <= pivot {
			// Move the temporary pivot index forward
			i = i + 1;
			// Swap the current element with the element at the temporary pivot index
			(ar[i as usize],ar[j]) = (ar[j],ar[i as usize])
		}
	}
	// Move pivot element between the smaller and larger elements
	i = i + 1;
	(ar[i as usize], ar[hi]) = (ar[hi], ar[i as usize]);	//swap the values
	return i as usize;			// the pivot index
}

// recursivelt partition an array until sorted
fn quicksort(ar: &mut [i32]) {
	let alen = ar.len();
	if alen < 2 { return }
	let p: usize = partition(ar);
	quicksort(&mut ar[0..p]);
	quicksort(&mut ar[p+1..alen]);
}

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
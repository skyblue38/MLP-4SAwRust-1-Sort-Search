``` rust
// Counting Sort by Chris Freeman (18AUG23)

use std::io;
use std::io::Write;
use std::time::{SystemTime, UNIX_EPOCH};
use std::fmt;

#[derive(Clone)]
struct Customer {
    id: String,
    num_purchases: i32,
}
impl fmt::Display for Customer {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        write!(f, "({}, {})", self.id, self.num_purchases)
    }
}

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

fn make_random_vec(num_items: i32, max: i32) -> Vec<Customer> {
    // Prepare a Prng.
    let mut prng = Prng::new();

    let mut vec: Vec<Customer> = Vec::with_capacity(num_items as usize);
    for i in 0..num_items {
//		let custid: String = format!("C{}",i);
		let cust = Customer {
			id: String::from(format!("C{}",i)),
			num_purchases: i32::from(prng.next_i32(0, max))
			};
        vec.push(cust);
    }
    return vec;
}

// Print at most num_items items.
fn print_vec(vec: &Vec<Customer>, num_items: i32) {
    let mut max = vec.len();
    if max > num_items as usize {
        max = num_items as usize;
    }
    print!("[");
    if max > 0usize {
		for i in 0usize..max {
			print!("{} ", vec[i]);
		}
		println!("]");
	}
}

// Verify that the Vec is sorted.
fn check_sorted(vec: &Vec<Customer>) {
	let mut ok = true;
	for i in 1..vec.len() {
		if vec[i-1].num_purchases > vec[i].num_purchases {
			println!("False at {}",i);
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
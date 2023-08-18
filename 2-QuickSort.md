# 2. Quick Sort
Quicksort is a *divide-and-conquer* algorithm. It works by selecting a **pivot** element from the array and partitioning the other elements into two sub-arrays, according to whether they are less than or greater than the pivot. For this reason, it is sometimes called partition-exchange sort. The sub-arrays are then sorted recursively. This can be done in-place, requiring small additional amounts of memory to perform the sorting.

Complexity analysis of quicksort shows that, on average, the algorithm takes *O* (*n* log*n* ) comparisons to sort *n* items. In the worst case, it makes *O* (*n*^2) comparisons.

![](https://upload.wikimedia.org/wikipedia/commons/6/6a/Sorting_quicksort_anim.gif)_Credit:https://en.wikipedia.org/wiki/User:RolandH_

(TBC)
## WORKFLOW
### 1. The `partition` function
The partition function is designed around the [Lomuto partition scheme(https://en.wikipedia.org/wiki/Quicksort#Lomuto_partition_scheme)]. In this case, the last item in the given `i32` array is used as a *pivot* value, and the array is modified in-place so that all values lower than the *pivot* are relocated to the beginning of the array, while the higher values are relocated to the end of the array, with the *pivot* placed between them. The index position of the *pivot* is then returned

The code for the `partition` function is shown below:
``` rust
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
```
### 2. The `quicksort` function
The `quicksort` function is used recursively to perform the sort, one partition at a time. The function immediately returns if the given `i32` array is less than 2 elements long, as in that case, no further sorting is required. Otherwise, the array is split using the `partition` function, and `quicksort` is called, passing each partition as a new array. Because the mutable array is partitioned in-place during the recursive descent, upon return from each recursion, the array is sorted into ascending sequence.

The code for the `quicksort` function is shown below:
``` rust
// recursivelt partition an array until sorted
fn quicksort(ar: &mut [i32]) {
	let alen = ar.len();
	if alen < 2 { return }
	let p: usize = partition(ar);
	quicksort(&mut ar[0..p]);
	quicksort
```
### 3. The `main` function
The `main` function will reuse support functions from the previous project to 
* accept a number from the operator: `get_i32`
* generate a sequence of random numbers: `make_random_vec`, `Prng` structure and `next_i32`function.
* populate an `i32` array with the numbers
* call the `quicksort` function to sort the generated array
* print the array values before and after sorting: `print_vec`

The code for the `main` function is shown below:
``` rust
fn main() {
	let num: i32 = get_i32("Enter how many numbers to sort: ");
    let max: i32 = get_i32("Enter maximum value: ");
    let mut v: Vec<i32> = make_random_vec(num, max);
    print_vec(&mut v, num);
	quicksort(&mut v);
	print_vec(&mut v, num);
}
```
### 4. Test Run
The complete program is available for download [HERE].

The following is a test run of 10 and 100 items:
``` bash
[sysadmin@centos8s ~]$ cd MLP/FSAwRust-1-SortSearch/m2-quicksort/
[sysadmin@centos8s m2-quicksort]$ cargo run --release
   Compiling m2-sortsearch v0.1.0 (/home/sysadmin/MLP/FSAwRust-1-SortSearch/m2-quicksort)
    Finished release [optimized] target(s) in 0.64s
     Running `target/release/m2-sortsearch`
Enter how many numbers to sort: 10
Enter maximum value: 100
[69 68 28 24 98 71 20 54 29 43]
[20 24 28 29 43 54 68 69 71 98]
[sysadmin@centos8s m2-quicksort]$ cargo run --release
    Finished release [optimized] target(s) in 0.01s
     Running `target/release/m2-sortsearch`
Enter how many numbers to sort: 100
Enter maximum value: 100
[79 16 81 0 35 65 49 15 71 12 70 31 43 7 28 90 19 91 75 76 92 82 30 91 96 15 30 73 83 58 73 75 12 99 39 55 92 33 55 94 19 40 88 4 73 82 12 20 1 1 53 54 46 61 60 11 60 18 85 24 66 34 36 21 80 55 91 90 90 45 16 35 85 94 98 98 27 23 23 52 82 58 8 12 72 31 26 1 91 74 32 97 32 46 21 97 65 78 26 44]
[0 1 1 1 4 7 8 11 12 12 12 12 15 15 16 16 18 19 19 20 21 21 23 23 24 26 26 27 28 30 30 31 31 32 32 33 34 35 35 36 39 40 43 44 45 46 46 49 52 53 54 55 55 55 58 58 60 60 61 65 65 66 70 71 72 73 73 73 74 75 75 76 78 79 80 81 82 82 82 83 85 85 88 90 90 90 91 91 91 91 92 92 94 94 96 97 97 98 98 99]
[sysadmin@centos8s m2-quicksort]$ 
```
## RESOURCES
* [*Grokking Algorithms* - Chapter 4 QuickSort ](https://livebook.manning.com/book/grokking-algorithms-second-edition/chapter-4/v-1)
* [Wikipedia - Quicksort Algorithm](https://en.wikipedia.org/wiki/Quicksort#Algorithm)
* [RosettaCode - QuickSort - Rust](https://rosettacode.org/wiki/Sorting_algorithms/Quicksort#Rust)

### (end)


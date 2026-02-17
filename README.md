[![GitHub go.mod Go version of a Go module](https://img.shields.io/github/go-mod/go-version/gomods/athens.svg)](https://github.com/KernelPryanic/bitset)
[![License](https://img.shields.io/badge/License-BSD_2--Clause-orange.svg)](https://opensource.org/licenses/BSD-2-Clause)

# ⚡ **BitSet** ⚡

This is a simple, though very fast, bitset implementation in Go derived from the [yourbasic/bit](github.com/yourbasic/bit) package. The goal of this package is providing the minimal sufficient set of operations on bitsets, remaining minimalistic and efficient as possible without overengineering.

## Installation

```go
go get github.com/KernelPryanic/bitset
```

## Usage

### Creating BitSets

```go
// Create an empty bitset
empty := bitset.New()

// Create a bitset with initial values
set := bitset.New(1, 3, 5, 7)
```

### Basic Operations

```go
// Add elements
set.Add(9)          // Add single element
set.AddRange(2, 5)  // Add range [2,3,4]

// Check membership
exists := set.Contains(3)    // true
absent := set.Contains(6)    // false

// Remove elements
set.Delete(3)               // Remove single element
set.DeleteRange(2, 5)       // Remove range [2,3,4]

// Get set information
size := set.Size()          // Number of elements
isEmpty := set.Empty()      // Check if set is empty
max := set.Max()           // Get maximum element

// Iterate through elements
set.Visit(func(n int) bool {
    fmt.Printf("%d ", n)
    return false  // Return true to stop iteration
})
```

### Set Operations

```go
set1 := bitset.New(1, 2, 3, 4, 5)
set2 := bitset.New(4, 5, 6, 7, 8)

// Create new sets from operations
intersection := bitset.And(set1, set2)  // Elements in both sets
union := bitset.Or(set1, set2)         // Elements in either set
symDiff := bitset.Xor(set1, set2)      // Elements in one set but not both
diff := bitset.AndNot(set1, set2)      // Elements in set1 but not in set2

// Modify existing sets
set1.And(set2)    // Keep only elements present in both sets
set1.Or(set2)     // Add all elements from set2
set1.Xor(set2)    // Keep elements present in one set but not both
set1.AndNot(set2) // Remove all elements present in set2
```

### Navigation

```go
set := bitset.New(1, 3, 5, 7, 9)

// Find next/previous elements
next := set.Next(4)     // Returns 5 (next element after 4)
prev := set.Prev(6)     // Returns 5 (previous element before 6)

// Copy sets
copy := set.Copy()      // Create a new copy
set2 := bitset.New()
set2.Set(set)          // Replace contents of set2 with set
```

### String Representation

```go
set := bitset.New(1, 2, 3, 5, 7, 8, 9, 10)
fmt.Println(set) // Outputs: {1..3 5 7..10}
```

## Benchmarking

The following benchmark results show that this bitset implementation is cuurently the fastest one in the core bitset operations and has the least number of allocations, comparing to the most popular solutions. Benchmarks code is in [bitset-bench](https://github.com/KernelPryanic/bitset-bench) repositry.

- Up to 3.17x faster than YourBasic/bit (1.61x on average) and 2.3x less memory
- Up to 39.89x faster than RoaringBitmap/roaring (10.12x on average) and 10.4x less memory
- Up to 1.7x faster than bits-and-blooms/bitset (1.7x on average) and 2.5x less memory

```sh
goos: linux
goarch: amd64
pkg: bitset-bench
cpu: 12th Gen Intel(R) Core(TM) i7-1270P
BenchmarkNew_KernelPryanic/size_10-8         	416101431	        14.55 ns/op	       8 B/op	       1 allocs/op
BenchmarkNew_KernelPryanic/size_100-8        	72343773	        81.69 ns/op	      16 B/op	       1 allocs/op
BenchmarkNew_KernelPryanic/size_1000-8       	 8618655	       700.6 ns/op	     128 B/op	       1 allocs/op
BenchmarkNew_KernelPryanic/size_10000-8      	  890434	      6825 ns/op	    1280 B/op	       1 allocs/op
BenchmarkNew_YourBasic/size_10-8             	154157577	        38.77 ns/op	      32 B/op	       2 allocs/op
BenchmarkNew_YourBasic/size_100-8            	40817985	       150.9 ns/op	      48 B/op	       3 allocs/op
BenchmarkNew_YourBasic/size_1000-8           	 5279248	      1128 ns/op	     272 B/op	       6 allocs/op
BenchmarkNew_YourBasic/size_10000-8          	  596094	     11082 ns/op	    4112 B/op	      10 allocs/op
BenchmarkNew_Roaring/size_10-8               	39293587	       153.7 ns/op	     160 B/op	       7 allocs/op
BenchmarkNew_Roaring/size_100-8              	15196503	       394.3 ns/op	     384 B/op	      10 allocs/op
BenchmarkNew_Roaring/size_1000-8             	 3169102	      1888 ns/op	    2176 B/op	      13 allocs/op
BenchmarkNew_Roaring/size_10000-8            	  260258	     22863 ns/op	   33312 B/op	      20 allocs/op
BenchmarkNew_BitsAndBlooms/size_10-8         	156515307	        38.17 ns/op	      40 B/op	       2 allocs/op
BenchmarkNew_BitsAndBlooms/size_100-8        	50930841	       118.6 ns/op	      48 B/op	       2 allocs/op
BenchmarkNew_BitsAndBlooms/size_1000-8       	 7387969	       809.4 ns/op	     160 B/op	       2 allocs/op
BenchmarkNew_BitsAndBlooms/size_10000-8      	  770175	      7717 ns/op	    1312 B/op	       2 allocs/op
BenchmarkAdd_KernelPryanic/small-8           	1000000000	         2.340 ns/op	       0 B/op	       0 allocs/op
BenchmarkAdd_KernelPryanic/medium-8          	1000000000	         2.339 ns/op	       0 B/op	       0 allocs/op
BenchmarkAdd_KernelPryanic/large-8           	1000000000	         2.357 ns/op	       0 B/op	       0 allocs/op
BenchmarkAdd_YourBasic/small-8               	1000000000	         2.340 ns/op	       0 B/op	       0 allocs/op
BenchmarkAdd_YourBasic/medium-8              	1000000000	         2.368 ns/op	       0 B/op	       0 allocs/op
BenchmarkAdd_YourBasic/large-8               	1000000000	         2.328 ns/op	       0 B/op	       0 allocs/op
BenchmarkAdd_Roaring/small-8                 	704256816	         8.225 ns/op	       0 B/op	       0 allocs/op
BenchmarkAdd_Roaring/medium-8                	300352508	        20.56 ns/op	       0 B/op	       0 allocs/op
BenchmarkAdd_Roaring/large-8                 	779435636	         7.778 ns/op	       0 B/op	       0 allocs/op
BenchmarkAdd_BitsAndBlooms/small-8           	1000000000	         2.320 ns/op	       0 B/op	       0 allocs/op
BenchmarkAdd_BitsAndBlooms/medium-8          	1000000000	         2.323 ns/op	       0 B/op	       0 allocs/op
BenchmarkAdd_BitsAndBlooms/large-8           	1000000000	         2.331 ns/op	       0 B/op	       0 allocs/op
BenchmarkContains_KernelPryanic/small_present-8         	1000000000	         5.000 ns/op	       0 B/op	       0 allocs/op
BenchmarkContains_KernelPryanic/small_absent-8          	1000000000	         5.000 ns/op	       0 B/op	       0 allocs/op
BenchmarkContains_KernelPryanic/large_present-8         	1000000000	         5.000 ns/op	       0 B/op	       0 allocs/op
BenchmarkContains_KernelPryanic/large_absent-8          	1000000000	         5.000 ns/op	       0 B/op	       0 allocs/op
BenchmarkContains_YourBasic/small_present-8             	1000000000	         5.000 ns/op	       0 B/op	       0 allocs/op
BenchmarkContains_YourBasic/small_absent-8              	1000000000	         5.000 ns/op	       0 B/op	       0 allocs/op
BenchmarkContains_YourBasic/large_present-8             	1000000000	         5.000 ns/op	       0 B/op	       0 allocs/op
BenchmarkContains_YourBasic/large_absent-8              	1000000000	         5.000 ns/op	       0 B/op	       0 allocs/op
BenchmarkContains_Roaring/small_present-8               	993212557	         5.905 ns/op	       0 B/op	       0 allocs/op
BenchmarkContains_Roaring/small_absent-8                	858776072	         6.935 ns/op	       0 B/op	       0 allocs/op
BenchmarkContains_Roaring/large_present-8               	1000000000	         5.000 ns/op	       0 B/op	       0 allocs/op
BenchmarkContains_Roaring/large_absent-8                	1000000000	         5.000 ns/op	       0 B/op	       0 allocs/op
BenchmarkContains_BitsAndBlooms/small_present-8         	1000000000	         5.000 ns/op	       0 B/op	       0 allocs/op
BenchmarkContains_BitsAndBlooms/small_absent-8          	1000000000	         5.000 ns/op	       0 B/op	       0 allocs/op
BenchmarkContains_BitsAndBlooms/large_present-8         	1000000000	         5.000 ns/op	       0 B/op	       0 allocs/op
BenchmarkContains_BitsAndBlooms/large_absent-8          	1000000000	         5.000 ns/op	       0 B/op	       0 allocs/op
BenchmarkAnd_KernelPryanic/small_sets-8                 	585634084	        10.26 ns/op	       8 B/op	       1 allocs/op
BenchmarkAnd_KernelPryanic/large_sets-8                 	26665315	       226.1 ns/op	    1280 B/op	       1 allocs/op
BenchmarkAnd_YourBasic/small_sets-8                     	207960330	        28.89 ns/op	      32 B/op	       2 allocs/op
BenchmarkAnd_YourBasic/large_sets-8                     	23051258	       257.4 ns/op	    1304 B/op	       2 allocs/op
BenchmarkAnd_Roaring/small_sets-8                       	50966869	       114.2 ns/op	     152 B/op	       6 allocs/op
BenchmarkAnd_Roaring/large_sets-8                       	 1534644	      3905 ns/op	    6920 B/op	       6 allocs/op
BenchmarkAnd_BitsAndBlooms/small_sets-8                 	193552717	        31.06 ns/op	      40 B/op	       2 allocs/op
BenchmarkAnd_BitsAndBlooms/large_sets-8                 	22592887	       264.5 ns/op	    1312 B/op	       2 allocs/op
BenchmarkOr_KernelPryanic/small_sets-8                  	552340365	        10.74 ns/op	       8 B/op	       1 allocs/op
BenchmarkOr_KernelPryanic/large_sets-8                  	25121366	       239.8 ns/op	    1280 B/op	       1 allocs/op
BenchmarkOr_YourBasic/small_sets-8                      	190544498	        31.36 ns/op	      32 B/op	       2 allocs/op
BenchmarkOr_YourBasic/large_sets-8                      	26454956	       225.4 ns/op	    1304 B/op	       2 allocs/op
BenchmarkOr_Roaring/small_sets-8                        	46533310	       123.0 ns/op	     160 B/op	       6 allocs/op
BenchmarkOr_Roaring/large_sets-8                        	 1000000	      5806 ns/op	    8336 B/op	       6 allocs/op
BenchmarkOr_BitsAndBlooms/small_sets-8                  	183226694	        32.70 ns/op	      40 B/op	       2 allocs/op
BenchmarkOr_BitsAndBlooms/large_sets-8                  	21561354	       278.3 ns/op	    1312 B/op	       2 allocs/op
BenchmarkXor_KernelPryanic/small_sets-8                 	548258488	        10.70 ns/op	       8 B/op	       1 allocs/op
BenchmarkXor_KernelPryanic/large_sets-8                 	24955268	       270.1 ns/op	    1280 B/op	       1 allocs/op
BenchmarkXor_YourBasic/small_sets-8                     	175430082	        33.91 ns/op	      32 B/op	       2 allocs/op
BenchmarkXor_YourBasic/large_sets-8                     	19108676	       339.3 ns/op	    1304 B/op	       2 allocs/op
BenchmarkXor_Roaring/small_sets-8                       	40061660	       146.6 ns/op	     160 B/op	       6 allocs/op
BenchmarkXor_Roaring/large_sets-8                       	  832849	      6826 ns/op	    8336 B/op	       6 allocs/op
BenchmarkXor_BitsAndBlooms/small_sets-8                 	162438415	        36.87 ns/op	      40 B/op	       2 allocs/op
BenchmarkXor_BitsAndBlooms/large_sets-8                 	17967027	       344.1 ns/op	    1312 B/op	       2 allocs/op
BenchmarkAndNot_KernelPryanic/small_sets-8              	517138628	        11.58 ns/op	       8 B/op	       1 allocs/op
BenchmarkAndNot_KernelPryanic/large_sets-8              	24691016	       242.4 ns/op	    1280 B/op	       1 allocs/op
BenchmarkAndNot_YourBasic/small_sets-8                  	207347138	        29.10 ns/op	      32 B/op	       2 allocs/op
BenchmarkAndNot_YourBasic/large_sets-8                  	22995970	       262.2 ns/op	    1304 B/op	       2 allocs/op
BenchmarkAndNot_Roaring/small_sets-8                    	51258081	       116.9 ns/op	     152 B/op	       6 allocs/op
BenchmarkAndNot_Roaring/large_sets-8                    	  631980	      9670 ns/op	   15144 B/op	       8 allocs/op
BenchmarkAndNot_BitsAndBlooms/small_sets-8              	181564711	        32.89 ns/op	      40 B/op	       2 allocs/op
BenchmarkAndNot_BitsAndBlooms/large_sets-8              	19502276	       309.9 ns/op	    1312 B/op	       2 allocs/op
BenchmarkCopy_KernelPryanic/small_set-8                 	520315304	        11.49 ns/op	      16 B/op	       1 allocs/op
BenchmarkCopy_KernelPryanic/large_set-8                 	36169732	       163.9 ns/op	    1280 B/op	       1 allocs/op
BenchmarkCopy_Roaring/small_set-8                       	46287285	       129.6 ns/op	     316 B/op	       7 allocs/op
BenchmarkCopy_Roaring/large_set-8                       	 5627685	      1067 ns/op	    8404 B/op	       7 allocs/op
BenchmarkCopy_BitsAndBlooms/small_set-8                 	165797050	        37.20 ns/op	      48 B/op	       2 allocs/op
BenchmarkCopy_BitsAndBlooms/large_set-8                 	21249416	       298.9 ns/op	    1312 B/op	       2 allocs/op
PASS
```

*Benchmarking was conducted on the P-cores.*

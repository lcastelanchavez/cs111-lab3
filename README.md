# Hash Hash Hash
This lab is an introduction to locking and safe concurrent programming practices. 
There are two implementations that both use mutex locks to make using a hash table thread-safe.
The first implementation uses only one mutex lock to ensure thread safety; thus slower.
The second implementation uses more mutex locks to increase performance.


## Building
```type in shell:
    make
```

## Running
```shell
    ./hash_table_tester -t <num_threads> -s <num_elem_per_thread>
```
The -t flag specifies the number of threads to use. (Default is 4)
The -s flag specifies the number of elements per thread to add onto the hash table (Default is 25,000)

This runs all implementations (base single thread and both multi-threaded) hash_table alterations. 
It outputs the generation time and performance of each implementation as well as any missing entries in the hash table. 

Example output: 
```
    $ ./hash-table-tester -t 6 -s 50000
    Generation: 58,101 usec
    Hash table base: 169,511 usec
        - 0 missing
    Hash table v1: 469,322 usec
        - 0 missing
    Hash table v2: 58,584 usec
        - 0 missing
```



## First Implementation
In the `hash_table_v1_add_entry` function, I added a mutex lock to get the hash table and list entries.
Then I added a lock around insertion into the singly-linked list in the hash table.

### Performance
Example output: 
```
    $ ./hash-table-tester -t 6 -s 50000
    ...
    Hash table base: 169,511 usec
        - 0 missing
    Hash table v1: 469,322 usec
        - 0 missing
    ...
```
Version 1 is a little slower than the base version due to multiple threads waiting on a single locked resource. 

## Second Implementation
In the `hash_table_v2_add_entry` function, I added a lock per hash table entry. 
This ensures all threads don't wait if not accessing the same resource inside the hash table. 

### Performance
Example output: 
```
    $ ./hash-table-tester -t 6 -s 50000
    Generation: 58,101 usec
    Hash table base: 169,511 usec
        - 0 missing
    Hash table v1: 469,322 usec
        - 0 missing
    Hash table v2: 58,584 usec
        - 0 missing
```
V2 implementation is much faster than v1 because locks don't have to wait for the whole resource to continue their work. 


Executing the following command around 10 times:
```
    ./hash-table-tester -t 4 -s 50000
```
Gives me the following:
```
    Hash table base: 81-85,000 usec on average
    Hash table version 1: 156-167,000 usec on average
    Hash table version 2: 20-25,000 usec on average

```

Increasing the thread count and executing the following command around 10 times:
```
    ./hash-table-tester -t 6 -s 50000
```
Gives me the following:
```
    Hash table base: 160-170,000 usec on average
    Hash table version 1: 430-500,000 usec on average
    Hash table version 2: 45-65,000 usec on average

```

Speedup is not proportional to increase of thread count.



## Cleaning up
```shell
    make clean
```
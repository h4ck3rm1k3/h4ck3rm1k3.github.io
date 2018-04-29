
based on https://github.com/eschulte/tracer


# int x

create a simple int

    echo "int x;" > test1a.c

compile it

    cat test1a.c | ./tracer -o test.trace ../gcc-1/build/gcc/cc1plus

# simple struct
    echo "struct foo {  int x; };struct foo y;" > test1.c

compile

    cat test1.c | ./tracer -o test.trace2 ../gcc-1/build/gcc/cc1plus

# create empty
    echo "\n" > empty.c

build an empty
     ./tracer -o empty.trace ../gcc-1/build/gcc/cc1plus empty.c 

# compare

compare the empty trace to one with the struct and find what instructions more are executed

diff empty.trace test.trace2  > trace2_more_than_empty

    grep -c '<' trace2_more_than_empty

gives you

    481

    grep -c '>' trace2_more_than_empty 

gives you

     373279

There are quite a few more functions called

    grep  '>' trace2_more_than_empty | sort | uniq -c | sort -n > trace2_more_than_empty.diffcount


# empty trace

`uniq -c` gives you the count of items based on a sorted list

    cat test.trace2 | sort | uniq -c | sort -n > t2
    cat test.trace | sort | uniq -c | sort -n > t1
    cat empty.trace | sort | uniq -c | sort -n > t0


cut the id off the count

    cut -b 9-20 t2  > t2a
    cut -b 9-20 t1  > t1a
    cut -b 9-20 t0  > t0a

sort the ids

    sort -n t1a > t1b
    sort -n t2a > t2b
    sort -n t0a > t0b

# extract the new instructions

    diff t0b t1b | grep '>' | cut -b 3- > struct_instructions


    diff struct_instructions int_instructions | grep '>' > int_not_struct
    diff struct_instructions int_instructions | grep '<' > struct_not_int

Example output

````

< 8060267
< 8060270
< 8060274
< 8060278

````

Look up address with x in gdb

  x addr


and run it in gdb

    gdb ../gcc-1/build/gcc/cc1plus

break on main

    b main

run to main
    c

now you can inspect all the functions.

so just replace all the > in the test with x

````

x 8060267
x 8060270
x 8060274
x 8060278

````

# Example output 

These are inside one function

```
(gdb) 0xa371dc <hash_table<list_hasher, xcallocator>::find_slot_with_hash(list_proxy* const&, unsigned int, insert_option)>:	0xe5894855
(gdb) 0xa371dd <hash_table<list_hasher, xcallocator>::find_slot_with_hash(list_proxy* const&, unsigned int, insert_option)+1>:	0x48e58948
(gdb) 0xa371e0 <hash_table<list_hasher, xcallocator>::find_slot_with_hash(list_proxy* const&, unsigned int, insert_option)+4>:	0x40ec8348
(gdb) 0xa371e4 <hash_table<list_hasher, xcallocator>::find_slot_with_hash(list_proxy* const&, unsigned int, insert_option)+8>:	0xd87d8948
```

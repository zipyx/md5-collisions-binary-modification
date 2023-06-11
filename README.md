## Report

The report is split into two parts outlining the process.

- **Part 1**
- **Part 2**

## Part 1: MD5 Collisions

1.1 Using `md5collgen` tool running the below command.

```bash
# Running command
md5collgen -p prefix.txt -o out1 out2
```

- Output (code format)

```bash
# Creat prefix file
touch prefix.txt

# Display of files in current directory
 ~ 🡒  ll
.rwxr-xr-x  3.2 MB  2023  md5collgen
.rw-r--r--    0 B   2023  prefix.txt

# Running command
 ~ 🡒  './md5collgen -p prefix.txt -o out1 out2'
MD5 collision generator v1.5
by Marc Stevens (http://www.win.tue.nl/hashclash/)

Using output filenames: 'out1' and 'out2'
Using prefixfile: 'prefix.txt'
Using initial value: '0123456789abcdeffedcba9876543210'

'
Generating first block: ........
Generating second block: W....
Running time: 1.73681 s
'

# Display list of files in current directory after command
 ~ 🡒  ll
.rwxr-xr-x   3.2 MB  2023  md5collgen
.rw-r--r--  '128 B   2023  out1'
.rw-r--r--  '128 B   2023  out2'
.rw-r--r--    '0 B   2023  prefix.txt'
```

- 1.2.1 The prefix file I created had '0' bytes. Due to the input of the file having `0` bytes. The output being `out1` and `out2` came out to be:
  - `128 B` (bytes) for `out1`
  - `128 B` (bytes) for `out2`

```bash
# I have omitted user and dates from displaying
 ~ 🡒  ll
.rwxr-xr-x   3.2 MB  2023  md5collgen
.rw-r--r--  '128 B   2023  out1'
.rw-r--r--  '128 B   2023  out2'
.rw-r--r--     0 B   2023  prefix.txt
```

- 1.2.2 The size of the output files increased and generating second block differed as well as the running time.

```bash
# Create arbitrary data up to 64 bytes in a file using the 'truncate' command
 ~ 🡒  truncate --size=64 64_byte_prefix.txt

# List files in directory
 ~ 🡒  ll
.rw-r--r--   '64 B   2023  64_byte_prefix.txt'
.rwxr-xr-x   3.2 MB  2023  md5collgen
.rw-r--r--  '128 B   2023  out1'
.rw-r--r--  '128 B   2023  out2'
.rw-r--r--     0 B   2023  prefix.txt

 ~ 🡒  ./md5collgen -p 64_byte_prefix.txt -o out1 out2
MD5 collision generator v1.5
by Marc Stevens (http://www.win.tue.nl/hashclash/)

Using output filenames: 'out1' and 'out2'
Using prefixfile: '64_byte_prefix.txt'
Using initial value: 'ac1d1f03d08ea56eb767ab1f91773174'

'
Generating first block: .......
Generating second block: S01.....
Running time: 1.84464 s
'

# New output file size
 ~ 🡒  ll
.rw-r--r--   '64 B   2023  64_byte_prefix.txt'
.rwxr-xr-x   3.2 MB  2023  md5collgen
.rw-r--r--  '192 B   2023  out1'
.rw-r--r--  '192 B   2023  out2'
.rw-r--r--     0 B   2023  prefix.txt
```

2. Description in detail with screenshots.

I downloaded the `c` file from moodle and named it `prog.c`. Then I generated the first binary file without modifying it's content and named it `a_xyz.out` using the following command:

```bash
# Named the file a_xyz as the the array was filled with 'A's
cc prog.c -o a_xyz.out

# List files
 ~ 🡒  ll
.rw-r--r--    64 B   2023  64_byte_prefix.txt
.rwxr-xr-x   '15 KB  2023  a_xyz.out'
.rwxr-xr-x   3.2 MB  2023  md5collgen
.rw-r--r--   192 B   2023  out1
.rw-r--r--   192 B   2023  out2
.rw-r--r--     0 B   2023  prefix.txt

# Ran the binary file with the output
 ~ 🡒  ./a_xyz.out
```

Screenshot `prog.c` with output as is.

![image](https://i.imgur.com/EKjh0Vv.png)

Screenshot output (`a_xyz.out`)

![image](https://i.imgur.com/gh90Yz5.png)

I then edited the `xyz` array in `prog.c` and replaced all `'A'` with `'B'` instead and repeated the process above.

```bash
# Named the file a_xyz as the the array was filled with 'A's
cc prog.c -o b_xyz.out

# List files
 ~ 🡒  ll
.rw-r--r--    64 B   2023  64_byte_prefix.txt
.rwxr-xr-x   '15 KB  2023  a_xyz.out'
.rwxr-xr-x   '15 KB  2023  b_xyz.out'
.rwxr-xr-x   3.2 MB  2023  md5collgen
.rw-r--r--   192 B   2023  out1
.rw-r--r--   192 B   2023  out2
.rw-r--r--     0 B   2023  prefix.txt

# Ran the binary file with the output
 ~ 🡒  ./b_xyz.out
```

Screenshot `prog.c` with `xyz` array modified

![image](https://i.imgur.com/ki8Pd1s.png)

Screenshot output (`b_xyz.out`)

![image](https://i.imgur.com/Qhgptq9.png)

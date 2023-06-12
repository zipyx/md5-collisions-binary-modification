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

**Hex modification of binary file.**

| Bytes            | Note                                                                                                                  |
| ---------------- | --------------------------------------------------------------------------------------------------------------------- |
| `0x0` - `0x3044` | `a_xyz.out` bytes from the `.ELF` byte up until the first four `'A'` in the array (i.e - `['A', 'A', 'A', 'A', ...]`) |
| `0x0` - `0x3044` | `b_xyz.out` bytes from the `.ELF` byte up until the first four `'B'` in the array (i.e - `['B', 'B', 'B', 'B', ...]`) |

```bash
# command used to modify a_xyz binary file to get the prefix
head -c 12356 a_xyz.out > a_prefix

# command used to modify b_xyz binary file to get the prefix
head -c 12356 b_xyz.out > b_prefix
```

- Screenshot (`a_prefix` and `b_prefix`)

  ![image](https://i.imgur.com/DIvJISx.png)

  ![image](https://i.imgur.com/XPiJZD7.png)

- Using hex editor `bless` in linux to validate prefix modification.

  - `a_prefix`

    ![image](https://i.imgur.com/mUPgFHR.png)

  - `b_prefix`

    ![image](https://i.imgur.com/XKT89sH.png)

| Bytes               | Note                                                                             |
| ------------------- | -------------------------------------------------------------------------------- |
| `0x30fa` - `0x3d6f` | `a_xyz.out` bytes from last 14 bytes in the `xyz` `'A'` array to the end of file |
| `0x30fa` - `0x3d6f` | `b_xyz.out` bytes from last 14 bytes in the `xyz` `'B'` array to the end of file |

```bash
# command used to get the suffix of a_xyz binary file
tail -c +12539 a_xyz.out > a_suffix

# command use to get the suffix of b_xyz binary file
tail -c +12539 b_xyz.out > b_suffix
```

- Screenshot (`a_suffix` and `b_suffix`)

![image](https://i.imgur.com/PJJ3nrp.png)

![image](https://i.imgur.com/kvoum7E.png)

- Using hex editor `bless` in linux to validate suffix modification.

  - `a_suffix`

    ![image](https://i.imgur.com/1wAVaQc.png)

  - `b_suffix`

    ![image](https://i.imgur.com/GVTNLrD.png)

Since the above has been validated, I ran `md5collgen` to satisfy the below equation:

```bash
# Therefore
MD5 (prefix || P) = MD5 (prefix || Q)
```

- Output

```bash
# Run command
 ~ 🡒  ./md5collgen -p prefix -o p_output q_output
MD5 collision generator v1.5
by Marc Stevens (http://www.win.tue.nl/hashclash/)

Using output filenames: 'p_output' and 'q_output'
Using prefixfile: 'prefix'
Using initial value: '32255c676e2608603354b5e505aad692'

'
Generating first block: ............
Generating second block: S10.....................
Running time: 5.82972 s
'
```

Since the above has been created

- `p` now represents `p_output`
- `q` now represents `q_output`

Based on **Merkle Damgard** construction of `MD5`, appending the same suffix to the above outputs will result with the same hash value. That is - true for any suffix.

```bash
# Therefore
MD5 (prefix || P || suffix) = MD5 (prefix || Q || suffix)
```

- Appending data

```bash
# Appending a_prefix + p_output + a_suffix to file named a_p_a_result
cat a_prefix p_output a_suffix > a_p_a_result

# Appending a_prefix + p_output + b_suffix to file named a_p_b_result
cat a_prefix p_output b_suffix > a_p_b_result

# Appending b_prefix + q_output + b_suffix to file named b_q_b_result
cat b_prefix q_output b_suffix > b_q_b_result

# Appending b_prefix + q_output + a_suffix to file named b_q_a_result
cat b_prefix q_output a_suffix > b_q_a_result
```

- Final binary results

`# (a prefix || p || a suffix)
🡒 ./a_p_a_result
414141417f454c46211000000000303e010005010000000400000000f0350000000000400380d04001e01d060004000400000000400000000400000000d82000000d8200000080000000300040001830000001830000001830000001c00000001c00000001000000010004000000000000000`

`# (a prefix || p || b suffix)
🡒 ./a_p_b_result
414141417f454c46211000000000303e010005010000000400000000f0350000000000400380d04001e01d060004000400000000400000000400000000d82000000d8200000080000000300040001830000001830000001830000001c00000001c00000001000000010004000000000000000`

`# (b prefix || q || b suffix)
🡒 ./b_q_b_result
424242427f454c46211000000000303e010005010000000400000000f0350000000000400380d04001e01d060004000400000000400000000400000000d82000000d8200000080000000300040001830000001830000001830000001c00000001c00000001000000010004000000000000000`

`# (b prefix || q || a suffix)
🡒 ./b_q_a_result
424242427f454c46211000000000303e010005010000000400000000f0350000000000400380d04001e01d060004000400000000400000000400000000d82000000d8200000080000000300040001830000001830000001830000001c00000001c00000001000000010004000000000000000`

`# (a prefix || q || a suffix)
🡒 ./a_q_a_result
414141417f454c46211000000000303e010005010000000400000000f0350000000000400380d04001e01d060004000400000000400000000400000000d82000000d8200000080000000300040001830000001830000001830000001c00000001c00000001000000010004000000000000000`

`# (a prefix || q || b suffix)
🡒 ./a_q_b_result
414141417f454c46211000000000303e010005010000000400000000f0350000000000400380d04001e01d060004000400000000400000000400000000d82000000d8200000080000000300040001830000001830000001830000001c00000001c00000001000000010004000000000000000`

`# (b prefix || p || b suffix)
🡒 ./b_p_b_result
424242427f454c46211000000000303e010005010000000400000000f0350000000000400380d04001e01d060004000400000000400000000400000000d82000000d8200000080000000300040001830000001830000001830000001c00000001c00000001000000010004000000000000000`

`# (b prefix || p || a suffix)
 🡒  ./b_p_a_result
424242427f454c46211000000000303e010005010000000400000000f0350000000000400380d04001e01d060004000400000000400000000400000000d82000000d8200000080000000300040001830000001830000001830000001c00000001c00000001000000010004000000000000000`

- Original (`a_xyz` and `b_xyz` array)

` 🡒  ./a_xyz.out
4141414141414141414141414141414141414141414141414141414141414141414141414141414141414141414141414141414141414141414141414141414141414141414141414141414141414141414141414141414141414141414141414141414141414141414141414141414141414141414141414141414141414141414141414141414141414141414141414141414141414141414141414141414141414141414141414141414141414141414141414141414141414141414141414141414141414141`

` 🡒  ./b_xyz.out
4242424242424242424242424242424242424242424242424242424242424242424242424242424242424242424242424242424242424242424242424242424242424242424242424242424242424242424242424242424242424242424242424242424242424242424242424242424242424242424242424242424242424242424242424242424242424242424242424242424242424242424242424242424242424242424242424242424242424242424242424242424242424242424242424242424242424242`

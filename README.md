# zxcvbn-c
This is a C/C++ implementation of the zxcvbn password strength estimation.

The code is intended to be included as part of the source of a C/C++ program. Like the
original this code is for character sets which use single byte characters primarily in the
code range 0x20 to 0x7E.

The original coffee script version is available at 
 https://github.com/lowe/zxcvbn

An article on the reasons for zxcvbn is at
https://tech.dropox.com/2012/04/zxcvbn-realistic-password-strength-estimation

##Building

The makefile will build several test programs to test the code. It shows the steps needed
to use the code in C and C++ programs, using the dictionary data read from file or included
within the program executable.
The makefile has only been tried on Linux using GCC version 4.8.4, but should be faily
portable to other systems.

When dictionary data is included in your program's executable, the files `zxcvbn.c` ,
`zxcvbn.h` , `dict-src.h` are used in your program. 

When dictionary data is read from file, the files `zxcvbn.c` , `zxcvbn.h` ,  `dict-crc.h`
and `zxcvbn.dict` are used in your program, compiled with `#define USE_DICT_FILE`. The CRC
of the dictionary data file is written to `dict-crc.h` so your executable can detect
corruption of the data.

Rename `zxcvbn.c` to `zxcvbn.cpp` (or whatever your compiler uses) to compile as C++.

The `dict*.h` and `zxcvbn.dict` files are generated by the dictgen program compiled from
dict-generate.cpp (see makefile for details).

##Using

Initially call `ZxcvbnInit()` with the pathname of the `zxcvbn.dict` file. This can be
omitted when dictionary data is included in the executable.

Call `ZxcvbnMatch()` with the password and optional user dictionary to get the entropy
estimation and optional information on the password parts (which will need freeing with
`ZxcvbnFreeInfo()` after use). Do this for each password to be tested, or as each character
of it is entered into your program. The optional user dictionary can change between each
call.

Finally call `ZxcvbnUninit()` to free the dictionary data from read from file. This can be
omitted when dictionary data is included in the executable.

Review the test program in `test.c` for an example.


## Differences from the original version.

The entropy calculated will sometimes differ from the original because of

* The UK keyboard layout is also included, so there are additional spacial sequences, e.g.
**;'#** is a spacial sequence.
* The different character classes in a password are taken into account when calculating the
strength of brute-force matches.
* Dijktra's path searching algorithm is used to combine parts of the entered password. This
can result in the found parts of the password being combined differently than the
original coffee script. E.g. the password **passwordassword**
is combined by the original coffee script as **p** (3.5 bits) + **asswordassword** (12.6
bits) + multiple part allowance (1.0bit) to give total entropy of 17.1 bits. This
implementation combines it as **password** (1.0 bit) + **assword** (11.6 bits) + multiple
part allowance (1.0bit) to give 13.6 bits.
* For multi part passwords the original coffee script version multiplies the number of
guesses needed by the factorial of the number of parts. This is not possible in this
version as Dijktra's algorithm is used. Instead one bit entropy is added for the part at the
end of the password, 1.7 bits for each part in the middle of a password and nothing
for the part at the beginning. This gives similar results compared to the coffee script
version when there are 4 or less parts, but will differ significantly when there are many
parts (which is likely to be a rare occurrence).


## References

The original coffee-script version is available at 
 https://github.com/lowe/zxcvbn

The dictionary words are taken from the original coffee script version.

Dictionary trie encoding (used for by the word lookup code) based on idea from the Caroline
Word Graph from
http://www.pathcom.com/~vadco/cwg.html

## License

MIT License

* http://www.opensource.org/licenses/mit-license.php


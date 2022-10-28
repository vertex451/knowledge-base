# Encoding

## base36 

(a-z ,0-9)

## base62 

(A-Z, a-z, 0-9)

## base64 

(A-Z, a-z, 0-9, +, /)

## ASCII

ASCII encodes 128 specified characters into seven-bit integers
Abbreviated from **American Standard Code for Information Interchange**. Pronounced as "ask ee" or "A S C I I" 
ASCII codes represent text in computers, telecommunications equipment, and other devices.
(A-Z, a-z, 0-9, punctuation symbols)

## Unicode 

As of Version 15.0, the Unicode Standard contains 149,186 characters

UTF-8(Unicode Transformation Format) is an encoding system for Unicode. It represents characters in one-byte(8bits) units. There can be from 1 to 4 units, so thats why we can cover more than a 

## URL Encoding (Percent Encoding)

(A-Z, a-z, 0-9, `"-"`, `"."`, `"_"`, `"~"`)

URL encoding is also called percent encoding since it uses percent sign (`%`) as an escape character.

There are **reserved** characters that have special meaning within URLs, so when we need to transmit any data in the URL that contain these disallowed characters, we encode them.



# Hashing

## MD5

Message Digest Method 5 is a cryptographic hash algorithm used to generate a 128-bit digest from a string of any length. It represents the digests as 32 digit hexadecimal numbers.

Good online demo: https://fthb321.github.io/MD5-Hash/MD5OurVersion2.html

### Steps in MD5 Algorithm

#### 1. Padding Bits

Output of this step shoud result in message length of (512 * z)+448 bits, where z is any number.)
Why 448? because we reserved 64 bits(512-448) to store the length of original message.

For example, if the message is 1472 bits long, then you would be able to use it as an MD5 hash, because 1472 % 512 = 448. 
If the file was 1400 bits long, then you would need to pad in an extra 72 bits before you could run the rest of the MD5 algorithm.

#### 2.  Padding length

Message length in bits we store in 64 bits value and append this to our previos bit-code. And now bitcode is multiple by 512.

#### 3. Initialize MD Buffer

You also need to initialize four different buffers, namely A, B, C, and D (Letters are constants, assigned for Hash collection). These buffers are 32 bits each and are initialized as follows:
```
A = 01 23 45 67
B = 89 ab cd ef
C = fe dc ba 98
D = 76 54 32 10
```

#### Process Each Block

Each 512-bit block gets broken down further into 16 sub-blocks of 32 bits each. There are four rounds of operations, with each round utilizing all the sub-blocks, the buffers, and a constant array value.

#### Example:
Lets get MD5 hash from `"7"`.
1. Bit code(ASCII representation) of the message is `00110111 `
2. The message contains  8 bits. We add 440 bits more to get:  
`[message with padded bits] % 512 = 448`.  We will add  `1` and `439` ZEROS  to original Bit code. Now the Bit code looks like:
```
[original message bitcode] + [padding bits part(1+zeros)]
```
3. Append a 64 bit value which represents the original length of the message.
```
0000000000000000000000000000000000000000000000000000000000001000
```
Bit code now looks like:
```
[original message bitcode] + [padding bits part] + [original message lengh]
```

```
00110111 10000000 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00001000 
```

Then it is divided by 4 buffer channels and the magic is happening there with 
# Cifra
Cifra is a collection of cryptographic primitives targeted at embedded use.

## Aims
In order of descending emphasis, cifra aims for:

* **Clarity** and **simplicity**.
* Countermeasures for side channel leaks inherent in some
  algorithms.
* Suitability for embedded use.  Particularly: cifra uses an
  absolute minimum of the standard C library and is reasonably
  efficient with respect to code and data space.

## Features
* **AES** in the **GCM**, **CCM** and **EAX** authenticated encryption modes.
* **SHA224**, **SHA256**, **SHA384** and **SHA512** hash functions (including **HMAC** and **PBKDF2**).
* **SHA3-224**, **SHA3-256**, **SHA3-384**, **SHA3-512** hash functions (FIPS 202 draft compatible).
* **ChaCha20** and **Salsa20** stream ciphers.
* 100% code coverage by line.

Additionally cifra imports curve25519 from elsewhere (nacl, tweetnacl,
Adam Langley's donna) for comparison between various implementations
on embedded targets.

## Testing
There is quite a lot of testing available:

* **Host builds**: run `make test` in the `src` directory.  This builds and
  runs assorted test programs.
* **Emulated embedded builds**: run `make test` in the `src/arm` directory.  This
  expects to find `qemu-system-gnuarmeclipse` on the path.  These tests assume
  a Cortex-M0 target.
* **Cortex-M0 on-target tests**: run `make test.stm32f0` in the `src/arm` directory.
  This expects to find `openocd` on the path, with an STM32F0xx attached via
  stlinkv2.  It uses ARM semihosting to report results.
* **Cortex-M3/4 on-target tests**: run `make test.stm32f1` or `make test.stm32f3` as above.

Additionally all embedded targets expect to find the `arm-none-eabi` toolchain
to be on the path.

## Measurements
All measurements performed at `-Os` (optimise for space), on the following MCUs: 

Core       | Part number   | Price (1s)   | Max clock  | Flash | SRAM
---------- | ------------- | ------------ | ---------- | ----- | -----
Cortex-M0  | STM32F030F4P6 | 0.32EUR      | 48MHz      | 16KB  | 4KB
Cortex-M3  | STM32F103C8T6 | 2.87EUR      | 72MHz      | 64KB  | 20KB
Cortex-M4F | STM32F303K6T6 | 4.53EUR      | 72MHz      | 32KB  | 12KB

## AES
This test does a key schedule, then encrypts one block.

### 128-bit key
Core       | Cycles (key schedule + block) | Cycles (key schedule) | Cycles (block) | Stack | Code size
---------- | ----------------------------- | --------------------- | -------------- | ----- | ---------
Cortex-M0  | 7083                          | 2113                  | 4970           | 312B  | 1220B    
Cortex-M3  | 4681                          | 1595                  | 3086           | 300B  | 1160B    
Cortex-M4F | 4446                          | 1553                  | 2893           | 300B  | 1160B    

### 256-bit key
Core       | Cycles (key schedule + block) | Cycles (key schedule) | Cycles (block) | Stack | Code size
---------- | ----------------------------- | --------------------- | -------------- | ----- | ---------
Cortex-M0  | 10480                         | 3558                  | 6922           | 396B  | 1292B    
Cortex-M3  | 6684                          | 2414                  | 4270           | 380B  | 1252B    
Cortex-M4F | 6382                          | 2381                  | 4001           | 380B  | 1252B    


## AES128-GCM
This test encrypts and authenticates a 16 byte message,
with 16 bytes additionally authenticated data.  It includes
the initial key schedule.

Core       | Cycles | Stack | Code size
---------- | ------ | ----- | ---------
Cortex-M0  | 56997  | 820B  | 3468B    
Cortex-M3  | 43202  | 836B  | 3520B    
Cortex-M4F | 41017  | 836B  | 3520B    

## AES128-EAX
This test encrypts and authenticates a 16 byte message,
with 16 bytes additionally authenticated data.  It includes
the initial key schedule.

Core       | Cycles | Stack | Code size
---------- | ------ | ----- | ---------
Cortex-M0  | 50111  | 936B  | 3332B    
Cortex-M3  | 32789  | 924B  | 3292B    
Cortex-M4F | 31161  | 924B  | 3292B    

## AES128-CCM
This test encrypts and authenticates a 16 byte message,
with 16 bytes additionally authenticated data.  It includes
the initial key schedule.

Core       | Cycles | Stack | Code size
---------- | ------ | ----- | ---------
Cortex-M0  | 40252  | 816B  | 3168B    
Cortex-M3  | 26626  | 796B  | 3096B    
Cortex-M4F | 25353  | 796B  | 3096B    

## Chacha20
This test encrypts a 64 byte message.

Core       | Cycles | Stack | Code size
---------- | ------ | ----- | ---------
Cortex-M0  | 5630   | 568B  | 1612B    
Cortex-M3  | 3448   | 568B  | 1632B    
Cortex-M4F | 3323   | 568B  | 1632B    

(For comparison with AES, add an AES256 key schedule plus 4 blocks.
That's about 33K cycles.)

## Salsa20
This test encrypts a 64 byte message.

Core       | Cycles | Stack | Code size
---------- | ------ | ----- | ---------
Cortex-M0  | 5779   | 568B  | 1620B    
Cortex-M3  | 3231   | 572B  | 1544B    
Cortex-M4F | 3115   | 572B  | 1544B    

## SHA256
This test hashes the empty string (one compression function invocation).

Core       | Cycles | Stack | Code size
---------- | ------ | ----- | ---------
Cortex-M0  | 21866  | 460B  | 1776B    
Cortex-M3  | 15476  | 456B  | 1804B    
Cortex-M4F | 15204  | 456B  | 1804B    

## SHA512
This test hashes the empty string (one compression function invocation).

Core       | Cycles | Stack | Code size
---------- | ------ | ----- | ---------
Cortex-M0  | 55828  | 780B  | 2848B    
Cortex-M3  | 43029  | 836B  | 2976B    
Cortex-M4F | 42019  | 836B  | 2968B    

## SHA3-256
This test hashes the empty string (one sponge permutation).

Core       | Cycles | Stack | Code size
---------- | ------ | ----- | ---------
Cortex-M0  | 148553 | 848B  | 2204B    
Cortex-M3  | 114933 | 968B  | 2328B    
Cortex-M4F | 113622 | 968B  | 2328B    

## SHA3-512
This test hashes the empty string (one sponge permutation).

Core       | Cycles | Stack | Code size
---------- | ------ | ----- | ---------
Cortex-M0  | 135136 | 880B  | 2204B    
Cortex-M3  | 104328 | 1000B | 2328B    
Cortex-M4F | 103154 | 1000B | 2328B    

## Curve25519 (tweetnacl)
This test is one point multiplication.

Core       | Cycles   | Stack | Code size
---------- | -------- | ----- | ---------
Cortex-M0  | 73304448 | 1740B | 1584B    
Cortex-M3  | 30659678 | 1684B | 1552B    
Cortex-M4F | 27143485 | 1684B | 1560B    

See [curve25519-shootout](curve25519-shootout.md) for comparison
between the included curve25519 implementations.

## C library requirements
Cifra requires `memcpy`, `memset`, `abort` and `assert`.

## Future
* ~~Keccak hash function (aka SHA3)~~.
* Poly1305 one-time MAC.
* Constant time curve25519 for Cortex-M4F using the FPU.
* Constant time curve25519 for Cortex-M3 (avoiding the variable-time multiplier).

## License
Public domain.  Please attribute the author.

## Author
Joseph Birr-Pixton <jpixton@gmail.com>

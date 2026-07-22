# AES-Java

AES block cipher built from scratch in Java, following the NIST FIPS-197 specification for encryption and decryption.

## What this demonstrates

AES is the symmetric cipher behind most modern encrypted traffic, but the internals are usually hidden inside a library call. This implements the full algorithm by hand: the byte-level state matrix, the four per-round transformations, and the key expansion that turns a single cipher key into a schedule of round keys. It supports all three key sizes (128, 192, 256 bit), runs both directions, and matches the test vectors in Appendix C of the NIST specification.

## Concepts demonstrated

- The AES state as a 4x4 column-major matrix of bytes, and the mapping from a flat input to that matrix
- SubBytes / InvSubBytes using a precomputed S-box as the substitution step
- ShiftRows / InvShiftRows row rotation for diffusion across columns
- MixColumns / InvMixColumns as multiplication in the Galois field GF(2^8)
- AddRoundKey as a per-round XOR against the derived round key
- Key expansion (KeySchedule) with RotWord, SubWord, and the Rcon round constants
- Round-count selection from key length: 10 rounds for Nk=4, 12 for Nk=6, 14 for Nk=8
- Verbose per-round tracing that prints the intermediate state after each transformation, matching the NIST worked examples

## What's implemented

- `AES.java` orchestrates encrypt and decrypt over the round loop
- `State.java` holds the state matrix and conversions to and from byte arrays
- `Transformations/` holds SubBytes, ShiftRows, MixColumns, AddRoundKey and their inverses
- `KeySchedule/` holds the key expansion plus RotWord, SubWord, and Rcon
- `Helper/Sbox.java` holds the substitution table
- `Main.java` parses hex input, validates lengths, and runs `-e`, `-d`, or `-ed`

## Stack

- Java (standard library only, no crypto dependencies)

## Usage

Compile and run from `src/`:

```
javac Main.java
java Main <16-byte hex block> <16/24/32-byte hex key> <-e | -d | -ed> [-v]
```

- `-e` encrypt, `-d` decrypt, `-ed` encrypt then decrypt back
- `-v` prints the state after every round transformation

The input block is a 128-bit value (16 bytes / 32 hex characters). The key is 128, 192, or 256 bit.

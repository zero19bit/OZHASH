
A 256‑bit custom cryptographic hash function written in Python.
Ozhash is inspired by SHA‑256 but adds a second parallel state row, a larger block size, and AES S‑box nonlinearity.
Educational only – not suitable for real security use.

    In the source code the hash function is named powerful_hash(), but the algorithm is called Ozhash.

Features

    Automatic salting – prefixes sesht5er and suffixes gormitimhaaa to every input.

    Block size – 256 bytes (2048 bits) for higher theoretical throughput.

    Padding – SHA‑256 style 0x80 + zeros + 64‑bit length; padded length is always len % 256 == 248 before adding the length field.

    Two internal state rows – 16 words total (512 bits), updated in parallel.

    Extended message schedule – 96 words per block (vs. 64 in SHA‑256).

    Nonlinear mixing – uses Ch, Maj, custom sigma0/sigma1, and AES S‑box on intermediate values.

    Output – 64 hexadecimal characters (256 bits).

⚠️ Security Notice

Ozhash was not designed or audited for cryptographic security.
It may have collisions, preimage vulnerabilities, or other weaknesses.
Do not use for passwords, digital signatures, file integrity, or any security‑critical purpose.
Requirements

    Python 3.6+

    No external dependencies (uses only struct, sys, typing from the standard library)

Installation

Clone and run directly:
bash

git clone https://github.com/yourusername/ozhash.git
cd ozhash

No installation step is required.
Usage

The script reads input either from a command‑line argument or from standard input.
Command‑line argument
bash

python ozhash.py "hello world"

Standard input
bash

echo -n "hello world" | python ozhash.py

From a file
bash

python ozhash.py < file.txt

Example output
bash

$ python ozhash.py "test"
6a09e667bb67ae853c6ef372a54ff53a510e527f9b05688c1f83d9ab5be0cd19

    Your actual output will differ because of the fixed salts and the custom algorithm.

How It Works (Step by Step)

    Salting
    The input data is transformed into b"sesht5er" + data + b"gormitimhaaa".

    Padding

        Append 0x80.

        Append zeros until (total length) % 256 == 248.

        Append the original bit length as a 64‑bit big‑endian integer.

    Initialisation
    Two rows of 8 state words (h[0:8] and h[8:16]) are set to the first eight SHA‑256 constants (repeated).

    Block processing (256 bytes at a time)

        Break the block into 64 words (32‑bit, big‑endian).

        Expand to 96 words using sigma0 and sigma1.

        Run 96 rounds of compression, updating both state rows simultaneously.

        Each round uses Ch, Maj, rotations, sub_bytes (AES S‑box), and cross‑mixing between the two rows.

        After 96 rounds, XOR the result back into the global state.

    Finalisation
    Output the first eight words of the state as a 64‑character hexadecimal string.

Function Reference (from the code)
Function	Description
rotl(x, n)	32‑bit left rotation.
sub_bytes(word)	Splits a word into 4 bytes, applies AES S‑box to each, reassembles.
Ch(x, y, z)	Choice: (x & y) ^ ((~x) & z).
Maj(x, y, z)	Majority: (x & y) ^ (x & z) ^ (y & z).
sigma0(x)	rotl(x,7) ^ rotl(x,18) ^ (x >> 3).
sigma1(x)	rotl(x,17) ^ rotl(x,19) ^ (x >> 10).
powerful_hash(data)	Main hash function (implements Ozhash), returns a hex string.
Why the Name “Ozhash”?

The name is a short, distinctive identifier for this experimental hash. The original code referred to “powerful hash”, but the algorithm is now branded as Ozhash.
License

MIT License – see LICENSE file.
Contributing

Issues, pull requests, and discussions are welcome.
Because this is a learning project, clarity and readability are prioritised over performance.
Acknowledgments

    NIST SHA‑256 – design inspiration

    Rijndael S‑box – used in sub_bytes

    Python struct module – for binary packing/unpacking

# Hashing-Algorithm---SHA-256 </br>
Note 1: All variables are 32 bit unsigned integers and addition is calculated modulo 2^32 </br>
Note 2: For each round, there is one round constant k[i] and one entry in the message schedule array w[i], 0 ≤ i ≤ 63 </br>
Note 3: The compression function uses 8 working variables, a through h </br>
Note 4: Big-endian convention is used when expressing the constants in this pseudocode, </br>
    and when parsing message block data from bytes to words, for example, </br>
    the first word of the input message "abc" after padding is 0x61626380 </br>

Initialize hash values: </br>
(first 32 bits of the fractional parts of the square roots of the first 8 primes 2..19): </br>
h0 := 0x6a09e667 </br>
h1 := 0xbb67ae85 </br>
h2 := 0x3c6ef372 </br>
h3 := 0xa54ff53a </br>
h4 := 0x510e527f </br>
h5 := 0x9b05688c </br>
h6 := 0x1f83d9ab </br>
h7 := 0x5be0cd19 </br>

Initialize array of round constants: </br>
(first 32 bits of the fractional parts of the cube roots of the first 64 primes 2..311): </br>
k[0..63] := </br>
   0x428a2f98, 0x71374491, 0xb5c0fbcf, 0xe9b5dba5, 0x3956c25b, 0x59f111f1, 0x923f82a4, 0xab1c5ed5, </br>
   0xd807aa98, 0x12835b01, 0x243185be, 0x550c7dc3, 0x72be5d74, 0x80deb1fe, 0x9bdc06a7, 0xc19bf174, </br>
   0xe49b69c1, 0xefbe4786, 0x0fc19dc6, 0x240ca1cc, 0x2de92c6f, 0x4a7484aa, 0x5cb0a9dc, 0x76f988da, </br>
   0x983e5152, 0xa831c66d, 0xb00327c8, 0xbf597fc7, 0xc6e00bf3, 0xd5a79147, 0x06ca6351, 0x14292967, </br>
   0x27b70a85, 0x2e1b2138, 0x4d2c6dfc, 0x53380d13, 0x650a7354, 0x766a0abb, 0x81c2c92e, 0x92722c85, </br>
   0xa2bfe8a1, 0xa81a664b, 0xc24b8b70, 0xc76c51a3, 0xd192e819, 0xd6990624, 0xf40e3585, 0x106aa070, </br>
   0x19a4c116, 0x1e376c08, 0x2748774c, 0x34b0bcb5, 0x391c0cb3, 0x4ed8aa4a, 0x5b9cca4f, 0x682e6ff3, </br>
   0x748f82ee, 0x78a5636f, 0x84c87814, 0x8cc70208, 0x90befffa, 0xa4506ceb, 0xbef9a3f7, 0xc67178f2 </br>

Pre-processing (Padding): </br>
begin with the original message of length L bits </br>
append a single '1' bit </br>
append K '0' bits, where K is the minimum number >= 0 such that (L + 1 + K + 64) is a multiple of 512 </br>
append L as a 64-bit big-endian integer, making the total post-processed length a multiple of 512 bits </br>
such that the bits in the message are: <original message of length L> 1 <K zeros> <L as 64 bit integer> , (the number of bits will be a multiple of 512) </br>

Process the message in successive 512-bit chunks: </br>
break message into 512-bit chunks </br>
for each chunk </br>
    create a 64-entry message schedule array w[0..63] of 32-bit words </br>
    (The initial values in w[0..63] don't matter, so many implementations zero them here) </br>
    copy chunk into first 16 words w[0..15] of the message schedule array </br>

    Extend the first 16 words into the remaining 48 words w[16..63] of the message schedule array: </br>
    for i from 16 to 63 </br>
        s0 := (w[i-15] rightrotate  7) xor (w[i-15] rightrotate 18) xor (w[i-15] rightshift  3) </br>
        s1 := (w[i-2] rightrotate 17) xor (w[i-2] rightrotate 19) xor (w[i-2] rightshift 10) </br>
        w[i] := w[i-16] + s0 + w[i-7] + s1 </br>

    Initialize working variables to current hash value: </br>
    a := h0 </br>
    b := h1 </br>
    c := h2 </br>
    d := h3 </br>
    e := h4 </br>
    f := h5 </br>
    g := h6 </br>
    h := h7 </br>

    Compression function main loop: </br>
    for i from 0 to 63 </br>
        S1 := (e rightrotate 6) xor (e rightrotate 11) xor (e rightrotate 25) </br>
        ch := (e and f) xor ((not e) and g) </br>
        temp1 := h + S1 + ch + k[i] + w[i] </br>
        S0 := (a rightrotate 2) xor (a rightrotate 13) xor (a rightrotate 22) </br>
        maj := (a and b) xor (a and c) xor (b and c) </br>
        temp2 := S0 + maj </br>
 
        h := g </br>
        g := f </br>
        f := e </br>
        e := d + temp1 </br>
        d := c </br>
        c := b </br>
        b := a </br>
        a := temp1 + temp2 </br>

    Add the compressed chunk to the current hash value: </br>
    h0 := h0 + a </br>
    h1 := h1 + b </br>
    h2 := h2 + c </br>
    h3 := h3 + d </br>
    h4 := h4 + e </br> 
    h5 := h5 + f </br>
    h6 := h6 + g </br>
    h7 := h7 + h </br>

Produce the final hash value (big-endian): </br>
digest := hash := h0 append h1 append h2 append h3 append h4 append h5 append h6 append h7 </br>

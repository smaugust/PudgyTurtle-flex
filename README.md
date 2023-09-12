
#PudgyTurtle-flex
================
PudgyTurtle is an "encryption mode" for stream ciphers: after splitting the plaintext
into symbols (typically 4-16 bits in size), PudgyTurtle uses some keystream
to encode each plaintext-symbol, and some keystream to encipher each codeword (via XOR).

The code, which is non-systematic (i.e., the plaintext-symbol itself is not part of 
the codeword), describes the "match" (to within a 1-bit error tolerance) between each 
plaintext-symbol and a same-sized chunk of keystream.

GOALS
=====
  Another source-code is available in the GitHub /smaugust/PugyTurtle-basic repository.
  That code implements a simpler version of PudgyTurtle, which is limited to fixed-size
  input (4-bit symbols) and output (8-bit symbols).  
  
  The version in this repository allows FLEXIBLE I/O sizes, specified by 3 system parameters.
  Each PudgyTurtle-flex "implementation" PT[s,f,d] encrypts s-bit input-symbols into
  c-bit ouput-symbols, where c = f + d is the codeword size.  In more detail,
    s is the input (plaintext) symbol-size, in bits;
    f is the size of each failure-counter, in bits; and
    d is the size of each discrepancy-code, in bits.
  
WARNING
=======
  * This code is for research purposes only -- not a 'secure' production code for end-users. 
  * EG: secret-key, keystream, & keystream-generator state are purposely exposed to facilitate debugging.

FILES
=====
  LICENSE      (a permissive MIT license for all of this software)
  README.md    (this file)
  ptf.c        (ASCII C-language source code)
  ptf          (Compiled binary/executable of ptf.c)
  test.in      (A test input containing 16,000 bytes of a repeated test-pattern)
  test.check.e (test.in encrypted using PT[6,6,6] and a 24-bit NLFSR initialized to 0xABCDEF)

SOURCE CODE #1: ptf.c
====================
Flexible PudgyTurtle PT[s,f,d] implementation, with user-interface via these command-line options:
  -f = input file
  -c = cipher (keystream generator) to use: rc4, nlfsr, or lfsr
  -k = secret-key
  -n = inner-state size (bits) of keystream generator
  -p = PudgyTurtle implementation parameters. EG for s=5, 
         f-6, and d=4, user would enter the string "-ps5f6d4"
  -e = encrypt using PudgyTurtle mode
  -d = decrypt using PudgyTurtle mode

  WARNINGS:
  --------- 
  * Simplified "bare-bones" code for experimentation and research;
  * No input-validation or sanitization;
  * To aid debugging, insecure features are present (e.g., easily accessible keystream);
  * I/O & encryption are not optimized for speed or memory-efficiency.
  * Does not run in constant time;
  * No security guarantees are made (see LICENSE);

  TEST PATTERNS for ptf.c
  ----------------------
  Running ptf.c on test.in should produce check.test.e; decryption of check.test.e
  should then reproduce test.in.  In a Linux environment, try something like this:

    ./ptf -ftest.in -cnlfsr -kabcdef -ftest1.in -ps6f6d6 -e >test.e    
          ...ENcrypts test.in and writes result to test.e
    
    ./ptf -ftest.e -cnlfsr -kabcdef -ftest1.in -ps6f6d6 -e >test.d   
          ...DEcrypts test.e and writes results to test.d

    wc -c test.e
          ... How long is the encrypted text? Should be XX bytes (which 
          translates to YY 6-bit symbols, including some overflow events).
          
    diff -s test.e test.check.e           
          ... Was encryption correct (i.e., does test.e appear as expecte)?
          
    diff -s test.ptf.in test.d           
          ... Was decryption correct?
      
# PudgyTurtle-flex
"As is" source-codes for studying and implementing PudgyTurtle, which improves upon
/smaugust/PudgyTurtle-basic by allowing variably-sized input- and output-symbols.

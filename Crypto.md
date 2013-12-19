## Notes SecRepo Cryptography
The underlying encryption used in SecRepo is AES with 256 bit key-size and Cypher Block Chaining  (CBC) mode.
## Deterministic Encryption (no salt)
In order to take advantage of git object optimizations, it is important that the same unencrypted input always results in the same cypher-text. To this end, I am not adding any *salt* to the pass-phrase or AES key. This makes it more important that that strong pass-phrase is selected. See notes on password strength and choosing passwords, below.
   * for the compression, I am also using gzip / zlib in such a way that the the compressed output is deterministic.

refs:
   * https://en.wikipedia.org/wiki/Deterministic_encryption
   * https://en.wikipedia.org/wiki/Encryption_mode
   * https://en.wikipedia.org/wiki/Initialization_vector

## Keys and Passwords
The ASCII 'key' used in secrepo is not really the AES key. It is a pass-phrase which is hashed to generate the AES keys and Initialization Vector. Perhaps I will come up with better naming for this.
### Choosing Strong Encryption Passwords
   * short answer, *don't*

#### Longer answer
Because secrepo does not salt passwords and an attacker will get access to lots of material encrypted with the same key, password 'cracking' is an issue. If you are picking a password that is long-but-memorable, it does probably not have enough entropy to be very secure. My recommendation is  to use `secrepo new` to create long passwords with 256 bits of randomness, then, put it in your regular password vault for safe-keeping.

### Password Hash
I am using the openssl compatible password hash rather than a more modern iterative hash because earlier versions of secrepo used an openssl sub-process to perform the encryption and decryption. Now that I am using the PyCrypto library for encryption, I don't really need to keep the openssl compatibility, so I may improve the password hash in a future version.
If you are using the recommended 256bit base64 passwords then more modern password hashing will not make much difference.

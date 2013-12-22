## SecRepo Cryptography Notes
The underlying encryption used in SecRepo is AES with 256 bit key-size and Cypher Block Chaining  (CBC) mode.
## Deterministic Encryption (no salt)
In order to take advantage of git object optimizations, it is important that the same unencrypted input always results in the same cypher-text. To this end, I am not adding any *salt* to the pass-phrase or AES key. This makes it more important that that strong pass-phrase is selected. See notes on password strength and choosing passwords, below.
   * for the compression, I am also using gzip / zlib in such a way that the the compressed output is deterministic.

refs:
   * https://en.wikipedia.org/wiki/Deterministic_encryption
   * https://en.wikipedia.org/wiki/Encryption_mode

## Keys and Passwords
The ASCII 'key' used in SecRepo is not really the AES key. It is a pass-phrase which is hashed to generate the AES keys and Initialization Vector. Perhaps I will come up with better naming for this.
### Choosing Strong Encryption Passwords
   * short answer, *don't*

#### Longer answer
Because secrepo does not salt passwords and an attacker will get access to lots of material encrypted with the same key, password 'cracking' is an issue. If you are picking a password that is long-but-memorable, it does probably not have enough entropy to be very secure. My recommendation is  to use `git secrepo new` to create long passwords with 256 bits of randomness, then, put it in your regular password vault for safe-keeping (you won't be able to memorize it).

### Password Finger Print
Secrepo allows many different keys to be installed, also, we want to make sure that the correct key is selected when decrypting files. To allow this, the encrypted file header contains a key fingerprint.
This finger-print is 66 bits long, which is large enough to make collisions unlikely, but quite a bit smaller than the actual key-size (256 bits). Having it only 66 bits long (11 base64 chars), saves space in the header, and also makes it more difficult for an attacker to try and use the finger-print to compute the key (because there are many unusable keys that map to the same finger print).

The current finger-print algorithm uses SHA256 with a bit of extra data.
Originally, I used the openssl key derivation and AES, however, that was less efficient.

### Password Hash (Key Derivation)
I am using the openssl compatible password hash (without salt) rather than a more modern iterative hash because earlier versions of secrepo used an openssl sub-process to perform the encryption and decryption. Now that I am using the PyCrypto library for encryption, I don't really need to keep the openssl compatibility, so I may improve the password hash in a future version.

If you are using the recommended 256bit base64 passwords then more modern password hashing will not make much difference. The modern key derivation functions (PBKDF2, SCrypt) try and make shorter memorable passwords more secure by making the key-derivation slower.

SecRepo currently allows short memorable passwords to be used vi the 'add' command, however, those passwords are normalized with PBKDF2 from PyCrypto ( `Crypto.Protocol.KDF.PBKDF2` ).

References:
   * http://en.wikipedia.org/wiki/Key_derivation_function
   * http://en.wikipedia.org/wiki/PBKDF2
   * https://crackstation.net/hashing-security.htm
   * http://en.wikipedia.org/wiki/Scrypt
   * https://en.wikipedia.org/wiki/Initialization_vector
   * http://pythonhosted.org/pycrypto/

#### OpenSSL Password Hash (Key Derivation)
The OpenSSL approach derives the key and initialization vector from the ASCII password using an md5 hash repeatedly until sufficient bytes are available. I am still using this mechanism because it seems to work *ok* for short passwords and does not appear to *loose* any entropy for longer passwords, however, if I eventually force all passwords to be 256 bit, base64, then this code may not be the best approach. Even if I have a 256 bit base64 password that I could use as the AES key, I will still need to generate the initialization vector. This would probably be done using the same AES key material (because I need deterministic encryption). 

Refs:
   * Original openssl code:
      * EVP_BytesToKey in evp_key.c
      * https://github.com/openssl/openssl/blob/master/crypto/evp/evp_key.c
   * http://stackoverflow.com/questions/16761458/how-to-aes-encrypt-decrypt-files-using-python-pycrypto-in-an-openssl-compatible
   * In Python:

```python
# Without Salt
def derive_key_and_iv(password, key_length, iv_length):
    d = d_i = ''
    while len(d) < key_length + iv_length:
        d_i = hashlib.md5(d_i + password).digest()
        d += d_i
    return d[:key_length], d[key_length:key_length+iv_length]

# With Salt
def derive_key_and_iv(password, salt, key_length, iv_length):
    d = d_i = ''
    while len(d) < key_length + iv_length:
        d_i = md5(d_i + password + salt).digest()
        d += d_i
    return d[:key_length], d[key_length:key_length+iv_length]
```

Using the same key and iv derivation as openssl has the advantage of compatibilty.
For example, you can decrypt a SecRepo encrypted file with openssl by stripping the
64 byte header and using the openssl program and gzip:
```sh
dd bs=16 skip=4 if=encrypted_file | openssl aes-256-cbc -nosalt -d | gunzip > clear_file
```
The password you would provide to openssl in the above example is the password you see in
`{gitdir}/secrepo` or `~/secrepo` or `git-secrepo [-g] export` (without quotations).

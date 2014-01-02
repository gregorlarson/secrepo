## Compare SecRepo with other Git Encryption Projects
### git-remote-gcrypt (by Joey Hess)
* https://github.com/joeyh/git-remote-gcrypt/
* Joey also creates git-annex

This is a different approach that does not use attributes or filters.
* It uses git-remote *helpers* on to handle the encrypted files and communication.
   * The resulting remote git repo is in a special format that is only usable by participants with private keys, authorized with public keys.
   * All files and information on the branch are encrypted.
* Uses GPG public key encryption so multiple users can share the remote repo without sharing private keys.

### git-encrypt (by Woody Gilk)
* https://github.com/shadowhand/git-encrypt

Filter approach like SecRepo. Coded in Bash script. Very small, simple.
Uses external openssl program.
* Default ECB mode is not super secure.
* No compression.
* No file-header (other than openssl).

### git-crypt (by Andrew Ayer)
* https://www.agwa.name/projects/git-crypt/
* https://github.com/AGWA/git-crypt

Filter approach like SecRepo. Coded in C using OpenSSL libraries.
* similar crypto libraries to SecRepo
* C code probably runs faster than Python at some portability cost
   * Needs to be built (compiled for each platform)
   * not sure what platforms are tested.
* Less key management than SecRepo
* Uses AES-256 in CTR mode with a synthetic IV
   * SecRepo use AES-256 in CBC mode with OpenSSL style Key and IV derivation (where Key and IV are generated together from the same key material).
   * git-crypt uses a synthetic IV based on a hash of the entire clear-text. This still provides deterministic encryption but leaks less information.
      * Using the entire clear-text to compute IV is expensive for large files. IMO the first 32K would have been sufficient but I guess if you really want to hide the fact that two different files, encrypted with the same key, have identical prefix then you have to do this.
   * git-crypt includes only a NONCE in the header, so support for multiple decryption keys is not possible, and detection of an invalid key is difficult.
      * no explicit version in header
   * SecRepo includes a key fingerprint in the header which is a fairly large disclosure but has as lot of usability benefits.
* git-crypt does not do compression prior to encryption.

### gitcrypt (by Vadim Schetinin)
* http://gitcrypt.sourceforge.net/
* last update May 2010

Filter design (like SecRepo).
* Coded in c++ for Win32 and Linux/Unix
* Uses zlib for compression (like SecRepo)
* Includes a file header, but no support for multiple keys.
* Uses openssl AES-256 CBC mode (like SecRepo)
   * No IV computed (default / NULL).
   * No key derivation function used 
* In-memory encryption / decryption only.
* Encrypted payload is stored in base64 format. Not sure what the advantage of this is.


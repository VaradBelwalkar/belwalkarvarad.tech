---
title: "gpg: symmetric-cryptography, key distribution and more..."
date: 2023-01-14T09:15:19+05:30
draft: false
ShowToc: false
ShowRelatedContent: true
summary: "important topics covered"
tags: ["Cryptography", "Linux"]
categories: ["Linux","Cryptography"]
---


## Symmetric keys

To encrypt the file with passphrase:
```shell
gpg --symmetric file_to_encrypt
```
This will prompt you for the passphrase to enter to encrypt the file,  
To strongly encrypt, create first a symmetric key and use this as passphrase  

To decrypt simply run, and give passphrase when prompted:  
```shell
gpg --decrypt file_to_decrypt
```   
Here gpg will know whether to use symmetric or asymmtric decryption here

Here gpg after taking the passphrase, uses a key-derivation-function(kdf) to derive a key from the passphrase and use that key to   encrypt the file

---

## Key Distribution
The process of making your key publicly available on a keyserver is coupled with an important security measure - email authorization. Upon upload, the keyserver sends an email to the associated address with a link for authorization. Clicking on this link is mandatory for the key to be accessible to others for search and download, primarily based on email search.

This approach not only ensures the legitimacy of key ownership but also safeguards against attempts to manipulate key distribution for malicious purposes. The email authorization step adds an extra layer of protection, making the key distribution process more resilient against potential threats.



To send the publickey to the key-server:
```shell
gpg --keyserver <keyserverurl> --send-keys <fingerprint>
```

For example:
```shell
gpg --keyserver hkps://keys.openpgp.org --send-keys <fingerprint>
```
          
Now to search for a key on keyserver: (NOTE: Reflection of uploaded publickey takes some time)
```shell
gpg --keyserver hkps://keys.openpgp.org --search-keys <username or email>
```

Once you find the key on the keyserver, along with the metadata, it will also show the fingerprint for that key,
So to download that public key:
```shell
gpg --keyserver hkps://keys.openpgp.org --recv-keys <fingerprint>
```

Also other than openpgp,

we have pgp.mit.edu keyserver available
so you can change above url to, [hkp://pgp.mit.edu]

---

## Edit key expiration

To edit the key expiration date, 
run:
```shell
gpg --edit-key <key fingerprint or name or email>
```      
You will get gpg prompt
```shell
gpg> 
```
run expire
```shell
gpg> expire
```
and then choose option
make sure to run save

```shell
gpg> save
```

DONE


---

## Multiple Recipents

When you run the following command:
```shell    
gpg --encrypt --recipent <recipent name or email as per pubkey> file_to_encrypt
```

here there is only single recipient, so the gpg will directly encrypt the file with the public key of the recipient and create a .gpg file


but supppose you give multiple recipients like this:
```shell
gpg --encrypt --recipient <r1> --recipient <r2> --recipient <r3> file_to_encrypt
```

Here the gpg first, creates a symmetric key and encrypts the file, and then encrypts the symmetric key each with public key of the recipient so if there are 3 recipients
3 encrypted symmetric keys will get created and they are going to get appended to the encrypted file along with their metadata


---


## Signing and Verification

To sign a file
```shell
gpg --sign file
```

To verify a file
```shell
gpg --verify file
```

---
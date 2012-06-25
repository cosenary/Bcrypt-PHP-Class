# Bcrypt PHP Class #

## About ##

This is an easy-to-use static PHP class for Bcyrpt.  
Your feedback is always welcome.

> If you are new to the concept of Bcyrpt, just [take a look](#bcrypt) at it.  
> The documentation is also available as a [PDF file](http://cl.ly/3E1K0X2q292G0c110w2h).

## Requirements ##

- PHP 5.3 or higher
- OpenSSL

## Usage ##

The usage of this class is really simple. It contains two public methods:

```php
<?php
    require_once 'Bcrypt.php';
    
    // hash the password => returns hashed password
    Bcrypt::hashPassword($password);
    
    // check $password against the $hashedPassword => returns true/false
    Bcrypt::checkPassword($password, $hashedPassword);
?>
```

## Options ##

### Work factor ###

`Bcrypt::hashPassword($password, <$workFactor>)`

To increase your hash security this method accepts the optional parameter `$workFactor`. This defines the number of rounds.
With each round the creation time doubles, so the system is exponentially. If *one* round takes about `1 ms`, then *31* rounds would take ca. `74 minutes`.

    08 rounds = 2^8 iterations
    12 rounds = 2^12 iterations

---

**Important:** `$workFactor` must be a *2 char* integer in the range between `04` and `31`. The default value is `12`.

---

```php
<?php
    $workFactor = 16;
    Bcrypt::hashPassword($password, $workFactor);
?>
```

Because the current work factor is [stored in the hash](#structure), it's not a fixed value. This allows you to adjust it, depending on your hardware capacity and upcoming traffic. As your hardware speed improves, the factor can be increased to compensate.

> Suitable values are in the range between `08` to `12`, depending on the hardware used and patience. [Good practice](http://stackoverflow.com/a/4766811).

### Scheme ###

To alter the default scheme, change the variable `$_identifier` of the class to one of the following parameters (without $-signs):

- `$2a$` - Hash which is potentially generated with the buggy algorithm.
- `$2x$` - "compatibility" option the buggy Bcrypt implementation.
- `$2y$` - Hash generated with the new, corrected algorithm implementation *(crypt_blowfish 1.1 and newer)*.

---

**Note:** The default scheme is `$2y$`, which makes use of the new, corrected hash implementation.  
*Other schemes should only be used, when comparing values produced by an old version.*

---

## Bcrypt ##

So what exactly is a Bcrypt? It is an adaptive hash function based on the Blowfish symmetric block cipher cryptographic algorithm. It uses a Work Factor which adjusts the cost of hashing. This ability, to increase the time and processing power of hashing, makes Bcrypt so powerful compared to other hashing methods.

- Its slowness and multiple rounds ensures high security (an attacker must deploy massive hardware to be able to crack the passwords).
- Bcrypt is an one-way hashing algorithm. This means that you cannot "decode" the plain password.
- Bcrypt PHP class hashes each password with a different salt.

### Why should I use Bcrypt? ###

Some people believe MD5 would be a safe way to encode passwords - this is a lie! MD5 is a good method to obscure non-sensitive data, because it's quite fast.
However, this is a big disadvantage when it comes to password hashing. Using [rainbow tables](http://en.wikipedia.org/wiki/Rainbow_table) MD5 hashes can be very easily “decoded”.

That's the point where Bcrypt comes into play. Using a work factor of *12*, Bcrypt hashes the password in about *0.3 seconds*. MD5, on the other hand, takes less than *a microsecond*. If you still believe in MD5 as a secure password hasher, take a look at the following reference.

> You can test how easy it is to [crack MD5 encoded passwords](http://md5cracker.org/index-page-cracken.html).    
> Read more about [why you should use Bcrypt](http://phpmaster.com/why-you-should-use-bcrypt-to-hash-stored-passwords/).

### Structure ###

    $2a$12$Some22CharacterSaltXXO6NC3ydPIrirIzk1NdnTz0L/aCaHnlBa

- `$2a$` tells PHP to use which [Blowfish scheme](#scheme) *(Bcrypt is based on Blowfish)*
- `12$`  is the number of [iterations](#work-factor) the hashing mechanism uses.
- `Some22CharacterSaltXXO` is a random salt *(by OpenSSL)*

#### Diagram ####

    $2a$12$Some22CharacterSaltXXO6NC3ydPIrirIzk1NdnTz0L/aCaHnlBa
    \___________________________/\_____________________________/
      \                            \
       \                            \ Actual Hash (31 chars)
        \
         \  $2a$   12$   Some22CharacterSaltXXO
            \__/    \    \____________________/
              \      \              \
               \      \              \ Salt (22 chars)
                \      \
                 \      \ Number of Rounds (work factor)
                  \
                   \ Hash Header

> Diagram based on [Andrew Moore's structure](http://stackoverflow.com/a/5343655).

---

Please note, that there is ABSOLUTELY NO WARRANTY for this class.  
The project is based on the great [phpass framework](http://www.openwall.com/phpass/).

---

## History ##

**Bcrypt PHP Class 1.0 - 23/06/2012**

- `release` First version
- `update` Extensive documentation

## Credits ##

Copyright (c) 2012 - Programmed by Christian Metz  
Released under the [BSD License](http://www.opensource.org/licenses/bsd-license.php).
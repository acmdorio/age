# age

age is a simple, modern and secure file encryption tool.

It features small explicit keys, no config options, and UNIX-style composability.

```
$ age-keygen -o key.txt
Public key: age1ql3z7hjy54pw3hyww5ayyfg7zqgvc7w3j2elw8zmrj2kg5sfn9aqmcac8p
$ tar cvz ~/data | age -r age1ql3z7hjy54pw3hyww5ayyfg7zqgvc7w3j2elw8zmrj2kg5sfn9aqmcac8p > data.tar.gz.age
$ age -d -i key.txt data.tar.gz.age > data.tar.gz
```

The format specification is at [age-encryption.org/v1](https://age-encryption.org/v1). To discuss the spec or other age related topics, please email [the mailing list](https://groups.google.com/d/forum/age-dev) at age-dev@googlegroups.com. age was designed by [@Benjojo12](https://twitter.com/Benjojo12) and [@FiloSottile](https://twitter.com/FiloSottile).

An alternative interoperable Rust implementation is available at [github.com/str4d/rage](https://github.com/str4d/rage).

## Usage

```
Usage:
    age -r RECIPIENT [-a] [-o OUTPUT] [INPUT]
    age --decrypt [-i KEY] [-o OUTPUT] [INPUT]

Options:
    -o, --output OUTPUT         Write the result to the file at path OUTPUT.
    -a, --armor                 Encrypt to a PEM encoded format.
    -p, --passphrase            Encrypt with a passphrase.
    -r, --recipient RECIPIENT   Encrypt to the specified RECIPIENT. Can be repeated.
    -d, --decrypt               Decrypt the input to the output.
    -i, --identity KEY          Use the private key file at path KEY. Can be repeated.

INPUT defaults to standard input, and OUTPUT defaults to standard output.

RECIPIENT can be an age public key, as generated by age-keygen, ("age1...")
or an SSH public key ("ssh-ed25519 AAAA...", "ssh-rsa AAAA...").

KEY is a path to a file with age secret keys, one per line
(ignoring "#" prefixed comments and empty lines), or to an SSH key file.
Multiple keys can be provided, and any unused ones will be ignored.
```

### Multiple recipients

Files can be encrypted to multiple recipients by repeating `-r/--recipient`. Every recipient will be able to decrypt the file.

```
$ age -o example.jpg.age -r age1ql3z7hjy54pw3hyww5ayyfg7zqgvc7w3j2elw8zmrj2kg5sfn9aqmcac8p \
    -r age1lggyhqrw2nlhcxprm67z43rta597azn8gknawjehu9d9dl0jq3yqqvfafg example.jpg
```

### Passphrases

Files can be encrypted with a passphrase by using `-p/--passphrase`. By default age will automatically generate a secure passphrase. Passphrase protected files are automatically detected at decrypt time.

```
$ age -p secrets.txt > secrets.txt.age
Enter passphrase (leave empty to autogenerate a secure one):
Using the autogenerated passphrase "release-response-step-brand-wrap-ankle-pair-unusual-sword-train".
$ age -d secrets.txt.age > secrets.txt
Enter passphrase:
```

### SSH keys

As a convenience feature, age also supports encrypting to `ssh-rsa` and `ssh-ed25519` SSH public keys, and decrypting with the respective private key file. (`ssh-agent` is not supported.)

```
$ cat ~/.ssh/id_ed25519.pub
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIIZDRcvS8PnhXr30WKSKmf7WKKi92ACUa5nW589WukJz filippo@Bistromath.local
$ age -r "ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIIZDRcvS8PnhXr30WKSKmf7WKKi92ACUa5nW589WukJz" example.jpg > example.jpg.age
$ age -d -i ~/.ssh/id_ed25519 example.jpg.age > example.jpg
```

Note that SSH key support employs more complex cryptography, and embeds a public key tag in the encrypted file, making it possible to track files that are encrypted to a specific public key.

## Installation

On macOS or Linux, you can use Homebrew:

```
brew tap filippo.io/age https://filippo.io/age
brew install age
```

On Windows, Linux, and macOS, you can use [the pre-built binaries](https://github.com/FiloSottile/age/releases).

If your system has [Go 1.13+](https://golang.org/dl/), you can build from source:

```
git clone https://filippo.io/age && cd age
go build -o . filippo.io/age/cmd/...
```

On OpenBSD -current and 6.7+, you can use the port:

```
pkg_add age
```

On all supported versions of FreeBSD, you can build the security/age port or use pkg:

```
pkg install age
```

Help from new packagers is very welcome.

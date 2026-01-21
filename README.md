# Crypto Utils JS

Pure JavaScript cryptocurrency utilities library. Low-level building blocks for Bitcoin, Ethereum, Litecoin, and other cryptocurrencies. Extracted from [bitrequest](https://github.com/bitrequest/bitrequest.github.io) for reuse in other projects.

*No Node.js, no WebAssembly, just vanilla JavaScript.*

## Features

- **secp256k1 Elliptic Curve** - Private/public key operations
- **Address Generation** - Legacy (P2PKH), SegWit (Bech32), Ethereum, Bitcoin Cash (CashAddr)
- **Base58/Base58Check** - Encoding and decoding with checksum validation
- **Bech32** - Native SegWit address encoding/decoding
- **Hashing** - SHA256, RIPEMD160, Hash160, Keccak256, HMAC
- **WIF** - Wallet Import Format for private keys
- **AES Encryption** - Encrypt/decrypt with password
- **Hex/Byte Utilities** - Comprehensive conversion functions

## Live Demo

**[🔐 Crypto Utils Test Suite](https://bitrequest.github.io/unit_tests_crypto_utils.html)** - Interactive tests and tools

---

## Download

### Quick Download (Terminal)

```bash
mkdir crypto_utils && cd crypto_utils && \
curl -O https://raw.githubusercontent.com/bitrequest/bitrequest.github.io/master/unit_tests_crypto_utils.html && \
curl -O https://raw.githubusercontent.com/bitrequest/bitrequest.github.io/master/assets_js_lib_crypto_utils.js && \
curl -O https://raw.githubusercontent.com/bitrequest/bitrequest.github.io/master/assets_js_lib_sjcl.js
```

Then open `unit_tests_crypto_utils.html` in your browser.

### Manual Download

Right-click and "Save As" for each file:

| File | Description |
|------|-------------|
| [assets_js_lib_sjcl.js](https://raw.githubusercontent.com/bitrequest/bitrequest.github.io/master/assets_js_lib_sjcl.js) | Stanford JavaScript Crypto Library |
| [assets_js_lib_crypto_utils.js](https://raw.githubusercontent.com/bitrequest/bitrequest.github.io/master/assets_js_lib_crypto_utils.js) | Crypto utilities |
| [unit_tests_crypto_utils.html](https://raw.githubusercontent.com/bitrequest/bitrequest.github.io/master/unit_tests_crypto_utils.html) | Interactive test suite |

---

## Usage

### HTML Setup

```html
<script src="assets_js_lib_sjcl.js"></script>
<script src="assets_js_lib_crypto_utils.js"></script>
```

### Generate Key Pair

```javascript
// Private key (32 bytes hex)
const privateKey = "dae1479836371c6670870f2053153648605c316719572d4c0b53364f0b2f5341";

// Get compressed public key (33 bytes)
const publicKey = CryptoUtils.get_publickey(privateKey);
// "02a1633cafcc01ebfb6d78e39f687a1f0995c62fc95f51ead10a02ee0be551b5dc"
```

### Generate Bitcoin Address (Legacy)

```javascript
const publicKey = "02a1633cafcc01ebfb6d78e39f687a1f0995c62fc95f51ead10a02ee0be551b5dc";

// Legacy P2PKH address (version byte "00" for mainnet)
const address = CryptoUtils.pub_to_address("00", publicKey);
// "1HQ3rb7nyLPrjnuW85MUknPekwkn7poAUm"
```

### Generate SegWit Address (Bech32)

```javascript
const publicKey = "02a1633cafcc01ebfb6d78e39f687a1f0995c62fc95f51ead10a02ee0be551b5dc";

// Native SegWit (bc1q...)
const segwitAddress = CryptoUtils.pub_to_address_bech32("bc", publicKey);
// "bc1qg0azlj4w2lrq8jssrrz6eprt2fe7f7edm4vpd5"

// Litecoin SegWit (ltc1q...)
const ltcAddress = CryptoUtils.pub_to_address_bech32("ltc", publicKey);
```

### Generate Ethereum Address

```javascript
// Uncompressed public key (65 bytes, starts with 04)
const uncompressedPub = CryptoUtils.expand_pub(compressedPubKey);

const ethAddress = CryptoUtils.pub_to_eth_address(uncompressedPub);
// "0x7e5f4552091a69125d5dfcb7b8c2659029395bdf"

// With checksum
const checksumAddress = CryptoUtils.to_checksum_address(ethAddress);
// "0x7E5F4552091A69125d5DfCb7b8C2659029395Bdf"
```

### Generate Bitcoin Cash Address

```javascript
const legacyAddress = CryptoUtils.pub_to_address("00", publicKey);
const cashAddr = CryptoUtils.pub_to_cashaddr(legacyAddress);
// "bitcoincash:qp..."
```

### Private Key to WIF

```javascript
const privateKey = "dae1479836371c6670870f2053153648605c316719572d4c0b53364f0b2f5341";

// Bitcoin mainnet WIF (compressed)
const wif = CryptoUtils.privkey_wif("80", privateKey, true);
// "L4p2b9VAf8k5aUahF1JCJUzZkgNEAqLfq8DDdQiyAprQAKSbu8hf"
```

### Base58Check Encode/Decode

```javascript
// Encode
const encoded = CryptoUtils.b58check_encode("00" + hash160);
// "1A1zP1eP5QGefi2DMPTfTL5SLmv7DivfNa"

// Decode (validates checksum)
const decoded = CryptoUtils.b58check_decode(encoded);
// Returns hex payload without checksum
```

### Bech32 Encode/Decode

```javascript
// Decode SegWit address
const decoded = CryptoUtils.bech32_decode("bc1qg0azlj4w2lrq8jssrrz6eprt2fe7f7edm4vpd5");
// { hrp: "bc", data: [...] }

// Encode
const words = CryptoUtils.to_words(dataBytes);
words.unshift(0); // witness version
const encoded = CryptoUtils.bech32_encode("bc", words);
```

### Hashing

```javascript
// SHA256
const sha256 = CryptoUtils.hmacsha(data, "sha256");

// Hash160 (SHA256 + RIPEMD160)
const hash160 = CryptoUtils.hash160(publicKey);

// Keccak256 (Ethereum)
const keccak = CryptoUtils.keccak256(data);

// HMAC-SHA512
const hmac = CryptoUtils.hmac_bits(data, key, "hex");
```

### AES Encryption

```javascript
// Encrypt
const encrypted = CryptoUtils.aes_enc("secret data", "password");

// Decrypt
const decrypted = CryptoUtils.aes_dec(encrypted, "password");
```

---

## API Reference

### Key Operations

| Function | Description |
|----------|-------------|
| `get_publickey(privateKey)` | Derive compressed public key from private key |
| `priv_to_pub(privateKey)` | Alias for get_publickey |
| `expand_pub(compressedPub)` | Convert compressed to uncompressed public key |
| `privkey_wif(version, key, compressed)` | Convert private key to WIF format |
| `normalize_privatekey(key)` | Normalize private key format |

### Address Generation

| Function | Description |
|----------|-------------|
| `pub_to_address(version, pubkey)` | Legacy P2PKH address |
| `pub_to_address_bech32(hrp, pubkey)` | Native SegWit address |
| `hash160_to_address(version, hash)` | Address from Hash160 |
| `pub_to_eth_address(pubkey)` | Ethereum address |
| `to_checksum_address(address)` | EIP-55 checksum address |
| `pub_to_cashaddr(legacyAddress)` | Bitcoin Cash CashAddr |

### Base58

| Function | Description |
|----------|-------------|
| `b58enc(hex)` | Base58 encode |
| `b58dec(string)` | Base58 decode |
| `b58check_encode(hex)` | Base58Check encode (adds checksum) |
| `b58check_decode(string)` | Base58Check decode (validates checksum) |

### Bech32

| Function | Description |
|----------|-------------|
| `bech32_encode(hrp, data)` | Bech32 encode |
| `bech32_decode(string)` | Bech32 decode |
| `to_words(bytes)` | Convert bytes to 5-bit words |
| `from_words(words)` | Convert 5-bit words to bytes |

### Hashing

| Function | Description |
|----------|-------------|
| `hmacsha(data, algo)` | SHA256/SHA512 hash |
| `hmac_bits(data, key, format)` | HMAC with bit array |
| `hash160(data)` | SHA256 + RIPEMD160 |
| `keccak256(data)` | Keccak-256 (Ethereum) |
| `sha_sub(data, algo)` | Generic SHA hash |

### Hex/Byte Conversion

| Function | Description |
|----------|-------------|
| `hex_to_bytes(hex)` | Hex string to Uint8Array |
| `bytes_to_hex(bytes)` | Uint8Array to hex string |
| `dec_to_hex(decimal)` | Decimal to hex |
| `hex_to_dec(hex)` | Hex to BigInt |
| `str_pad(string, length)` | Pad string with leading zeros |

### Elliptic Curve (secp256k1)

| Function | Description |
|----------|-------------|
| `mod(a, b)` | Modulo operation |
| `pow_mod(base, exp, mod)` | Modular exponentiation |
| `invert(number, modulo)` | Modular inverse |
| `sqrt_mod(number)` | Modular square root |

### Encryption

| Function | Description |
|----------|-------------|
| `aes_enc(data, password)` | AES encrypt |
| `aes_dec(data, password)` | AES decrypt |

### String Utilities

| Function | Description |
|----------|-------------|
| `clean_string(str)` | Normalize whitespace |
| `normalize_string(str)` | NFKD normalization |
| `split_words(str)` | Split into word array |
| `join_words(arr)` | Join word array |

---

## Supported Currencies

| Currency | Address Types |
|----------|---------------|
| Bitcoin | Legacy (1...), SegWit (bc1q...) |
| Litecoin | Legacy (L...), SegWit (ltc1q...) |
| Ethereum | 0x... (with EIP-55 checksum) |
| Bitcoin Cash | CashAddr (bitcoincash:q...) |
| Dogecoin | Legacy (D...) |
| Dash | Legacy (X...) |

---

## Security Warning

⚠️ **Never enter real private keys on websites or share them with anyone.**

---

## Related Projects

- [bitrequest](https://github.com/bitrequest/bitrequest.github.io) - Cryptocurrency point-of-sale PWA
- [bip39-utils-js](https://github.com/bitrequest/bip39-utils-js) - BIP39/BIP32 HD wallet utilities
- [xmr-utils-js](https://github.com/bitrequest/xmr-utils-js) - Monero cryptographic utilities

---

## License

MIT License

## Credits

Developed for [bitrequest.io](https://bitrequest.io) - Lightweight cryptocurrency point-of-sale.
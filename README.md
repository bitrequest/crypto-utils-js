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
- **Built-in Compatibility Testing** - Verify browser/environment support

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

---

## Quick Start Examples

### Generate Key Pair

```javascript
// Private key (32 bytes hex)
const privateKey = "922c2cc579600419c8cde59ca8fcb03518ddbb5c38ed1bfbd150cb100e1f5430";

// Derive compressed public key (33 bytes)
const publicKey = CryptoUtils.get_publickey(privateKey);
// "036740c4f55d64fb6c9bc412084638b80062cee07f6c84b205671584e82a7c96b7"
```

### Generate Bitcoin Legacy Address

```javascript
const publicKey = "036740c4f55d64fb6c9bc412084638b80062cee07f6c84b205671584e82a7c96b7";

// Legacy P2PKH address (version byte "00" for mainnet)
const address = CryptoUtils.pub_to_address("00", publicKey);
// "1HQ3rb7nyLPrjnuW85MUknPekwkn7poAUm"
```

### Generate Bitcoin SegWit Address (Bech32)

```javascript
const publicKey = "036740c4f55d64fb6c9bc412084638b80062cee07f6c84b205671584e82a7c96b7";

// Native SegWit address (bc1q...)
const segwitAddress = CryptoUtils.pub_to_address_bech32("bc", publicKey);
// "bc1qk0wlvl4xh3eqe5szqyrlcj4ws8633vz0vhhywl"
```

### Generate Litecoin Addresses

```javascript
const publicKey = "036740c4f55d64fb6c9bc412084638b80062cee07f6c84b205671584e82a7c96b7";

// Litecoin Legacy (L...)
const ltcLegacy = CryptoUtils.pub_to_address("30", publicKey);
// "Lbd17oRd3zduzbbfJDLn2oTQyA84G8Mtmw"

// Litecoin SegWit (ltc1q...)
const ltcSegwit = CryptoUtils.pub_to_address_bech32("ltc", publicKey);
// "ltc1qk0wlvl4xh3eqe5szqyrlcj4ws8633vz0gtdqk0"
```

### Generate Ethereum Address

```javascript
// Start with compressed public key
const compressedPub = "03c026c4b041059c84a187252682b6f80cbbe64eb81497111ab6914b050a8936fd";

// Expand to uncompressed (65 bytes, starts with 04)
const uncompressedPub = CryptoUtils.expand_pub(compressedPub);

// Generate Ethereum address
const ethAddress = CryptoUtils.pub_to_eth_address(uncompressedPub);
// "0x2161DedC3Be05B7Bb5aa16154BcbD254E9e9eb68"
```

### Generate Bitcoin Cash Address

```javascript
const publicKey = "036740c4f55d64fb6c9bc412084638b80062cee07f6c84b205671584e82a7c96b7";

// First get legacy address
const legacyAddress = CryptoUtils.pub_to_address("00", publicKey);

// Convert to CashAddr format
const cashAddr = CryptoUtils.pub_to_cashaddr(legacyAddress);
// "bitcoincash:qzeaman75678yrxjqgqs0lz246ql2x9sfupluc8lgg"
```

### Private Key to WIF

```javascript
const privateKey = "922c2cc579600419c8cde59ca8fcb03518ddbb5c38ed1bfbd150cb100e1f5430";

// Bitcoin mainnet WIF (compressed)
const wif = CryptoUtils.privkey_wif("80", privateKey, true);
// "L27rKoVgW4dYc1BxhD1X7jp9ixPGwnn3E2eggznmpYcuktDYPKaH"

// Litecoin mainnet WIF
const ltcWif = CryptoUtils.privkey_wif("b0", privateKey, true);
// "T7x7mYnruSc9NqpqEqxPL6MXfp2b1snw3EYwYoRKPWo5GmmddZsh"
```

---

## Encoding Functions

### Base58Check

```javascript
// Encode with checksum (used for addresses, WIF)
const publicKey = "036740c4f55d64fb6c9bc412084638b80062cee07f6c84b205671584e82a7c96b7";
const hash160 = CryptoUtils.hash160(publicKey);
const encoded = CryptoUtils.b58check_encode("00" + hash160);
// "1HQ3rb7nyLPrjnuW85MUknPekwkn7poAUm"

// Decode (validates checksum, throws on invalid)
const decoded = CryptoUtils.b58check_decode("1HQ3rb7nyLPrjnuW85MUknPekwkn7poAUm");
// Returns hex payload without checksum
```

### Plain Base58

```javascript
// Encode (no checksum)
const encoded = CryptoUtils.b58enc(hexString);

// Decode
const decoded = CryptoUtils.b58dec(base58String);
```

### Bech32 (SegWit)
```javascript
// Decode SegWit address
const decoded = CryptoUtils.bech32_decode("bc1qk0wlvl4xh3eqe5szqyrlcj4ws8633vz0vhhywl");
// { hrp: "bc", data: [0, 192, 158, ...] }

// Roundtrip: decode and re-encode
const encoded = CryptoUtils.bech32_encode(decoded.hrp, decoded.data);
// "bc1qk0wlvl4xh3eqe5szqyrlcj4ws8633vz0vhhywl"

// Encode from hash160 (20 bytes)
const publicKey = "036740c4f55d64fb6c9bc412084638b80062cee07f6c84b205671584e82a7c96b7";
const hash160 = CryptoUtils.hash160(publicKey);
const hash160Bytes = CryptoUtils.hex_to_bytes(hash160);
const words = CryptoUtils.to_words(hash160Bytes);
words.unshift(0); // witness version 0
const address = CryptoUtils.bech32_encode("bc", words);
```

---

## Hashing Functions

### SHA256

```javascript
const hash = CryptoUtils.hmacsha(data, "sha256");
// Returns hex string
```

### Hash160 (SHA256 + RIPEMD160)

```javascript
// Used for Bitcoin addresses
const hash160 = CryptoUtils.hash160(publicKey);
// Returns 40-char hex (20 bytes)
```

### Keccak256 (Ethereum)

```javascript
// Used for Ethereum addresses and signing
const keccak = CryptoUtils.keccak256(data);
```

### HMAC-SHA512

```javascript
// Used in BIP32 key derivation
const hmac = CryptoUtils.hmac_bits(data, key, "hex");
// Returns 128-char hex (64 bytes)
```

---

## Hex/Byte Conversion

```javascript
// Hex string to Uint8Array
const bytes = CryptoUtils.hex_to_bytes("deadbeef");

// Uint8Array to hex string
const hex = CryptoUtils.bytes_to_hex(bytes);

// Decimal to hex
const hex = CryptoUtils.dec_to_hex(255);  // "ff"

// Hex to BigInt
const bigint = CryptoUtils.hex_to_dec("ff");  // 255n

// Pad string with leading zeros
const padded = CryptoUtils.str_pad("ab", 4);  // "00ab"
```

---

## AES Encryption

```javascript
// Encrypt with password
const encrypted = CryptoUtils.aes_enc("secret data", "password123");

// Decrypt
const decrypted = CryptoUtils.aes_dec(encrypted, "password123");
// "secret data"
```

---

## Compatibility Testing

The library includes built-in test functions to verify browser/environment compatibility before use.

### Quick Compatibility Check

```javascript
// Check if all core features work
if (CryptoUtils.test_crypto_api() && CryptoUtils.test_bigint() && CryptoUtils.test_secp256k1()) {
	console.log("Environment compatible!");
}
```

### Individual Test Functions

```javascript
// Test crypto.getRandomValues availability
CryptoUtils.test_crypto_api();  // returns true/false

// Test BigInt support
CryptoUtils.test_bigint();  // returns true/false

// Test secp256k1 key derivation
CryptoUtils.test_secp256k1();  // returns true/false

// Test Bech32 address encoding
CryptoUtils.test_bech32();  // returns true/false

// Test Bitcoin Cash CashAddr encoding
CryptoUtils.test_cashaddr();  // returns true/false

// Test Keccak256 / Ethereum address derivation
CryptoUtils.test_keccak256();  // returns true/false

// Test AES encryption round-trip
CryptoUtils.test_aes();  // returns true/false
```

### Test Constants

The library exposes test vectors for verification. The bech32/eth/cashaddr vectors are derived from the standard [BIP39 test phrase](https://github.com/bitcoinbook/bitcoinbook/blob/f8b883dcd4e3d1b9adf40fed59b7e898fbd9241f/ch05.asciidoc): `army van defense carry jealous true garbage claim echo media make crunch`

```javascript
const TC = CryptoUtils.crypto_utils_const;

TC.version              // "1.1.0"

// secp256k1 test (private key 1 = generator point G)
TC.test_privkey         // "0000...0001"
TC.test_pubkey          // "0279be667ef9dcbbac55a06295ce870b07029bfcdb2dce28d959f2815b16f81798"

// Bech32 test vectors (from test phrase, m/84'/0'/0'/0/0)
TC.test_pubkey_bech32   // public key for bech32 test
TC.test_address_bech32  // "bc1qg0azlj4w2lrq8jssrrz6eprt2fe7f7edm4vpd5"

// Ethereum test vectors (from test phrase, m/44'/60'/0'/0/0)
TC.test_pubkey_eth      // public key for Ethereum test
TC.test_address_eth     // "0x2161DedC3Be05B7Bb5aa16154BcbD254E9e9eb68"

// CashAddr test vectors
TC.test_legacy_address  // legacy Bitcoin address
TC.test_address_cashaddr // "qp5p0eur784pk8wxy2kzlz3ctnq5whfnuqqpp78u22"
```

---

## API Reference

### Key Operations

| Function | Parameters | Returns | Description |
|----------|------------|---------|-------------|
| `get_publickey` | `privateKey` | `string` | Derive compressed public key (33 bytes) |
| `priv_to_pub` | `privateKey` | `string` | Alias for get_publickey |
| `expand_pub` | `compressedPub` | `string` | Convert to uncompressed (65 bytes) |
| `privkey_wif` | `version, key, compressed` | `string` | Private key to WIF format |
| `normalize_privatekey` | `key` | `string` | Normalize private key format |

### Address Generation

| Function | Parameters | Returns | Description |
|----------|------------|---------|-------------|
| `pub_to_address` | `version, pubkey` | `string` | Legacy P2PKH address |
| `pub_to_address_bech32` | `hrp, pubkey` | `string` | Native SegWit (bc1q..., ltc1q...) |
| `hash160_to_address` | `version, hash` | `string` | Address from Hash160 |
| `pub_to_eth_address` | `uncompressedPub` | `string` | Ethereum address (0x...) |
| `to_checksum_address` | `address` | `string` | EIP-55 checksum format |
| `pub_to_cashaddr` | `legacyAddress` | `string` | Bitcoin Cash CashAddr |

### Address Version Bytes

| Currency | Legacy | SegWit HRP |
|----------|--------|------------|
| Bitcoin | `00` | `bc` |
| Litecoin | `30` | `ltc` |
| Dogecoin | `1e` | - |
| Dash | `4c` | - |

### WIF Version Bytes

| Currency | Version |
|----------|---------|
| Bitcoin | `80` |
| Litecoin | `b0` |
| Dogecoin | `9e` |
| Dash | `cc` |

### Base58 Functions

| Function | Parameters | Returns | Description |
|----------|------------|---------|-------------|
| `b58enc` | `hex` | `string` | Base58 encode |
| `b58dec` | `string` | `string` | Base58 decode |
| `b58check_encode` | `hex` | `string` | Base58Check encode (adds checksum) |
| `b58check_decode` | `string` | `string` | Base58Check decode (validates checksum) |

### Bech32 Functions

| Function | Parameters | Returns | Description |
|----------|------------|---------|-------------|
| `bech32_encode` | `hrp, data` | `string` | Bech32 encode |
| `bech32_decode` | `string` | `{hrp, data}` | Bech32 decode |
| `to_words` | `bytes` | `array` | Convert bytes to 5-bit words |
| `from_words` | `words` | `array` | Convert 5-bit words to bytes |

### Hashing Functions

| Function | Parameters | Returns | Description |
|----------|------------|---------|-------------|
| `hmacsha` | `data, algo` | `string` | SHA256/SHA512 hash |
| `hmac_bits` | `data, key, format` | `string` | HMAC with bit array |
| `hash160` | `data` | `string` | SHA256 + RIPEMD160 |
| `keccak256` | `data` | `string` | Keccak-256 (Ethereum) |
| `sha_sub` | `data, algo` | `string` | Generic SHA hash |

### Hex/Byte Conversion

| Function | Parameters | Returns | Description |
|----------|------------|---------|-------------|
| `hex_to_bytes` | `hex` | `Uint8Array` | Hex string to bytes |
| `bytes_to_hex` | `bytes` | `string` | Bytes to hex string |
| `dec_to_hex` | `decimal` | `string` | Decimal to hex |
| `hex_to_dec` | `hex` | `BigInt` | Hex to BigInt |
| `str_pad` | `string, length` | `string` | Pad with leading zeros |

### String Utilities

| Function | Parameters | Returns | Description |
|----------|------------|---------|-------------|
| `clean_string` | `str` | `string` | Normalize whitespace |
| `normalize_string` | `str` | `string` | NFKD normalization |
| `split_words` | `str` | `array` | Split into word array |
| `join_words` | `arr` | `string` | Join word array |

### Encryption

| Function | Parameters | Returns | Description |
|----------|------------|---------|-------------|
| `aes_enc` | `data, password` | `string` | AES encrypt |
| `aes_dec` | `data, password` | `string` | AES decrypt |

### Testing Functions

| Function | Parameters | Returns | Description |
|----------|------------|---------|-------------|
| `test_crypto_api` | - | `boolean` | Test crypto.getRandomValues availability |
| `test_bigint` | - | `boolean` | Test BigInt functionality |
| `test_secp256k1` | - | `boolean` | Test secp256k1 key derivation |
| `test_bech32` | - | `boolean` | Test Bech32 address encoding |
| `test_cashaddr` | - | `boolean` | Test Bitcoin Cash CashAddr encoding |
| `test_keccak256` | - | `boolean` | Test Keccak256 / Ethereum address |
| `test_aes` | - | `boolean` | Test AES encryption round-trip |

### Constants

| Property | Type | Description |
|----------|------|-------------|
| `crypto_utils_const` | `object` | Test vectors and version info |
| `CURVE` | `object` | secp256k1 curve parameters (P, N, Gx, Gy) |

---

## Supported Currencies

| Currency | Address Types | Notes |
|----------|---------------|-------|
| Bitcoin | Legacy (1...), SegWit (bc1q...) | Full support |
| Litecoin | Legacy (L...), SegWit (ltc1q...) | Full support |
| Ethereum | 0x... (EIP-55 checksum) | Full support |
| Bitcoin Cash | CashAddr (bitcoincash:q...) | Full support |
| Dogecoin | Legacy (D...) | Full support |
| Dash | Legacy (X...) | Full support |

---

## Interactive Test Suite

The test suite (`unit_tests_crypto_utils.html`) includes:

### Automated Tests (50+ tests)

**Built-in Library Tests**
- `CryptoUtils.test_crypto_api` - Crypto API availability
- `CryptoUtils.test_bigint` - BigInt support
- `CryptoUtils.test_secp256k1` - Key derivation
- `CryptoUtils.test_bech32` - Bech32 encoding
- `CryptoUtils.test_cashaddr` - CashAddr encoding
- `CryptoUtils.test_keccak256` - Ethereum address derivation
- `CryptoUtils.test_aes` - AES encryption

**Unit Tests**
- Key generation and derivation
- Address generation for all formats
- Base58/Base58Check encoding
- Bech32 encoding
- Hashing functions
- Hex conversion utilities
- Test vector validation

### Interactive Tools
1. **Key Generation** - Generate keypairs from private key
2. **Address Generation** - Create addresses for different currencies
3. **Base58 Encode/Decode** - Test Base58Check operations
4. **Bech32 Encode/Decode** - Test SegWit address operations
5. **Hash Functions** - Test SHA256, Hash160, Keccak256
6. **AES Encryption** - Test encrypt/decrypt

---

## Security Warning

⚠️ **Never enter real private keys on websites or share them with anyone.**

This library is intended for:
- Educational purposes
- Development and testing
- Client-side key generation (with proper entropy)

---

## Dependencies

- **sjcl.js** - Stanford JavaScript Crypto Library

---

## Related Projects

- [bip39-utils-js](https://github.com/bitrequest/bip39-utils-js) - BIP39/BIP32 HD wallet utilities
- [xmr-utils-js](https://github.com/bitrequest/xmr-utils-js) - Monero cryptographic utilities
- [bitrequest](https://github.com/bitrequest/bitrequest.github.io) - Cryptocurrency point-of-sale PWA

---

## License

AGPL-3.0 License

## Credits

Developed for [bitrequest.io](https://bitrequest.io) - Lightweight cryptocurrency point-of-sale.

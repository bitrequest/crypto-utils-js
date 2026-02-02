# Crypto Utils JS

Pure JavaScript cryptocurrency utilities library. Low-level building blocks for Bitcoin, Ethereum, Litecoin, Kaspa, Nimiq, Nano, and other cryptocurrencies. Extracted from [bitrequest](https://github.com/bitrequest/bitrequest.github.io) for reuse in other projects.

*No Node.js, no WebAssembly, just vanilla JavaScript.*

## Features

- **secp256k1 Elliptic Curve** - Private/public key operations
- **Ed25519 Elliptic Curve** - Edwards curve key derivation (Nimiq, Nano, Monero)
- **Address Generation** - Legacy (P2PKH), SegWit (Bech32), Ethereum, Bitcoin Cash (CashAddr), Kaspa, Nimiq, Nano
- **Base58/Base58Check** - Encoding and decoding with checksum validation
- **Bech32** - Native SegWit address encoding/decoding
- **Hashing** - SHA256, RIPEMD160, Hash160, Keccak256, Blake2b, HMAC
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

### Generate Kaspa Address

Kaspa uses a custom bech32 variant with 8-character (40-bit) checksum instead of the standard 6-character checksum.

```javascript
const publicKey = "036740c4f55d64fb6c9bc412084638b80062cee07f6c84b205671584e82a7c96b7";

// Generate Kaspa address (kaspa:q...)
const kaspaAddress = CryptoUtils.pub_to_kaspa_address(publicKey);
// "kaspa:qzn54t6vpasykvudztupcpwn2gelxf8y9p73uu5jcs3dg8s4yzmcgnu7jhj5l"
```

The function automatically extracts the x-only public key (32 bytes) from the compressed key, as Kaspa uses Schnorr-style addressing.

### Generate Nimiq Address

Nimiq uses Ed25519 with SHA-512 for public key derivation, Blake2b-256 hashing, and a custom Base32 encoding with IBAN-style checksum.

```javascript
// Ed25519 private key (32 bytes hex)
const privateKey = "9eac269fb28cbeab3c7cd77b60daa4590e1316b6e9a71e5e58dfeaa40d9ebc15";

// Derive Ed25519 public key (SHA-512 variant)
const publicKey = CryptoUtils.ed25519_pubkey(privateKey);

// Generate Nimiq address (NQ...)
const nimiqAddress = CryptoUtils.to_nimiq_address(publicKey);
// "NQ288KG7ER5QUANFN5X1J1CJFRN6FE8GC1KM"
```

### Generate Nano Address

Nano uses a Blake2b-512 variant of Ed25519 for key derivation, with a custom Base32 encoding and Blake2b-5 checksum.

```javascript
// Ed25519 private key (32 bytes hex)
const privateKey = "9eac269fb28cbeab3c7cd77b60daa4590e1316b6e9a71e5e58dfeaa40d9ebc15";

// Derive Ed25519 public key (Blake2b-512 variant, different from standard SHA-512)
const publicKey = CryptoUtils.nano_ed25519_pubkey(privateKey);

// Generate Nano address (nano_...)
const nanoAddress = CryptoUtils.to_nano_address(publicKey);
// "nano_1mbtirc4x3kixfy5wufxaqakd3gbojpn6gpmk6kjiyngnjwgy6yty3txgztq"
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

### Kaspa Bech32 (40-bit Checksum)

Kaspa uses a modified bech32 encoding with key differences from Bitcoin's implementation:

```javascript
// Generate Kaspa address directly from public key
const publicKey = "036740c4f55d64fb6c9bc412084638b80062cee07f6c84b205671584e82a7c96b7";
const kaspaAddress = CryptoUtils.pub_to_kaspa_address(publicKey);
// "kaspa:qzn54t6vpasykvudztupcpwn2gelxf8y9p73uu5jcs3dg8s4yzmcgnu7jhj5l"

// Low-level Kaspa bech32 functions
const data = [0, 1, 2, 3]; // 5-bit words
const checksum = CryptoUtils.kaspa_create_checksum("kaspa", data);
// Returns 8 values (40-bit checksum)

// Polymod calculation for Kaspa's 40-bit checksum
const polymod = CryptoUtils.kaspa_polymod([1, 2, 3]);
```

| Feature | Bitcoin Bech32 | Kaspa Bech32 |
|---------|----------------|--------------|
| Checksum length | 6 chars (30-bit) | 8 chars (40-bit) |
| HRP expansion | High + low 5 bits | Low 5 bits only |
| Generator polynomial | 0x3b6a57b2 | Split 40-bit |
| Address prefix | bc1q... | kaspa:q... |

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

### Blake2b

```javascript
// Blake2b with configurable output length (used by Nimiq, Nano)
const hash = CryptoUtils.blake2b(inputHex, outputLengthBytes);

// Blake2b-256 (32 bytes) — used for Nimiq address hashing
const hash256 = CryptoUtils.blake2b(data, 32);

// Nimiq-specific: Blake2b-256 of raw bytes
const nimiqHash = CryptoUtils.nimiq_hash(rawHex);
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

// Test Kaspa address encoding (40-bit bech32)
CryptoUtils.test_kaspa();  // returns true/false
```

### Test Constants

The library exposes test vectors for verification. The bech32/eth/cashaddr vectors are derived from the standard [BIP39 test phrase](https://github.com/bitcoinbook/bitcoinbook/blob/f8b883dcd4e3d1b9adf40fed59b7e898fbd9241f/ch05.asciidoc): `army van defense carry jealous true garbage claim echo media make crunch`

```javascript
const TestVector = CryptoUtils.crypto_utils_const;

TestVector.version              // "1.1.0"

// secp256k1 test (private key 1 = generator point G)
TestVector.test_privkey         // "0000...0001"
TestVector.test_pubkey          // "0279be667ef9dcbbac55a06295ce870b07029bfcdb2dce28d959f2815b16f81798"

// Bech32 test vectors (from test phrase, m/84'/0'/0'/0/0)
TestVector.test_pubkey_bech32   // public key for bech32 test
TestVector.test_address_bech32  // "bc1qg0azlj4w2lrq8jssrrz6eprt2fe7f7edm4vpd5"

// Ethereum test vectors (from test phrase, m/44'/60'/0'/0/0)
TestVector.test_pubkey_eth      // public key for Ethereum test
TestVector.test_address_eth     // "0x2161DedC3Be05B7Bb5aa16154BcbD254E9e9eb68"

// CashAddr test vectors
TestVector.test_legacy_address  // legacy Bitcoin address
TestVector.test_address_cashaddr // "qp5p0eur784pk8wxy2kzlz3ctnq5whfnuqqpp78u22"

// Kaspa test vectors (from test phrase, m/44'/111111'/0'/0/0)
TestVector.test_pubkey_kaspa    // public key for Kaspa test
TestVector.test_address_kaspa   // "kaspa:qpd7m89g20e989s8ue5gqkwkv9k94nvxe562exrey7lrcjmzqegn2wspgcke4"

// Nimiq test vectors (from test phrase, SLIP-0010 m/44'/242'/0'/0')
TestVector.test_address_nimiq   // "NQ288KG7ER5QUANFN5X1J1CJFRN6FE8GC1KM"

// Nano test vectors (from test phrase, SLIP-0010 m/44'/165'/0')
TestVector.test_address_nano    // "nano_1mbtirc4x3kixfy5wufxaqakd3gbojpn6gpmk6kjiyngnjwgy6yty3txgztq"
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

### Ed25519 Key Operations

| Function | Parameters | Returns | Description |
|----------|------------|---------|-------------|
| `ed25519_pubkey` | `privateKey` | `string` | Ed25519 public key via SHA-512 (Nimiq) |
| `nano_ed25519_pubkey` | `privateKey` | `string` | Ed25519 public key via Blake2b-512 (Nano) |
| `ed25519_point_multiply` | `hex` | `EdPoint` | Scalar × basepoint multiplication |
| `ed_bytes_to_number_le` | `Uint8Array` | `BigInt` | Little-endian bytes to BigInt |

### Address Generation

| Function | Parameters | Returns | Description |
|----------|------------|---------|-------------|
| `pub_to_address` | `version, pubkey` | `string` | Legacy P2PKH address |
| `pub_to_address_bech32` | `hrp, pubkey` | `string` | Native SegWit (bc1q..., ltc1q...) |
| `hash160_to_address` | `version, hash` | `string` | Address from Hash160 |
| `pub_to_eth_address` | `uncompressedPub` | `string` | Ethereum address (0x...) |
| `to_checksum_address` | `address` | `string` | EIP-55 checksum format |
| `pub_to_cashaddr` | `legacyAddress` | `string` | Bitcoin Cash CashAddr |
| `pub_to_kaspa_address` | `pubkey` | `string` | Kaspa address (kaspa:q...) |
| `to_nimiq_address` | `rawHex` | `string` | Nimiq address (NQ...) from public key |
| `to_nano_address` | `pubkey` | `string` | Nano address (nano_...) from public key |
| `nano_to_raw` | `amount` | `string` | Convert NANO to raw units |

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
| `kaspa_polymod` | `values` | `BigInt` | Kaspa 40-bit polymod calculation |
| `kaspa_create_checksum` | `hrp, data` | `array` | Kaspa 8-char checksum |
| `pub_to_kaspa_address` | `pubkey` | `string` | Public key to Kaspa address |

### Hashing Functions

| Function | Parameters | Returns | Description |
|----------|------------|---------|-------------|
| `hmacsha` | `data, algo` | `string` | SHA256/SHA512 hash |
| `hmac_bits` | `data, key, format` | `string` | HMAC with bit array |
| `hash160` | `data` | `string` | SHA256 + RIPEMD160 |
| `keccak256` | `data` | `string` | Keccak-256 (Ethereum) |
| `blake2b` | `data, outLen` | `string` | Blake2b hash (configurable length) |
| `nimiq_hash` | `rawHex` | `string` | Blake2b-256 hash (Nimiq) |
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
| `test_kaspa` | - | `boolean` | Test Kaspa address encoding |

### Constants

| Property | Type | Description |
|----------|------|-------------|
| `crypto_utils_const` | `object` | Test vectors and version info |
| `CURVE` | `object` | secp256k1 curve parameters (P, N, Gx, Gy) |
| `ED25519` | `object` | Ed25519 curve parameters (P, n, a, d, Gx, Gy) |
| `EdPoint` | `class` | Ed25519 affine point (fromHex, add, multiply, toHex) |

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
| Kaspa | kaspa:q... (40-bit bech32) | Full support |
| Nimiq | NQ... (IBAN-style checksum) | Ed25519 + Blake2b |
| Nano | nano_... (custom base32) | Ed25519-Blake2b variant |

---

## Interactive Test Suite

The test suite (`unit_tests_crypto_utils.html`) includes:

### Automated Tests (140+ tests)

**Built-in Library Tests**
- `CryptoUtils.test_crypto_api` - Crypto API availability
- `CryptoUtils.test_bigint` - BigInt support
- `CryptoUtils.test_secp256k1` - Key derivation
- `CryptoUtils.test_bech32` - Bech32 encoding
- `CryptoUtils.test_cashaddr` - CashAddr encoding
- `CryptoUtils.test_keccak256` - Ethereum address derivation
- `CryptoUtils.test_aes` - AES encryption
- `CryptoUtils.test_kaspa` - Kaspa address encoding

**Unit Tests**
- Key generation and derivation (secp256k1 + Ed25519)
- Address generation for all formats (including Kaspa 40-bit bech32, Nimiq, Nano)
- Ed25519 curve operations and public key derivation
- Blake2b hashing (Nimiq, Nano)
- Base58/Base58Check encoding
- Bech32 encoding (standard and Kaspa variant)
- Hashing functions (SHA256, Hash160, Keccak256, Blake2b)
- Hex conversion utilities
- Test vector validation

### Interactive Tools
1. **Key Generation** - Generate keypairs from private key (secp256k1 + Ed25519)
2. **Address Generation** - Create addresses for different currencies (including Kaspa)
3. **Nimiq Address** - Private key to Nimiq address (Ed25519 + Blake2b)
4. **Nano Address** - Private key to Nano address (Ed25519-Blake2b)
5. **Blake2b Hash** - Test Blake2b hashing with configurable output length
6. **NANO → RAW** - Convert NANO amounts to raw units
7. **Kaspa Address** - Generate Kaspa addresses from public key
8. **Base58 Encode/Decode** - Test Base58Check operations
9. **Bech32 Encode/Decode** - Test SegWit address operations
10. **Hash Functions** - Test SHA256, Hash160, Keccak256
11. **AES Encryption** - Test encrypt/decrypt
12. **WIF Conversion** - Private key to Wallet Import Format
13. **LNURL Decode** - Lightning Network URL decoding
14. **Address → Script Hash** - Electrum format conversion
15. **String Utilities** - BIP39 helper functions
16. **Modular Arithmetic** - secp256k1 curve operations

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

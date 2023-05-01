---
title: Sui On-Chain Signatures Verification in Move
---

Move contracts in Sui supports verifications for several signature schemes on chain. Note that not all signatures supported in on-chain verification are supported as user signature verification. See [Sui Signatures](sui-signatures.md#user-signature) for valid signatures to be used for transaction execution. 

This topic covers:
 1. How to use [fastcrypto](https://github.com/MystenLabs/fastcrypto)'s CLI tool to create a signature of a given scheme. For testing and debugging only, do not use in production.
 1. Call the Move method on-chain to verification by submitting the signature, the message and the public key. 

Signature schemes covered: 
 1. Ed25519 signature (64 bytes)
 1. Secp256k1 non-recoverable signature (64 bytes)
 1. Secp256k1 recoverable signature (65 bytes)
 1. Secp256r1 non-recoverable signature (64 bytes)
 1. Secp256r1 recoverable signature (65 bytes)
 1. BLS G1 signature (minSig setting)
 1. BLS G2 signature (minPk setting)

## Usage

1. Set up fastcrypto CLI binary. 

```shell
git@github.com:MystenLabs/fastcrypto.git
cd fastcrypto/
cargo build --bin sigs-cli
```

2. Sign with CLI and submit to on-chain Move method.

 1. Ed25519 signature (64 bytes)
```shell
target/debug/sigs-cli keygen --scheme ed25519 --seed 0000000000000000000000000000000000000000000000000000000000000000                
Private key in hex: $SK
Public key in hex: $PK

target/debug/sigs-cli sign --scheme ed25519 --msg $MSG --secret-key  $SK

Signature in hex: $SIG
Public key in hex: $PK
```

Call smart contract's verify method. All inputs are represented in bytes in hex format:

```move
    let msg = x"$MSG";
    let pk = x"$PK";
    let sig = x"$SIG";
    let verify = ed25519::ed25519_verify(&sig, &pk, &msg);
    assert!(verify == true, 0);
```

 1. Secp256k1 non-recoverable signature (64 bytes)

```shell
target/debug/sigs-cli keygen --scheme secp256k1 --seed 0000000000000000000000000000000000000000000000000000000000000000                
Private key in hex: $SK
Public key in hex: $PK

target/debug/sigs-cli sign --scheme secp256k1 --msg $MSG --secret-key $SK

Signature in hex: $SIG
Public key in hex: $PK
```

Call smart contract's verify method:

```move
    let msg = x"$MSG";
    let pk = x"$PK";
    let sig = x"$SIG";
    let verify = ecdsa_k1::secp256k1_verify(&sig, &pk, &msg);
    assert!(verify == true, 0);
```

 1. Secp256k1 recoverable signature (65 bytes)

```shell
target/debug/sigs-cli keygen --scheme secp256k1-rec --seed 0000000000000000000000000000000000000000000000000000000000000000                
Private key in hex: $SK
Public key in hex: $PK

target/debug/sigs-cli sign --scheme secp256k1-rec --msg $MSG --secret-key $SK

Signature in hex: $SIG
Public key in hex: $PK
```

Call smart contract's ecrecover method and check equality:

```move
    let msg = x"$MSG";
    let pk = x"$PK";
    let sig = x"$SIG";
    let recovered = ecdsa_k1::secp256k1_ecrecover(&sig, &msg);
    assert!(pk == recovered, 0);
```

 1. Secp256r1 non-recoverable signature (64 bytes)

```shell
target/debug/sigs-cli keygen --scheme secp256r1 --seed 0000000000000000000000000000000000000000000000000000000000000000                
Private key in hex: $SK
Public key in hex: $PK

target/debug/sigs-cli sign --scheme secp256r1 --msg $MSG --secret-key $SK

Signature in hex: $SIG
Public key in hex: $PK
```

Call smart contract's verify method:

```move
    let msg = x"$MSG";
    let pk = x"$PK";
    let sig = x"$SIG";
    let verify = ecdsa_r1::secp256r1_verify(&sig, &pk, &msg);
    assert!(verify == true, 0);
```

 1. Secp256r1 recoverable signature (65 bytes)

```shell
target/debug/sigs-cli keygen --scheme secp256r1-rec --seed 0000000000000000000000000000000000000000000000000000000000000000                
Private key in hex: $SK
Public key in hex: $PK

target/debug/sigs-cli sign --scheme secp256r1-rec --msg $MSG --secret-key $SK

Signature in hex: $SIG
Public key in hex: $PK
```

Call smart contract's ecrecover method and check equality:

```move
    let msg = x"$MSG";
    let pk = x"$PK";
    let sig = x"$SIG";
    let recovered = ecdsa_r1::secp256r1_ecrecover(&sig, &msg);
    assert!(pk == recovered, 0);
```

 1. BLS G1 signature (minSig setting)

```shell
target/debug/sigs-cli keygen --scheme bls12381 --seed 0000000000000000000000000000000000000000000000000000000000000000                
Private key in hex: $SK
Public key in hex: $PK

target/debug/sigs-cli sign --scheme bls12381 --msg $MSG --secret-key $SK

Signature in hex: $SIG
Public key in hex: $PK
```

Call smart contract's verify method and check equality:

```move
    let msg = x"$MSG";
    let pk = x"$PK";
    let sig = x"$SIG";
    let verified = bls12381::bls12381_min_sig_verify(&sig, &msg);
    assert!(verify == true, 0);
```
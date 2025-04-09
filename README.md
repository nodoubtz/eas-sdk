# Ethereum Attestation Service - TypeScript/JavaScript SDK

[![Docs](https://img.shields.io/badge/docs-%F0%9F%93%84-blue)](https://eas.eth.link)
[![NPM Package](https://img.shields.io/npm/v/@ethereum-attestation-service/eas-sdk.svg)](https://www.npmjs.org/package/@ethereum-attestation-service/eas-sdk)
[![Test](https://github.com/ethereum-attestation-service/eas-sdk/actions/workflows/ci.yml/badge.svg)](https://github.com/ethereum-attestation-service/eas-sdk/actions/workflows/ci.yml)

This repository contains the Ethereum Attestation Service SDK, used to interact with the Ethereum Attestation Service Protocol.

## Table of Contents

- [Introduction](#introduction)
- [Features](#features)
- [Installation](#installation)
- [Usage](#usage)
- [Examples](#examples)
  - [Getting an Attestation](#getting-an-attestation)
  - [Creating Onchain Attestations](#creating-onchain-attestations)
  - [Revoking Onchain Attestations](#revoking-onchain-attestations)
- [Contributing](#contributing)
- [License](#license)

## Introduction

The Ethereum Attestation Service (EAS) SDK provides a simple and efficient way to interact with the EAS Protocol, enabling developers to create, fetch, and manage attestations on the Ethereum blockchain. It supports both on-chain and off-chain functionalities.

## Features

- Easy-to-use TypeScript/JavaScript SDK.
- Supports creating, reading, and revoking attestations.
- Includes utilities for encoding and decoding schema data.
- Supports private data handling using Merkle trees.
- Delegated attestations and revocations supported.

## Installation

Install the EAS SDK using your favorite package manager:

```sh
yarn add @ethereum-attestation-service/eas-sdk
```

OR

```sh
npm install @ethereum-attestation-service/eas-sdk
```

OR

```sh
pnpm add @ethereum-attestation-service/eas-sdk
```

## Usage

Import and initialize the library:

```javascript
import { EAS } from '@ethereum-attestation-service/eas-sdk';
import { ethers } from 'ethers';

// EAS Contract Address (Sepolia v0.26)
const EASContractAddress = '0xC2679fBD37d54388Ce493F1DB75320D236e1815e';

// Initialize the SDK
const eas = new EAS(EASContractAddress);

// Set up an ethers provider
const provider = ethers.getDefaultProvider('sepolia');

// Connect the provider to the SDK
eas.connect(provider);
```

## Examples

### Getting an Attestation

Retrieve an on-chain attestation by its unique identifier (UID):

```javascript
const uid = '0xYourAttestationUID';
const attestation = await eas.getAttestation(uid);

console.log(attestation);
```

### Creating Onchain Attestations

Create an on-chain attestation for a specific schema:

```javascript
import { SchemaEncoder } from '@ethereum-attestation-service/eas-sdk';

const schemaUID = '0xYourSchemaUID';
const schemaEncoder = new SchemaEncoder('uint256 eventId, uint8 voteIndex');

const encodedData = schemaEncoder.encodeData([
  { name: 'eventId', value: 1, type: 'uint256' },
  { name: 'voteIndex', value: 1, type: 'uint8' },
]);

const transaction = await eas.attest({
  schema: schemaUID,
  data: {
    recipient: '0xRecipientAddress',
    expirationTime: 0, // No expiration
    revocable: true,
    data: encodedData,
  },
});

const newAttestationUID = await transaction.wait();
console.log('New attestation UID:', newAttestationUID);
```

### Revoking Onchain Attestations

Revoke an on-chain attestation:

```javascript
const transaction = await eas.revoke({
  schema: '0xYourSchemaUID',
  data: { uid: '0xYourAttestationUID' },
});

await transaction.wait();
console.log('Attestation revoked');
```

## Contributing

Contributions are welcome! Please follow the [contribution guidelines](CONTRIBUTING.md) to get started.

## License

This project is licensed under the BSD 3-Clause "New" or "Revised" License. See the [LICENSE](LICENSE) file for details.

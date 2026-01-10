---
layout: post
title: The Problem with Passkeys
description: A summary of issues when signing crypto transactions with passkeys, and some alternative approaches.
comments: true
tags: [security, crypto]
---

Crypto wallets are hard to use because of seed phrases. Users forget to back them up or can’t access them on new devices. Small mistakes can leak the phrase and lead to a loss of funds.

Passkeys have been proposed as an alternative because they are automatically backed up and harder to leak. But they are built on WebAuthn, which was designed for website authentication, not for presenting and signing transactions. Using them as primary signers introduces new problems for users.   

### Problems

The most important issue is that passkeys don't move across device ecosystems[^1]. A passkey created on a Mac is stored in Keychain and can’t be used on Android. Because apps can’t predict which devices a user will add later, they still ask users to back up a seed phrase. This defeats the promise of passkeys as a universal recovery mechanism.

Signing flows are confusing because passkeys say "logging in" even when approving a transaction. The OS-controlled flow can't be modified, has multiple steps, and shows no transaction details. Users can't verify what they are signing and the prompts become cumbersome during frequent transactions. 

Passkeys standards[^2] and implementations don’t support the elliptic curves and signature formats used by Ethereum and Solana. Wallets work around this with smart contracts that accept passkeys as signers. The issue is that adding or removing passkeys must be replayed to every chain the user interacts with. There is no upper bound on the number of chains, each with different transaction types, fees, and failure modes to manage. 

### Alternative Approaches

One solution is to use passkeys for recovery, not signing. Apps can store a seed phrase in the largeBlob or PRF fields of a passkey, which sync across devices within that ecosystem. The app can load the seed phrase into memory and use its own signing flow. Users still need to backup the seed phrase for unsupported devices, but don't have to use it as frequently. 

Server-side wallets offer a simpler experience by storing keys in a Trusted Execution Environment. The TEE signs transactions once a user proves control of an email address, making recovery straightforward. Providers can layer on MFA or spending limits through policies. The tradeoff is trust, since users rely on the provider to implement TEEs  correctly and secure their infrastructure. 

Server-side wallets are a better fit for consumer apps than passkeys or raw seed phrases. With MFA, they offer security similar to consumer fintech apps without the usability headaches. Seed phrases remain the best option for minimizing trust assumptions, at the cost of user experience. Passkeys work best for recovery or as secondary signers.

<br/>
*Thanks to [Horsefacts](https://farcaster.xyz/horsefacts){:target='blank'} and [Tony D'Addeo](https://farcaster.xyz/deodad){:target='blank'} who did the research on passkey implementations and helped with drafts.*
<br/><br/>

-----

[^1]: A third party password manager like 1Password can sync across platforms, but this is not easy for users to figure out.  


[^2]: WebAuthn: [Proposal for general cryptographic signatures](https://github.com/w3c/webauthn/issues/1608){:target='blank'}.
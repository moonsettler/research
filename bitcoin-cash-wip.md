# Bitcoin Cash
## Using bitcoin as cash

Cash is a physical form of money, a bearer asset with a denomination, that has anti-forgery features that allow for independent offline verification. Bitcoin in this context also takes care of the scarcity of the currency.

## Motivation

The limitations of the bitcoin blokchain, including the transactions per second (tps) stemming from the block limit, and the strong traceability stemming from the UTXO model are well known. Making ownership of a UTXO transferrable would do a great deal to mitigate these issues. Statechains and tamper evident device based physical layer-2 are typical examples of such attempts. Also often cited that 4 billion people still have no internet access. Devices like Bitbills, Casascius coins, Opendimes can attest to the private key not being revealed, but the payee would still need to look up the balance of the address associated with the device online to verify the value. Making bitcoin denominations physically transferrable and verifiable offline would turn bitcoin into real cash.

## Problem

Physical tamper evident devices already exist. The security tradeoffs they represent are well understood, their technological evolution can be expected to continue. The remaining challenge is to attest to a specific balance offline in a trust minimized way.

## Scope

Tamper evidence and securing the private key from 'double spend' attacks, either before or after receiving payment is out of scope for this paper. There are two main types of physical devices existing that facilitate ownership transfer of a private key:

* Trusted issuer (ex. Bitbill, Casascius)
* Trusted device (ex. Opendime, Satscard)

The main difference lies in the issuers/manufacturers potential knowledge of private keys (or the potential disconnect between public and private keys) in case of a trusted issuer; as opposed to a trusted device, where the issuer can not know the private key without breaking seal, which is a tamper evident action.

## Concept

Having a device attesting to a public key or address is the first step. The second is to confirm the funding transaction to be valid, and included in the longest chain with the largest cumulative proof of work (PoW).

This can be a simple online lookup: either using a block explorer, or connecting to one's own full node.

However there is a way to also include an additional amount of data on a physical device that can contain:

* The funding transaction
* The SPV proof to the block header
* N consecutive block headers as proof of forge cost (PoFC)

The payee having some rough estimate for current difficulty, bitcoin price and average block reward can do an estimate for the cost of a forgery, which would mean a fake block header for the SPV proof with sufficient PoW that is not part of the longest chain with the most cumulative PoW.

Over time as price and difficulty goes up, the attestation to PoFC may become insufficient. However even with limited storage capacity there are ways to keep things verifiable:

* Zero-Knowledge proofs used to "compress" PoW for an arbitrary number of consecutive blocks
* Payee having sporadic access to updates for checkpoint block headers from the longest valid chain
* Merklized UTXO commitments (or utreexo blocks) may allow refreshing PoFC attestation at a higher difficulty

The first requires the current owner of the UTXO, who's possession the device is in, to update the PoFC from time to time. The second requires the payees to have a not too out of date, sparse snapshot of the block headers.

For a simple example let's assume that all PoFC attestations are at least 100 block long as a "standard" and that payees going to have every 100th block headers (as checkpoints) for the longest chain on their device they use to verify. At this moment that requires 7400 block headers 80 bytes each. Strictly their hash would actually be enough which is only 32 bytes. The payee having the ability to sporadically update their list from a trusted source (preferably their own full node) can verify any arbitrarily old UTXO was indeed part of the blockchain. For recent UTXOs the PoFC attestation compared to the UTXO value should be sufficient.

## Possible attack scenarios

* Counterfeit device
* Malicious device
* Tampered device / private key extraction
* Counterfeit chain / bitcoin

### Counterfeit device

Fake device that is visually indistinguishable from the real one: no actual ownership is being transferred.
**mitigation:** The device can generate BIP-322 messages to show that it could spend them without revealing the private key. This proves that the device has control over the UTXO, just not necessarily exclusive control. (nothingmuch)

### Malicious device

Bogus DRNG for private key generation. The manufacturer can know the private keys and can swipe the UTXO in a way that offline verification will not reveal.
**mitigation:** If the user provides entropy and the device rotates the UTXO to a pubkey which is proven in zero knowledge to be include that *in addition* to its own, then the user can conclude no other device has the private key without learning the private key. (nothingmuch)

### Tampered device / private key extraction

The device could be tampered with in a non evident way or the private key extracted in a non-invasive way (ex. high-res x-ray on holographic sticker type devices, side channel attacks, etc.). Cost of this type of attack has to be reasonably high compared to the usual UTXO values for physical devices to be viable for transfer of private key ownership.

### Counterfeit chain / bitcoin

Proof of forge cost is insufficient for the denomination and payee is presented with fake block headers from his trusted source which he can't independently verify: accepts counterfeit bitcoin.
**mitigation:** Merklized UTXO commitments in bitcoin blocks of various kind may be used to update PoFC without heavy ZK proofs by showing the UTXO was part of the UTXO set at a higher block height and higher difficulty. (nothingmuch)

## Conclusion

While a theoretical possibility exists for abuse, the risk of running into an online verifier in short order and getting the transactions back-traced by investigators or private individuals is high. All has to be weighted against the cost and difficulty of said attacks compared to the value of the denominations.

Worst case someone with no internet access will have to trust some "authority" to present him with updates on bitcoin metrics and block header checkpoints. However, depending on the source and the scale of the forging operation to be economically viable it paints the picture of a large scale conspiracy where commitments are published that can be independently verified by anyone running a full node and bothering to verify. The more mainstream the publication is, the more people would trust it, the more it is exposed to be falsified. Infrequent updates by the payees actually make it less likely that such an exploit can go unnoticed by the time they would rely on bogus data as scandalous news of an attempted exploit are sure to spread fast.

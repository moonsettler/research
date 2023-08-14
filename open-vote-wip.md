# Open Vote (draft)
*A trustless private voting system implemented as an opensource project.*
## Digital Identity
A merkle tree of claims and their blinding factors. First claim is a public key that can be used to sign a message, and the owner may selectively reveal a certain subset of immutable claims, for example: year of birth, citizenship, postal code without revealing any personally identifying or sensitive data... It may be signed by an Identity Provider which may be a state authority or a federated entity. Vote Organizers may accept the signature of an identity provider (cheaper for certain types of votes, polls and petitions) or may want to verify the identity of voters for themselves.

## Voter Registration
Registration cert issued by Vote Organizer via blind signature scheme. The VO never learnes of the Voting Pass pubKey. But it can verify that it has indeed signed the VP after it has checked for voter eligibility and it will only issue a single VP for a ballotId and a VOR cert. Any single person can only register once within an expiration period for a Voting Registry, and only get issued a single Voting Pass for any poll organized using that registry. The Identity Provider or the Vote Organizer can never learn who voted how in which poll even if they were to collude.

```json
// VP (Voting Pass) pass issued by vote organizer via blind signature scheme
{
	"ballotId": "fc2a4be9-ca20-49fa-91a0-562a093cb211",
	"pubKey": "feccc9d8131c33697d6296a9ba2d3765ca6a34952fc2c7e2dae30a1d9053b9ac",
	"_signature": "<VO-signature-on-VP-root-hash>"
}
```

## Trustless multiparty identity verification or voting pass issuance
### Each participant: *Alice, Bob, Carol the candidates and Mallory*
 1.  **rv = rng(2^128)** *generate random vector rv [128 bit]*
 2.  **m = ballotId|rv** *message m [256 bit], is ballotId [128 bit]concatenated with random vector rv [128 bit]*
 3.  **h(m) = sha256(m)** *hash h [256 bit] is sha256 hash of message m*
 4.  **s = sign(k, h)** *sign hash h with private key k for pubkey p*
 5.  **reveal (h, s), but not m**
 6.  **wait until all signatures are revealed and all messages commited to; check_sig(p, h)**
 7.  **reveal m**
 8.  **wait until all messages are revealed; check h'i = sha256(m'i); check ballotId**
 9.  **rv'i = split(m'i)** *extract all rv*
10.  **xrv = null; foreach(rv') => xrv = xor(xrv, rv'i)**
11.  **drng_init(xrv)**
12.  **ci = drng(cn)** *ci choosen certificate index = drng(cn) where cn is the number of certificates c[] submitted*
13.  **bs = bsig(k, c'ci)** *same as in a chaumian mint, have applicant reveal every member of c except ci!*

tldr: Everyone must sign a hash commitment for a round and everyone receives the commitments before they release their random vector for the drng seed!

### Alternative approach with BDH
Alternatively as a simpler scheme: each ballot can be identified by a shared public key of the signers; a VP would just be a signed pubkey hash, and then there is no reason to jump through all these hoops, there is no need to have probabilistic commit reveal verification. This is not dissimilar how chaumian ecash tokens would be signed by different keys for their different denominations. ballotId in this case could be the hash of the schnorr aggregate pubKey of the VO.

## Private Voting and Public Tabulating
 1.  VP pubkey is used to sign a vote (voting transaction)
 2.  After a VP is issued the VP holder is asked to wait by his own applcation until sufficient anonymity set is present
 3.  Anon set is measured on log scale (n stars for 10^n) and displayed for the voters by their own application
 4.  Voters are free to decide their preferred anon set that is appropriate for the population size of the ballot
 5.  Every option has a public key listed in the Ballot Manifest (Alice, Bob and Carol hold the private keys)
 6.  When casting a vote you encrypt with the option pubkey the integer value for the option (0, 1) with a random padding
 7.  Optionally some organizing authority will also publish a pubkey and the votes can be double encrypted
 8.  Votes are organized into blocks by a federation of nodes ran by VO members
 9.  Blocks form a federated threshold consensus blockchain and are optionally timestamped to the bitcoin blockchain
10.  Anyone can run a node that observes a ballot blockchain, but only VOs can add blocks (threshold signature)
11.  NGOs may run nodes to observe the elections and may act as anonymizing proxies that will hide the IP address of voters
12.  Anyone can decide which proxies/NOGs they trust, and even onion routing between multiple proxies is possible (tor alternative)
13.  Votes can not be tied to identity and can not be decrypted until the tabulation period starts and private keys are published
14.  At the end of the voting period the public tabulation begins by VOs releasing their private keys for the ballot
15.  Everyone who has downloaded and verified the blocks can now tabulate the results for themselves
16.  If a single VP signed multiple votes only the latest in the blockchain will be counted.
17.  Ballot Manifest may contain restrictions on how many times a VP can vote and at what intervals (dos protection).

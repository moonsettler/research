# cost 'analysis' comparision of attacks (wip)

let’s assume the reorg bounty for reorging a whole day 144 blocks is 10,000 btc, let’s assum in both cases 51% coordinating in a reorg has a chance to succeed (for easier comparision, pos could actually raise this limit)

both the cost of attack and the reward gets multiplied with the probability of success or failure. In both cases the attacker will distribute the bounty to the last 144 block reward addresses with his double spend tx.
#
## PoS
let’s assume 30% of the bitcoin supply gets staked, let’s assume simple epoch based drng staking, with 51% votes on no-show! out of 19m btc 5.7m is staked, 51% of that is 2.9m btc. at current prices ($20k) that is $59 billion locked in for 51% stakes.

the attackers being real bad at risk assesment, predict that the successful sucial consensus against them is only 10% and they assume they have a 90% chance of getting their reorg accepted as the longest chain and get away with it. (this is absolutely outrageously unrealistic, but we are working under the assumption that social consensus on sanctions is not easy to achieve)
```
expected returns in case of success: 0.9 * 0.51 * 10,000 btc - share of the block rewards for 144 blocks ~100 btc * 0.51

4590 - 51 = 4539 btc. roughly $90.7 million.
```
however has 10% chance to lose 2.9m btc, so the **expected profit overall** for executing the reorg is **-285,461 btc** which is a fat **$5.7 billion loss**.
#
## PoW
the operating costs are upper bound with the mier revenue, so i will use the miner revenue as basis for the estimate. 144 blocks will cost 100 btc to reorg currently. the miner assumes a successful social cordination to twart his efforts only has 10% chance and he is 90% sure he will succeed in rewriting history.
```
expected returns in case of success: 0.9 * 0.51 * 10,000 btc - share of the block rewards for 144 blocks ~100 btc * 0.51

4590 - 51 = 4539 btc. roughly $90 million.
```

however he has 10% chance to lose the operating costs and not accomplish the longest chain with his reorg:
```
144 + 0.49 x = 0.51 x
144 = 0.02 x
x = 7200 (blocks)
```
5040 btc but that has to be multiplied with the probability of 10%, so 504 btc cost.

so the **expected profit overall** for executing the reorg is **4,035 btc** which is a fat **$80.7 million profit**.

under **66%** assumptions it’s much worse the attacker would catch up in 436 blocks that only has an expected opportunity costs of **30.5 btc**.
#
## opinion
it would be more realistic to assume over 90% of the miners would take the bribe to reorg with well over 90% chance of success since bitcoiners are quiet fond of the heaviest chain rule.

## conclusion
pow does not actually offer good finality guarantees and good censorship resistance compared to pos, even with extremely unreliable social consensus as a fallback. the expected returns for pos stakers would pretty much always be negative, while pow miners would have minimal opportunity cost as detriment, putting a simple well understood price tag on reorgs that gets halved every 4 years.

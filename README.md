# Direct probability staking for prediction markets

Traditional prediction markets use AMM formulas (`x × y = k`) borrowed from DEXs. This requires initial liquidity, creates arbitrary constants, and derives probability indirectly from token ratios.

**I simplified this dramatically**

Track capital on each outcome and calculate probability directly with virtual liquidity smoothing. No pools, minimal complexity.

## Math

**Probability calculation:**
```
P_yes = (C_yes + b) / (C_yes + C_no + 2b)

where b = virtual liquidity parameter (e.g., $100)
```

**When betting amount B on YES:**
```
shares_received = B / P_yes
C_yes += B
```

**Payout if YES wins:**
```
payout = (your_shares / total_yes_shares) × (C_yes + C_no)
```
*Note: Virtual liquidity b is not part of the payout pool*

## Why I used virtual liquidity

- **Solves 0/0 problem**: Empty market starts at 50% automatically
- **Smooths early trades**: First $10 bet doesn't move market to 99%
- **Converges to direct model**: When C_yes >> b, formula approaches C_yes/(C_yes + C_no)
- **Still simpler than LMSR**: Linear formula, no exponentials

## Example (b = $100)
```
Start: C_yes = $0, C_no = $0
  P_yes = (0+100)/(0+0+200) = 50%

Alice bets $100 YES → gets 200 shares
  C_yes = $100
  P_yes = (100+100)/(100+0+200) = 66.7%

Bob bets $50 NO → gets 150 shares  
  C_no = $50
  P_yes = (100+100)/(100+50+200) = 57.1%

Charlie bets $100 YES → gets ~175 shares
  C_yes = $200
  P_yes = (200+100)/(200+50+200) = 66.7%

If YES wins:
  Total pot = $250 (only real capital)
  Alice: (200/375) × $250 = $133
  Charlie: (175/375) × $250 = $117
  Bob: $0
```

## Comparison to alternatives

**vs Constant Product AMM:**
- No pool initialization required
- Direct probability (not derived from ratios)
- Same smoothing benefit via parameter b

**vs LMSR:**
- Linear formula (simpler than logarithmic)
- Same bounded smoothing via parameter b
- More intuitive: probability = weighted capital ratio

**Trade-off:**
- Accepting one parameter (b) for smoothing
- But getting: direct probability, minimal code, no pool mechanics

## Properties

- ✅ Handles zero capital (starts at 50%)
- ✅ Smooth probability updates (controlled by b)
- ✅ Early believer reward (but bounded)
- ✅ No pool initialization needed
- ✅ Linear formula (simpler than LMSR)
- ⚠️ One parameter (b) - unavoidable for any robust market

**This is the minimal robust mathematical model for on-chain prediction markets.**

## Next steps
Fixing irreversible trades / proper two-way market. The model is currently one-directional (only deposits into outcomes).

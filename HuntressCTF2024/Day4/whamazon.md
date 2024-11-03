## Whamazon
Within the UI, we are told we have $50 and a flag costs $1000000000. This is an example where simplicity got the best of me. I immediately got burpsuite out, didn't intercept anything useful. Long story short, once I asked myself "is there a way to *sell* stuff and make more money?" it occurred to me.

*Selling* is the opposite of *purchasing*. So try it out, enter a (-) number of items to buy from the affordable options and you will see that you ***MAKE*** money.
```python
The 'Video Games' item costs 30 dollars.
How many of the 'Video Games' items would you like ?
> -999999999999
Crunching the numbers...
  30 dollars x -999999999999 = -29999999999970 subtracted from your wallet!

!! You have: 30000000000020 dollars in your wallet !!
```
And now you are no longer a peasant.
Purchase the flag and you will encounter a rock/paper/scissors game, but you will find it never guesses rock. It also always chooses paper as well, so the correct answer is **scissors**. 
Once you defeat this mighty foe, the flag is added to your inventory. Back out and view it.
`flag{18bdd83cee5690321bb14c70465d3408}`

# Design Exercise

### Prompt
Per project specs, there is no vote delegation. This means for someone's vote to count, they must manually participate every time. How would you design your contract to allow for non-transitive vote delegation?

What are some problems with implementing transitive vote delegation on-chain? (Transitive means: If A delegates to B, and B delegates to C, then C gains voting power from both A and B, while B has no voting power).

### Answer
I would have a struct that contains details for each DAO member. In particular, I would have a property like votePower, which would store the cumulative weight of voting power in terms of votes that were delegated to that DAO member. For example, if I am a DAO member, and 2 other members of the DAO had delegated their votes to me, then my votePower value would be 2. In addition to my vote, the cumulative weight of my vote would be 3. That way, when I vote on a proposal, my vote would be the equivalent of 3 votes instead of one vote. In addition, DAO members who delegate their votes would need to be recorded as having already voted.

Ultimately, a decentralized voting system is about providing power to an individual in terms of voting responsibilities. When a transitive vote delegation occurs, you cannot guarantee that the vote-holder has the best interests of the delegateees at heart. The vote-holder may have the immediate delegatees' interests in mind, but it is less and less likely the further down the chain of delegation (for example, if a vote has been delegated from A -> B -> C -> D, it's unlikely that voter D understands voter A's viewpoints clearly and succintly). At this point, you're giving more weight to the delegated voter's viewpoints, and thereby giving "whale" capabilities to that voter.

### Evaluation:

1. This is a nice approach to the problem, however, keep in mind undelegation as well (elaborating on the next point)

2. That's a fair flaw on transitive delegation, but on a more technical level, it's absolutely problematic because undelegation logic can get really tricky: Imagine A delegates to B, and then B delegates to C, then C delegates to A again. What if A wants to undo their delegation to C?
    We'd need a loop to keep track of all the delegation history, which at the time of undoing could become really expensive if there has been many delegations


# Issues

**[H-1]** Anyone can create a proposal

`propose` doesn't check if the caller is part of the DAO

**[H-2]** Anyone can vote

Neither `vote()`, `voteBySig()` or `_vote()` check if the caller is part of the DAO

**[H-3]** A proposal doesn't need to pass to be executed

`execute()` checks if above 25% voted, but doesn't check for the amount yes/no/abstain votes, it just executes it

**[H-4]** Reentrancy vulnerability in `execute`

You seem to have tried to implement the reentrancy guard pattern here with the `_executing` variable, but the function doesn't check for this variable's value, so it's doing nothing and allows for a reentrancy to happen

**[H-5]** Reentrancy vulnerability in `buyNft`

Anyone can call this function (even non-members) and pass any address to the first parameter. Here the contract checks for the `_executing` value that's changing on `execute` but doesn't apply on this scope

**[L-1]** Proposer can vote on their own proposals

This could be undesirable with small membership counts

**[L-2]** `_updateQuorum` gets called before `member`'s length changes

**[Q-1]** Remove hardhat's console import on production

**[Q-2]** `hashProposal` could be private

# Score

| Reason | Score |
|-|-|
| Late                       | - |
| Unfinished features        | - |
| Extra features             | - |
| Vulnerability              |17 |
| Unanswered design exercise | - |
| Insufficient tests         | - |
| Technical mistake          | - |

Total: 17

Good effort!

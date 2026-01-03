

# A malicious reputer can remove all his stake just by queueing a fraction of it for removal

### Summary
A reputer can remove all his stake just by queueing a fraction of it for removal just once.

### Root Cause
When a reputer queues their stake for removal, their stakeRemovalsByBlock is stored in state with the byBlockKey which is formed using the removalInfo.BlockRemovalCompleted as part of the key as shown

```js
File: msg_server_stake.go  
070: func (ms msgServer) RemoveStake(ctx context.Context, msg *types.RemoveStakeRequest) (_ *types.RemoveStakeResponse, err error) {  
071: 	defer metrics.RecordMetrics("RemoveStake", time.Now(), &err)  
///SNIP  
  
116:@> stakeToRemove := types.StakeRemovalInfo{  
117: 		BlockRemovalStarted:   sdkCtx.BlockHeight(),  
118: 		BlockRemovalCompleted: sdkCtx.BlockHeight() + moduleParams.RemoveStakeDelayWindow,  
119: 		TopicId:               msg.TopicId,  
120: 		Reputer:               msg.Sender,  
121: 		Amount:                msg.Amount,  
122: 	}  
123:   
124: 	// If no errors have occurred and the removal is valid, add the stake removal to the delayed queue  
125:@>	err = ms.k.SetStakeRemoval(ctx, stakeToRemove)  
126: 	return &types.RemoveStakeResponse{}, err  
127: }  
  
  
File: keeper.go  
2550: func (k *Keeper) SetStakeRemoval(ctx context.Context, removalInfo types.StakeRemovalInfo) error {  
2551: 	if err := removalInfo.Validate(); err != nil {  
2552: 		return errorsmod.Wrap(err, "removalInfo is not valid")  
2553: 	}  
2554:@>	byBlockKey := collections.Join3(removalInfo.BlockRemovalCompleted, removalInfo.TopicId, removalInfo.Reputer)  
2555: 	err := k.stakeRemovalsByBlock.Set(ctx, byBlockKey, removalInfo)  

```

However, when stake_removals::RemoveStakes() is called, it can be called with currentBlock (which can be greater than or equal removalInfo.BlockRemovalCompleted of most of the queued stake removals) and then all the queued stake removals prior to and including currentBlock are processed. as shown on L20 below

```js
File: stake_removals.go  
14: func RemoveStakes(  
15: 	sdkCtx sdk.Context,  
16: 	currentBlock int64, // @audit this can be entered mannually as there is no check to confirm the current block is past this delay  
17: 	k emissionskeeper.Keeper,  
18: 	limitToProcess uint64,  
19: ) error {  
20: @>	removals, limitHit, err := k.GetStakeRemovalsUpUntilBlock(sdkCtx, currentBlock, limitToProcess)  
21: 	if err != nil {  
22: 		return errors.Wrapf(err, "Unable to get stake removals for block %d", currentBlock)  
23: 	}  
  
/// SNIP  
31: 	}  
32: 	for _, stakeRemoval := range removals {  
33: 		// attempt writes in a cache context, only write finally if there are no errors  
34: 		cacheSdkCtx, write := sdkCtx.CacheContext()  
35:   
36: 		// Update the stake data structures  
37: 	@>	err = k.RemoveReputerStake(  
38: 			cacheSdkCtx,  
39: 	@>		currentBlock,  
40: 			stakeRemoval.TopicId,  
41: 			stakeRemoval.Reputer,   
42: 			stakeRemoval.Amount,  
43: 		)  
44: 		if err != nil {  
45: 			sdkCtx.Logger().Error(fmt.Sprintf(  
46: 				"Error removing stake data structures: %v | %v",  
47: 				stakeRemoval,  
48: 				err,  
49: 			))  
50: 	@>		continue  
51: 		}  

```

As seen on L37 above, there is an attempt to remove the stake removal information from queue using RemoveReputerStake(...). A closer look at the RemoveReputerStake(...) function shows that it calls DeleteStakeRemoval()

```js
File: keeper.go  
2124: // Removes stake from the system for a given topic and reputer  
2125: // subtracts from: totalStake, topicStake, stakeReputerAuthority  
2126: func (k *Keeper) RemoveReputerStake(  
2127: 	ctx context.Context,  
2128: 	blockHeight BlockHeight,  
2129: 	topicId TopicId,  
2130: 	reputer ActorId,  
2131: 	stakeToRemove cosmosMath.Int) error {  
//////SNIP.......  
2185:   
2186: 	// remove stake withdrawal information  
2187: @>	err = k.DeleteStakeRemoval(ctx, blockHeight, topicId, reputer)  
2188: 	if err != nil {  
2189: 		return errorsmod.Wrapf(err, "Deleting stake removal from queue failed")  
2190: 	}  
2191:   
2192: 	return nil  
2193: }  
  
  
2563: // remove a stake removal from the queue  
2564: func (k *Keeper) DeleteStakeRemoval(  
2565: 	ctx context.Context,  
2566: 	blockHeight BlockHeight,  
2567: 	topicId TopicId,  
2568: 	address ActorId,  
2569: ) error {  
2570: @>	byBlockKey := collections.Join3(blockHeight, topicId, address)  
2571: @>	has, err := k.stakeRemovalsByBlock.Has(ctx, byBlockKey)  
2572: 	if err != nil {  
2573: 		return errorsmod.Wrap(err, "error checking if stake removal by block exists")  
2574: 	}  
2575: @>	if !has {  
2576: 		return types.ErrStakeRemovalNotFound  
2577: 	}  
```

The problem is that, when k.RemoveReputerStake() is called above it is called with currentBlock instead of removalInfo.BlockRemovalCompleted and as such if removalInfo.BlockRemovalCompleted is not equal to currentBlock, then stakeRemovalsByBlock.Has() will return has = false thus allowing the reputer to call stake_removals::RemoveStakes() more than one with the same currentBlock (or a future block number) and reduce their stake further because if k.RemoveReputerStake() returns an error the execution flow continues on L50 above

https://github.com/sherlock-audit/2025-01-allora-update/blob/main/allora-chain/x/emissions/module/stake_removals.go#L37-L50

https://github.com/sherlock-audit/2025-01-allora-update/blob/main/allora-chain/x/emissions/keeper/keeper.go#L2570-L2577

Internal Pre-conditions
NIL

External Pre-conditions
NIL

### Attack Path
removalInfo.BlockRemovalCompleted = 20
Alice and Bob are reputers R1 and R2 with stakes 10000 coins each
delegators stake 8000 coins each to both reputers
Bob queues 1000 coins of her stake at B1 and Alice queues 2000 coins of his stake with C = B1 + 10
removalInfo.BlockRemovalCompleted elapses and
Alice calls stake_removals::RemoveStakes() with currentBlock = C + removalInfo.BlockRemovalCompleted + 100
Alice and Bob gets 2000 coins each
but Alice k.stakeRemovalsByBlock could not be deleted because currentBlock != C + removalInfo.BlockRemovalCompleted and as such no value was found stored in k.stakeRemovalsByBlock for the key formed using currentBlock instead of B1 + removalInfo.BlockRemovalCompleted
Alice can keep calling stake_removals::RemoveStakes() with currentBlock = C + removalInfo.BlockRemovalCompleted until her stake is reduced to zero thus having only delegators stake left in the topic
regarding Bob who is does not know about this exploit, his stake will be further reduced when EndBlocker() is called , futher reducing the stake of his topic in that block and here the deletion is finally done (and this could possibly bring the weight of his topic below the minimum threshold preventing the topic from earning rewards for its workers and reputers)
### Impact
This breaks core protocol functionality also, this can be used to DOS a topic preventing it from earning rewards as explained in the last point of the Attack path.

### Mitigation
Modify the stake_removals::RemoveStakes(...) function as shown below, so that at the point of removing the stake, the queued record is cleared for the reputer

```diff
File: stake_removals.go  
14: func RemoveStakes(  
15: 	sdkCtx sdk.Context,  
16: 	currentBlock int64,   
17: 	k emissionskeeper.Keeper,  
18: 	limitToProcess uint64,  
19: ) error {  
20:  	removals, limitHit, err := k.GetStakeRemovalsUpUntilBlock(sdkCtx, currentBlock, limitToProcess)  
21: 	if err != nil {  
22: 		return errors.Wrapf(err, "Unable to get stake removals for block %d", currentBlock)  
23: 	}  
  
/// SNIP  
31: 	}  
32: 	for _, stakeRemoval := range removals {  
33: 		// attempt writes in a cache context, only write finally if there are no errors  
34: 		cacheSdkCtx, write := sdkCtx.CacheContext()  
35:   
36: 		// Update the stake data structures  
37: 	  	err = k.RemoveReputerStake(  
38: 			cacheSdkCtx,  
-39: 		        currentBlock,  
+39: 		        stakeRemoval.BlockRemovalCompleted,  
40: 			stakeRemoval.TopicId,  
41: 			stakeRemoval.Reputer,   
42: 			stakeRemoval.Amount,  
43: 		)  

```

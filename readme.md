# Stake Wars III troubleshooting notes
*This is a collection of random troubleshooting items and commands that have come up on our own server and helping others.*

## Blocks & chunks are not being produced
Get the staking pool key

```near view <POOL_ID> get_staking_key '{}'```

Get the key in validator_key.json

```cat ~/.near/validator_key.json | grep public_key```

Both keys must match. If they do not update the staking pool key and wait 2 epochs before pinging.

If you need to update the staking pool key:

```near call <POOL_ID> update_staking_key '{"stake_public_key": "<PUBLIC_KEY>"}' --accountId <ACCOUNT_ID>```

*Also check your setup if you're still not producing blocks/chunks, if your connection is too slow or the resources aren't good enough it'll miss production.*

## Check Blocks made vs. Expected
```curl -s -d '{"jsonrpc": "2.0", "method": "validators", "id": "dontcare", "params": [null]}' -H 'Content-Type: application/json' http://localhost:3030/ | jq -c ".result.current_validators[] | select(.account_id | contains (\"POOL ID\"))"```

## Why did I get kicked?
```curl -s -d '{"jsonrpc": "2.0", "method": "validators", "id": "dontcare", "params": [null]}' -H 'Content-Type: application/json' https://rpc.shardnet.near.org | jq -c ".result.prev_epoch_kickout[] | select(.account_id | contains (\"POOLID\"))" | jq .reason```

## Make sure cron is set for ping each epoch (epochs are short on shardnet)
```NEAR_ENV=shardnet
0 * * * * near call POOLID.factory.shardnet.near ping '{}' --accountId ACCOUNTID.shardnet.near --gas=300000000000000 >> /root/output.txt
```

## Misc Commands
### Ping the pool
```near call POOLID.factory.shardnet.near ping '{}' --accountId ACCOUNTID.shardnet.near --gas=300000000000000```

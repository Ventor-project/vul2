# Compatibility Mode - Emulation

## The problem
The Vlc asset platform could have an uphill climb getting adoption because of its incompatibility with the existing infrastructure.  Most exchanges, and crypto merchant acceptance systems are configured to handle coins (tokens) that work like Bitcoin (RPC to bitcoind) or Ethereum (IPC to geth using web3).

Direct use of the RPC transfer call for moving assets has been possible from the day that assets launched on Vlc - November 5, 2018.  But expecting each and every exchange to modify their infrastructure to handle assets with different RPC calls might slow down Vlc asset adoption.

## The solution - Compatibility Mode - Emulation
The solution is to speed up adoption is to run vlcd in compatibility mode that emulates the RPC capabilities of vlcd, for an asset instead of vlc.  Exchanges that have already added vlc, can add asset exchange quickly an easily using emulation for every asset they'd like to add.

As an example, normally the rpc call for transferring vlc is   
```sendfrom "fromaccount" "toaddress" amount ( minconf "comment" "comment_to" )```   
Ordinarily this call to vlcd (port 8766) will transfer vlc

Configuring a different port for sending the TRONCO token will allow the same RPC call to transfer TRONCO instead of vlc.  The advantage is that the only change needed for compatibility with a system that can already handle BTC and vlc is to use the port configured for TRONCO, or whatever asset you'd like.

### Configuration
Every Bitcoin-type, or Vlc-type coin uses a different RPC port.  For example, the standard RPC port for Bitcoin is 8332, and the standard RPC port for Vlc is 8766.

An exchange can configure multiple Vlc assets by selecting a different port for each asset.  Once configured, vlcd will emulate a vlcd daemon with RPC calls that send the specified asset, instead of vlc.

Configuration is done in ```vlc.conf```  
```emulate=TRONCO:8888,FREE_HUGS:8889,MAIN/SUB:8890```

Each emulated asset must have its own port that is available on the machine.

The RPC username and password will use the same one as configured for Vlc.

### Detection of Emulation in Compatibility Mode
An additional field is added to getblockchainfo for information. 

```"emulate":"TRONCO:8888"```

This will be absent if not running in compatibility mode.

### Supported RPC Calls

Most of the Vlc RPC calls do not interact with assets.  These can be straight pass-through.  Only those that deal with transfers, and balances of vlc are switched to work with the specified asset.  
```getbalance ( "account" minconf include_watchonly )```  
```sendfrom "fromaccount" "toaddress" amount ( minconf "comment" "comment_to" )```    
```sendmany "fromaccount" {"address":amount,...} ( minconf "comment" ["address",...] replaceable conf_target "estimate_mode")```  
```sendtoaddress "address" amount ( "comment" "comment_to" subtractfeefromamount replaceable conf_target "estimate_mode")```  
```move "fromaccount" "toaccount" amount ( minconf "comment" )```  
```listtransactions ( "account" count skip include_watchonly)```  
```listunspent ( minconf maxconf  ["addresses",...] [include_unsafe] [query_options])```  
```getreceivedbyaddress "address" ( minconf )```  
```getunconfirmedbalance```  



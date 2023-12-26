# BlurSwap

BlurSwap contract: 0x39da41747a83aee658334415666f3ef92dd0d541

tools:
decompiler: https://ethervm.io/decompile

called functions 

```solidity
function batchBuyWithETH(
        MarketRegistry.TradeDetails[] memory tradeDetails
    ) payable external nonReentrant {
        // execute trades
        _trade(tradeDetails);

        // return remaining ETH (if any)
        assembly {
            if gt(selfbalance(), 0) {
                let callStatus := call(
                    gas(),
                    caller(),
                    selfbalance(),
                    0,
                    0,
                    0,
                    0
                )
            }
        }
    }
```

```solidity
function batchBuyWithERC20s(
        ERC20Details memory erc20Details,
        MarketRegistry.TradeDetails[] memory tradeDetails,
        ConverstionDetails[] memory converstionDetails,
        address[] memory dustTokens
    ) payable external nonReentrant {
        // transfer ERC20 tokens from the sender to this contract
        for (uint256 i = 0; i < erc20Details.tokenAddrs.length; i++) {
            erc20Details.tokenAddrs[i].call(abi.encodeWithSelector(0x23b872dd, msg.sender, address(this), erc20Details.amounts[i]));
        }

        // Convert any assets if needed
        _conversionHelper(converstionDetails);

        // execute trades
        _trade(tradeDetails);

        // return dust tokens (if any)
        _returnDust(dustTokens);
    }
```
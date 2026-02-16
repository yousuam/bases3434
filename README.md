# bases3434from web3 import Web3

RPC_URL = "https://mainnet.base.org"
CHAIN_ID = 8453

# Uniswap V3 Factory (same address across many chains)
UNISWAP_V3_FACTORY = Web3.to_checksum_address("0x1F98431c8aD98523631AE4a59f267346ea31F984")

FACTORY_ABI_MIN = [
    {
        "name": "getPool",
        "type": "function",
        "stateMutability": "view",
        "inputs": [
            {"name": "tokenA", "type": "address"},
            {"name": "tokenB", "type": "address"},
            {"name": "fee", "type": "uint24"},
        ],
        "outputs": [{"name": "pool", "type": "address"}],
    }
]


def main():
    w3 = Web3(Web3.HTTPProvider(RPC_URL))
    if not w3.is_connected():
        raise RuntimeError("❌ Cannot connect to Base RPC")

    print("✅ Connected to Base")
    print("Chain ID:", CHAIN_ID)

    # --- EDIT THESE ---
    # Example: WETH + USDC on Base
    tokenA = Web3.to_checksum_address("0x4200000000000000000000000000000000000006")  # WETH
    tokenB = Web3.to_checksum_address("0x833589fCD6eDb6E08f4c7C32D4f71b54bdA02913")  # USDC
    fee = 500  # 500=0.05%, 3000=0.3%, 10000=1%
    # ------------------

    factory = w3.eth.contract(address=UNISWAP_V3_FACTORY, abi=FACTORY_ABI_MIN)

    pool = factory.functions.getPool(tokenA, tokenB, fee).call()

    print("\nTokenA:", tokenA)
    print("TokenB:", tokenB)
    print("Fee   :", fee)

    if pool == "0x0000000000000000000000000000000000000000":
        print("\n❌ No pool found for this pair + fee")
    else:
        print("\n✅ Pool address:", Web3.to_checksum_address(pool))


if __name__ == "__main__":
    main()

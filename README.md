# safe-configs

This repository is used for the automated importing of blockchain configurations into the Safe Config Service. It includes scripts and configuration files necessary to import chains, features, wallets, and safe apps.

## Description

The `safe-configs` repository contains JSON configuration files and a Django management command script to import blockchain configurations into the Safe Config Service. This service is essential for managing and maintaining blockchain network configurations, including chain details, features, wallets, and safe apps.

## Adding a New Chain

To add a new chain to this repository and import it into the Safe Config Service, follow these steps:

### 1. Update `configs/chains.json`

Add the new chain's configuration to the `configs/chains.json` file. Ensure the configuration includes all necessary fields. Here is an example of a chain configuration:

```json
{
  "chainId": "12345",
  "chainName": "NewChain",
  "description": "NewChain Mainnet",
  "chainLogoUri": "https://example.com/chains/12345/chain_logo.png",
  "l2": false,
  "isTestnet": false,
  "nativeCurrency": {
    "name": "NewCoin",
    "symbol": "NC",
    "decimals": 18,
    "logoUri": "https://example.com/chains/12345/currency_logo.png"
  },
  "transactionService": "https://newchain-tx.example.com",
  "blockExplorerUriTemplate": {
    "address": "https://newchainexplorer.com/address/{{address}}",
    "txHash": "https://newchainexplorer.com/tx/{{txHash}}",
    "api": "https://api.newchainexplorer.com/api?module={{module}}&action={{action}}&address={{address}}&apiKey={{apiKey}}"
  },
  "ensRegistryAddress": null,
  "features": [
    "CONTRACT_INTERACTION",
    "ERC1155",
    "ERC721"
  ],
  "gasPrice": [],
  "publicRpcUri": {
    "authentication": "NO_AUTHENTICATION",
    "value": "https://rpc.newchain.com"
  },
  "rpcUri": {
    "authentication": "NO_AUTHENTICATION",
    "value": "https://rpc.newchain.com"
  },
  "safeAppsRpcUri": {
    "authentication": "NO_AUTHENTICATION",
    "value": "https://rpc.newchain.com"
  },
  "shortName": "newchain"
}
```

Alternatiely, blockExplorer and RPC can be provided simply as a URL, in which case the script will attempt to autodetect the rest of the data, example:

```json
{
  "chainId": "12345",
...
  "rpcUri": "https://rpc.newchain.com",
  "blockExplorer": "https://explorer.newchain.com/"
}
```

### 2. Update `.env` of safe-config-service

Ensure the `.env` file in your configuration service is set up correctly to import the new chains. Here are the relevant environment variables:

```env
CONFIG_URL=https://raw.githubusercontent.com/protofire/safe-configs/refs/heads/main/
DEFAULT_CHAIN_IDS=1,10,5000
IMPORT_FEATURES=1
IMPORT_WALLETS=1
IMPORT_SAFE_APPS=1
```

- `CONFIG_URL`: Url to the configuration files.
- `DEFAULT_CHAIN_IDS`: Set to `ALL` to import all chains listed in `chains.json`. Alternatively, specify a comma-separated list of chain IDs to import specific chains.
- `IMPORT_FEATURES`: Set to `1` to import features.
- `IMPORT_WALLETS`: Set to `1` to import wallets.
- `IMPORT_SAFE_APPS`: Set to `1` to import safe apps.

### 3. Run the Import Script (should be executed during during deployment)

Execute the Django management command to import the configurations:

```bash
python manage.py import_default_config
```

This command will read the configuration files and import the chains, features, wallets, and safe apps into the Safe Config Service.

## Detailed Guide

### Import Script Overview

The import script is located at `.internal/import_default_config.py`. It handles the loading and importing of JSON data for chains, features, wallets, and safe apps.

#### Key Functions

- `load_json_data`: Loads JSON data from a file or URL.
- `handle`: Main function that orchestrates the import process.
- `import_features`: Imports features from `features.json`.
- `import_wallets`: Imports wallets from `wallets.json`.
- `import_safe_apps`: Imports safe apps from `safeApps.json`.
- `import_chains`: Imports chains from `chains.json`.

### Adding Default Wallets and Features

The script automatically adds default wallets and features to each chain. The default wallets and features are defined within the `import_chains` function.

#### Default Wallets

- Metamask
- Ledger
- Trezor
- WalletConnect_v2

#### Default Features

- EIP1271
- COUNTERFACTUAL
- DELETE_TX
- SAFE_141
- SAFE_APPS
- SAFE_TX_GAS_OPTIONAL
- SPEED_UP_TX

### Handling Icons

The script also handles the uploading of chain and currency logos. Ensure the URLs provided in the chain configuration are accessible and return valid image files.

## Conclusion

By following the steps outlined in this README, you can add new chains to the `safe-configs` repository and they will be automatically imported to the Safe Config Service. Ensure all configurations are correctly set up and the import script is executed to reflect the changes.
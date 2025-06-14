# <img src="logo.png" alt="Symmetric" height="128px">

# Symmetric V4 Monorepo

This repository contains the Symmetric Protocol V4 core smart contracts, including the `Vault` and standard Pools, along with their tests.

## Structure

This is a Yarn monorepo, with the packages meant to be published in the [`pkg`](./pkg) directory. Newly developed packages may not be published yet.

Active development occurs in this repository, which means some contracts in it might not be production-ready. Proceed with caution.

### Packages

- [`v4-interfaces`](./pkg/interfaces): Solidity interfaces for all contracts.
- [`v4-solidity-utils`](./pkg/solidity-utils): miscellaneous Solidity helpers and utilities used in many different contracts.
- [`v4-pool-hooks`](./pkg/pool-hooks/): hook examples to illustrate potential capabilities and how to build one.
- [`v4-pool-utils`](./pkg/pool-utils/): Solidity utilities used to develop Pool contracts.
- [`v4-pool-stable`](./pkg/pool-stable/): contains [`StablePool`](./pkg/pool-stable/contracts/StablePool.sol), along with its associated factory.
- [`v4-pool-weighted`](./pkg/pool-weighted): contains [`WeightedPool`](./pkg/pool-weighted/contracts/WeightedPool.sol), along with its associated factory.
- [`v4-vault`](./pkg/vault): contains the main [`Vault`](./pkg/vault/contracts/Vault.sol) contract, which is the cornerstone of Symmetric V4, and its extensions. Also includes the standard [`Router`](./pkg/vault/contracts/Router.sol) and [`BatchRouter`](./pkg/vault/contracts/BatchRouter.sol), which supports end-user interactions with the Vault.

## Pre-requisites

The build & test instructions below should work out of the box with Node 18. More specifically, it is recommended to use the LTS version 18.15.0; Node 19 and higher are not supported. Node 18.16.0 has a [known issue](https://github.com/NomicFoundation/hardhat/issues/3877) that makes the build flaky.

Multiple Node versions can be installed in the same system, either manually or with a version manager.
One option to quickly select the suggested Node version is using `nvm`, and running:

```bash
$ nvm use
```

Solidity 0.8.24 or higher is required to support the upcoming Cancun hard fork with transient storage. We strongly recommend using the latest released version of the Solidity compiler (at least 0.8.24), to incorporate all the latest bug fixes.

## Build and Test

Before any tests can be run, the repository needs to be prepared:

### First time build

```bash
$ yarn # install all dependencies
```

You will also need to configure your environment variables to point to RPC endpoints in order to run fork tests.
Write your preferred RPC URL to `.env`, and source it. For example:

```bash
$ sed 's,YOUR_MAINNET_RPC_URL,<YOUR_RPC_URL>,g' .env.example > .env
$ source .env
```

### Regular build & test

```bash
$ yarn build # compile all contracts
```

Most tests are standalone and simply require installation of dependencies and compilation.

In order to run all tests (including those with extra dependencies), configure your RPC endpoints by sourcing your `.env` file and run:

```bash
$ yarn test # run all tests
```

To instead run a single package's tests, run:

```bash
$ cd pkg/<package> # e.g. cd pkg/vault
$ yarn test
```

You can see a sample report of a test run [here](./audits/test-report.md).

#### Foundry (Forge) tests

To instead run a single package's forge tests, run:

```bash
$ cd pkg/<package> # e.g. cd pkg/vault
$ yarn test:forge
```

#### Hardhat tests

To instead run a single package's hardhat tests, run:

```bash
$ cd pkg/<package> # e.g. cd pkg/vault
$ yarn test:hardhat
```

Hardhat tests will also update snapshots for bytecode size and gas usage if applicable for a given package.

### Medusa tests

Medusa is a tool, developed by Trail of Bits, that allow us to execute stateful fuzz tests in contracts, in a way
smarter than Forge. That's because Medusa has an optimizer to create a path of transactions which is not completely
random.

To run Medusa tests, we first need to install Echidna. To do so, install `echidna` using the 
[release page](https://github.com/crytic/echidna/releases) or `brew install echidna` on a Mac. Notice that the Mac
installation will also install Crytic-compiler, so this step can be skipped when installing Medusa.

Then, install Medusa using this [installation guide](https://github.com/crytic/medusa/blob/master/docs/src/getting_started/installation.md#building-from-source), `brew install medusa` on a Mac. Alternatively, just copy the adequate precompiled binary to a directory within your `PATH`.

Finally, run `yarn test:medusa`. This command is available inside the packages `vault`, `pool-weighted` and 
`pool-stable`.

## Static analysis

To run [Slither](https://github.com/crytic/slither) static analyzer, Python 3.8+ is a requirement.

### Installation in virtual environment

This step will create a Python virtual environment with Slither installed. It only needs to be executed once:

```bash
$ yarn slither-install
```

### Run analyzer

```bash
$ yarn slither
```

The analyzer's global settings can be found in `.slither.config.json`.

Some of the analyzer's known findings are already filtered out using [--triage-mode option](https://github.com/crytic/slither/wiki/Usage#triage-mode); the results of the triage can be found in `slither.db.json` files inside each individual workspace.

To run Slither in triage mode:

```bash
$ yarn slither:triage
```

### Coverage

```bash
$ yarn coverage
```

The `coverage` command generates a coverage report for each package found in the `./package/coverage/index.html` directory. The `coverage.sh` script can generate Forge and Hardhat reports and/or merge them. The Yarn command uses Forge by default as most of the tests are written with it, and it's the most reliable option.

**Note: We suggest adopting [lcov 1.16](https://github.com/linux-test-project/lcov/releases/tag/v1.16) since `forge coverage --report lcov` command works better in this version.**

## Security

> Upgradeability | Not Applicable. The system cannot be upgraded.

## Licensing

Most of the Solidity source code is licensed under the GNU General Public License Version 3 (GPL v3): see [`LICENSE`](./LICENSE).

### Exceptions

- All other files, including tests and the [`pvt`](./pvt) directory are unlicensed.

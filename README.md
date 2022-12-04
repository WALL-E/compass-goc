# Compass Chain Information

Contents

* [Status](#status)
* [Chain Data](#chain-data)

## Status

* Timeline
  * 2022-12-09: Spawn time: 2022-12-09T14:00:00.000000Z 
  * 2022-12-06: Proposal goes into voting period
  * 2022-12-06: Chain initialized

Compass will launch as a consumer chain through a governance proposal in the `provider` chain. Read the [Consumer Chain Start Process](https://github.com/WALL-E/ics-compass/blob/main/Consumer-Chain-Start-Process.md) page for more details about the workflow.

The following items will be included in the proposal:
* Genesis file hash
  * The SHA256 is used to verify against the genesis file that the proposer has made available for review.
  * This "fresh" genesis file cannot be used to run the chain: it must be updated with the CCV states after the spawn time is reached.
* Binary hash
* Spawn time
  * Even if the proposal passes, the CCV states will not be available from the provider chain until after the spawn time is reached.

## Chain Data

### Binary

The binary published in this repo is the `compassd` binary built using the `wangfeiping/compass-goc` repo tag [v0.1.1](https://github.com/wangfeiping/compass-goc/releases/tag/v0.1.1). You can generate the binary following the [Get Started section](https://github.com/wangfeiping/compass-goc#build-and-run-instructions).

  * [Linux amd64 build](compassd)
  * Version: `v0.1.1-f04272d9d91dc792ece9fbadc3671c2c6414ab81`
  * SHA256: `d354255c2f815d3b9289e4afd580d7d9005667393e90ab861cd339e9524ff906`

### Genesis file

**The genesis file for Interchain Security consumer chains must include the CCV (Cross Chain Validation) state generated by the provider chain _after_ the spawn time is reached.**

**Final** genesis file **with CCV state**: **[compass-genesis.json](compass-genesis.json)**
- **SHA256: `pending`**
- Validators must replace their `config/genesis.json` file with this one before running the binary.

The genesis file with was generated using the following settings:

* Chain ID: `compass-goc`
* Denom: `ucompass`
* Signed blocks window: `"8640"`
* Genesis accounts were added to provide funds for a faucet and a relayer that will be run by the testnet coordinators.
* Genesis file **without CCV state**: [`compass-fresh-genesis.json`](compass-fresh-genesis.json), SHA256: `4a498592197d87223eb987cda461243c5eb6826df41c0a641ffe65874f8c3c62`
  * **This is provided only for verification, this is not the genesis file validators should be running their nodes with.**

## Endpoints

* **p2p seeds : `pending`**
* **p2p persistent peers : `pending`**
* These peers represent the `goc-coordinator` and `goc-backup` validators (run by the testnet coordinators). 
* The `goc-backup` validator node will be running on Compass shortly after the genesis file that includes the CCV state (Cross Chain Validation state) has been published.
* The `goc-coordinator` validator node has an overwhelming majority of the voting power, and we aim to start it two hours after the spawn time is reached. 67% of the voting power needs to come online for consumer chains to start. Once the `goc-coordinator` is live, the chain will progress.
* Please keep in mind that any validator that does not come online after 67% of the voting power is up and running, is likely to be slashed for downtime, potentially resulting in being jailed (the `signed_blocks_window` parameter is set to `8640`).

## Join via Bash Script

On the node machine:
- Copy the `node_key.json` and `priv_validator_key.json` files for your validator.
  - **These should be the same ones as the ones from your provider node**.
- Run one of the following scripts:
  - Compass service: [compass-init.sh](compass-init.sh)
  - Cosmovisor service: [compass-init-cv.sh](compass-init-cv.sh)
- Wait until the spawn time is reached and the genesis file with the CCV states is available.
- Overwrite the genesis file with the one that includes the CCV states.
  - The default location is `$HOME/.compass/config/genesis.json`.
- Enable and start the service:
  - Compass
    ```
    sudo systemctl enable compass
    sudo systemctl start compass
    ```
  - Cosmovisor
    ```
    sudo systemctl enable cv-compass
    sudo systemctl start cv-compass
    ```
- To follow the log, use:
  - Compass: `journalctl -fu compass`
  - Cosmovisor: `journalctl -fu cv-compass`
- If the log does not show up right away, run `systemctl restart systemd-journald`.

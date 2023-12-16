# bitcoin-stack

A simple & legible docker-based bitcoin stack for self-hosting.

The stack's main goals are to provide the following:
- a `bitcoind` full node.
- an electrum server via `electrs`.
- provide all of this in an easy-to-audit manner to individuals familiar with the tools used.

## Details
- Ports exposed
  - `50001`: *insecure* electrum rpc port
  - :warning: Note that a reverse proxy is not provided here. I'm assuming that if you're looking at running a bitcoin stack in docker, you're probably the kind of person who has a preferred & secured setup for your networking environment. If not, I would suggest that you look into an option like [Tailscale](https://tailscale.com/), use custom ACLs, and bind the exposed port only to your Tailscale IP.
- Docker images used
  - [`briann/bitcoind`](https://hub.docker.com/repository/docker/briann/bitcoind) ([source](https://github.com/briann/docker-bitcoind))
  - [`briann/electrs`](https://hub.docker.com/repository/docker/briann/electrs) ([source](https://github.com/briann/electrs))
  - ⚠️ At the moment you're trusting these two images. See [future additions](#future-additions) for the task to add some `Dockerfile`s into this repo so this important part is legibile.

## System requirements
- CPU & RAM requirements are pretty modest, though the initial sync will be more taxing. I'm currently running this in a VM with 2 CPUs & 4GB of RAM.
- Storage: As of block 821400, I'm using about 616GB. You probably want at least an SSD.
    ```
    $ du -h --max-depth=1 bitcoin-stack/
    43G     bitcoin-stack/electrs
    574G    bitcoin-stack/bitcoind
    616G    bitcoin-stack/
    ```

## Getting started
1. **Set variables** for data storage location (required) and IP to bind to (optional - default is `0.0.0.0` ⚠️ *this may not be what you want*).
    1. Option A: Setting these as environment variables that will be available to you when running `docker compose up -d`. For example, in `.bashrc`:
        ```bash
        export BITCOIN_STACK_DATA_DIR='/path/to/dir/bitcoin-stack'
        export BITCOIN_STACK_BIND_IP='192.168.1.100'
        ```
    2. Option B: Add these to an `.env` file at the root of this project. `.gitignore` is set to ignore the `.env` file.
        ```
        BITCOIN_STACK_DATA_DIR='/path/to/dir/bitcoin-stack'
        BITCOIN_STACK_BIND_IP='192.168.1.100'
        ```
    3. Option C: Specify the variables at runtime (see below).
1. **Start the stack**
    ```bash
    docker compose up -d
    ```
    If you skipped step 1 and want to specify the variables now, your command will look something like:
    ```bash
    BITCOIN_STACK_DATA_DIR='/path/to/dir/bitcoin-stack' BITCOIN_STACK_BIND_IP='192.168.1.100' docker compose up -d
    ```
    If this is your first time starting the stack, the sync might take a while depending on your internet connection and compute.

## Future additions
In rough-ish order of things to do next:
- open electrum port 50002
- Docker image builds in this repo
- Tor
- mempool UI
- Custom certificate for electrum
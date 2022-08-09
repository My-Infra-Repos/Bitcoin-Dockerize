Bitcoind for Docker
===================
### BitCoin Dockerized: -> What is there
This is a Docker image that runs the Bitcoin -0.21.0 bitcoind node in a container. It carry out some basic test on our image for security concerns. We can run the test ourselves once we create the image. Currently we are exposing only two ports but We can expose multiple ports for Wer testing.
The Image have been verified with gpg checks of bitcoin core itself.
This image contains the main binaries from the Bitcoin Core project - `bitcoind`, `bitcoin-cli` and `bitcoin-tx`. It behaves like a binary, so We can pass any arguments to the image and they will be forwarded to the `bitcoind` binary. We need to open the requisite ports in docker file
### BitCoin Dockerized: -> How to Build the image.
Clone the repository & issue following commands, Note we are implementing CI-CD through GitHub Actions in upcoming publications.
say:
docker build -t bitcoinimg:latest .

### Basic Security tests has been carried our & results are present in test_results file

For testing run below:
-----------------------------

test/run.sh bitcoinimg:latest


Requirements
------------

* Physical machine, cloud instance, or VPS that supports Docker , KVM or XEN based VMs) running Ubuntu 14.04 or later (*not OpenVZ containers!*)
* At least 100 GB to store the block chain files (and always growing!)
* At least 1 GB RAM + 2 GB swap file

For Quick Start
-----------------------

One liner for Ubuntu 14.04 LTS machines with JSON-RPC enabled on localhost and adds upstart init script:

    curl https://raw.githubusercontent.com/kylemanna/docker-bitcoind/master/bootstrap-host.sh | sh -s trusty


How to  Start
-----------

1. We need to Create a `data` volume to persist the bitcoind blockchain data.  It will store the data in case of container reboot. Then we will run below commands
        ```sh
        docker volume create --name=data
        docker run -v data:/bitcoin --name=bitcoin-server -d \
            -p 8333:8333 \
            -p 127.0.0.1:8332:8332 \
            bitcoinimg:latest
        ```sh

2. We can identify the running container 

        $ docker ps
        CONTAINER ID        IMAGE                         COMMAND             CREATED             STATUS              PORTS                                              NAMES
        d0e1076b2dca        bitcoin:latest      "btc_oneshot"       2 seconds ago       Up 1 seconds        127.0.0.1:8332->8332/tcp, 0.0.0.0:8333->8333/tcp   bitcoind-node

3. We can then access the daemon's output with below

        docker logs -f bitcoind-node

### How to interact with the daemon

There are two communications methods to interact with a running Bitcoin Core daemon.

The first one which we have tested is using a cookie-based local authentication. It doesn't require any special authentication information as running a process locally under the same user that was used to launch the Bitcoin Core daemon allows it to read the cookie file previously generated by the daemon for clients. The downside of this method is that it requires local machine access.

The second option is making a remote procedure call using a username and password combination. This has the advantage of not requiring local machine access, but in order to keep Wer credentials safe We should use the newer `rpcauth` authentication mechanism.

#### Using cookie-based local authentication

Start by launch the Bitcoin Core daemon:

```sh
❯ docker run --rm --name bitcoin-server -it ruimarinho/bitcoin-core \
  -printtoconsole \
  -regtest=1
```

Then, inside the running `bitcoin-server` container, locally execute the query to the daemon using `bitcoin-cli`:

```sh
❯ docker exec --user bitcoin bitcoin-server bitcoin-cli -regtest getmininginfo

{
  "blocks": 0,
  "currentblocksize": 0,
  "currentblockweight": 0,
  "currentblocktx": 0,
  "difficulty": 4.656542373906925e-10,
  "errors": "",
  "networkhashps": 0,
  "pooledtx": 0,
  "chain": "regtest"
}




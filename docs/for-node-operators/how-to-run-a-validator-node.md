---
sidebar_position: 3
---

# How to run a validator node

This guide will explain the basic principles on how to run a validator node and how to stake CAPS. A more detailed approach will be available later in the form of a workshop and/or documentation.

The alpha version of the workshop can be found **[here](https://docs.google.com/presentation/d/1rjMtUB-foRfSkruRWzVbFqKS1VRVvqmk4ohxJ_lNYrk/edit#slide=id.p)**. We recommend you to go through the slides since most of the guide is based upon that same workshop.

> _Make sure that you first fully read the guide before you start doing anything._


<iframe width="581" height="327" src="https://www.youtube.com/embed/RhYCDSq0Pv0" title="Ternoa Coffee Break #1 - How to Become a Ternoa Node Operator" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

___

## Getting the binary (node/client)

There are two ways to get the binary: from the github repo or to build it.

The easiest approach is to directly get the binary from the Ternoa chain github **[repo](https://github.com/capsule-corp-ternoa/ternoa-node)**. From the github repo website, click on the Releases link and find the latest release with a binary attached to it.

The second approach is to manually build the client. You can follow the how-to-build-this-repo guide to understand how to do it. My recommendation is if you are going to follow this method, make sure that you have at least a quad core CPU otherwise it will takes ages to build.

Whatever method you choose, make sure that once you have the binary that you place it in the right directory. Usually the place is `usr/bin` or `/opt/ternoa.`

___

## Running the node

The key in correctly running the node is to use the right flags to connect to the right chain. Ternoa is currently running two networks, Ternoa Alphanet and Ternoa Mainnet, and depending if you want to deploy a test validator or a mainnet validator you need to pass either `--chain alphanet` or `--chain mainnet`, respectively.

**Here is an example on what flags you can/should use:**
```bash 
/usr/bin/ternoa --name MyFirstNode --chain alphanet --base-path /opt/node-data --validator --state-cache-size 0 --execution wasm
```

**Let's see what those flags do:**

- name `X` - It sets the name of the validator node. `X` should be something unique.
- chain `X` - `X` can be either alphanet or mainnet. This tells the node which chain specification to use.
- base-path `X` - Defines where the blockchain data will be stored. If have an external disk attached, you that external disk for storing the data.
- validator - This runs the node in validator mode
- state-cache-size 0 - This fixes a bug that substrate has.
- execution wasm - This runs the node in wasm mode. This is also used to fix a bug that is introduced in the substrate native runtime.

We recommend you that you create a systemd service file which will run the node in the background and start it up on every restart. Instructions for that you can find in the linked workshop presentation.

**Once the node is running, it will be visible in the telemetry UI and it will take time to sync up**

>**[Alphanet Telemetry UI](https://telemetry.polkadot.io/#list/0x18bcdb75a0bba577b084878db2dc2546eb21504eaad4b564bb7d47f9d02b6ace)**

>**[Mainnet Telemetry UI](https://telemetry.polkadot.io/#list/0x6859c81ca95ef624c9dfe4dc6e3381c33e5d6509e35e147092bfbc780f777c4e)**

While it's syncing up, you can run the following command to generate the session keys:

```bash 
$ curl -H "Content-Type: application/json" -d '{"id":1, "jsonrpc":"2.0", "method": "author_rotateKeys", "params":[]}' http://localhost:9933 &> session_keys.txt
# The session keys will be stored inside the session_keys.txt file. Let’s printout that file.
$ cat session_keys.txt
```

The sessions keys are stored inside the "result" field. Make sure that you store it somewhere safe since it's going to be used in the next part of this guide.

To make sure that the session keys are properly inserted, stop the node and start it again. This is quite important to do because otherwise the next step in this guide might not work
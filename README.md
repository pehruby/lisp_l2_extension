# Cloud layer 2 extension using LISP

A VIRL lab based on an example described on (https://www.cisco.com/c/en/us/products/collateral/routers/cloud-services-router-1000v-series/white-paper-c11-731872.html)

Another example is [here](https://www.cisco.com/c/en/us/td/docs/routers/csr1000/software/aws/b_csraws/configure-lisp-layer2-extension.html)

The lab expects you are using virlutils to manage your VIRL simulations.

## Setup

Clone the repository

Edit .virlrc according to your VIRL setup

```text
VIRL_HOST=xxxxxx
VIRL_USERNAME=guest
VIRL_PASSWORD=guest
```

Spin up the simulation

```text
virl up
```

cisco is used as an username and password to all devices

## Lab schema

![alt text](../blob/master/doc/schema.png "Topology")


## Useful commands


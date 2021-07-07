# Introduction

Python multi-thread wrapper for fping

This script was developed to support IoT device monitoring using ICMP(ping). The performance is much better than single fping however there is bottleneck somehow. See performance benchmark

# Installation

fping
python3


# Start

Generate a IP list file for sample "test.csv"
Generate a "test.yml" and adjust configuration according to your need

python3 pyfping -c test.yaml

# Performance benchmark


## Environment

ESXi with different "Linux" guest OS . The server has 40 sockets and 80 logic CPUs and ~100G mem and 10G NIC connected to Internet core router directly. 

## Initial result

Using 1 VM , I'm able to ping ~200K targets using "-c5 -i4 -b12 -t500 -r1" around 60 second
Threads (python) = 72
ping bucket (per fping) = 500

## Some observations

* CPU ( 8x vCPU )is kind of busy but OK. I tried more vCPU up to 32 and it doesn't make difference

* Same setup if I double threads or increase "number of target per FPING". it will run "faster" however I notice rtt increase and packetloss become unreliable

* Ubuntu 21 and Centos 8 . They have similar performance

* Linux "Alpine". It's much slower with same setup.

* If fping interval (-i) is changed to 2ms, it's faster but result (rtt/packetloss) become unreliable

* if fping interval (-i) is changed to 10ms, it will slow down each round (200K) to ~90 seconds

* I looked around Linux kernel and adjusted few ICMP/socket parameters . No luck

* tried multiple process as well. It's similar result as multi-thread

* I tried few python native ICMP packages and none of them is reliable/faster comparing to "fping"

Basically I can do 200K targets in 60s per VM. PPS = 400,000 * 5 / 60 ~ 16K PPS . The network traffic is max at ~8Mbps with stable result. With 3 x VM , I can do ~600K targets. It's quite amazing but like to understand if more PPS can be pushed per VM


# Credits

## FPING

Original author: Roland Schemers (schemers@stanford.edu)
Previous maintainer: RL "Bob" Morgan (morgan@stanford.edu)
https://github.com/schweikert/fping

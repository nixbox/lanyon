---
layout: post
title: Solo (CPU) Mining
---

This is a follow up to my [previous post](http://bigendian.io/2017/08/06/litecoin-testnet/) which discussed how to download and compile Litecoin code and then run the Litecoin daemon (*litecoind*).

In order to experiment on the Litecoin testnet, we need testnet Litecoins. One way to get these coins is to try and mine them. In the context of cryptocurrencies like Bitcoin and Litecoin, mining is the process by which a [total ordering](https://en.wikipedia.org/wiki/Total_order) of blocks (containing transactions) is established by the network. Miners are network participants that periodically compete to solve a computationally hard problem. When a miner "wins", specifically, by solving the aforementioned problem, they get a reward. This process is the only way to generate new coins in Bitcoin and Litecoin. In a later post, I will expound on the computational problem that miners attempt to solve.

Today, on the mainnet, miners use ASIC-based machines for mining. For our purposes, since we will be using the testnet to learn more about the internals of Litecoin, we would make use of a CPU-based miner.

## Download and compile minerd

{% highlight shell %}
$ git clone https://github.com/pooler/cpuminer
$ cd cpuminer
$ ./autogen.sh
$ mkdir $HOME/cpuminer
$ ./configure --prefix=$HOME/cpuminer
$ make -j8
$ make install
{% endhighlight %}

## Generating an address

In order to receive mining rewards, you need to specify an address. When minerd successfully mines a block, the reward amount which includes the newly generated coins and the fees for all the transactions in the new block will be paid to the address that the miner specified. This is known as the *coinbase* address. You can generate a new address as follows:

{% highlight shell %}
$ cd $HOME/litecoin/bin
$ ./litecoin-cli getnewaddress
mx555Wkr5Wh59J8gbycEDzuk2aEtKtrRTh
{% endhighlight %}

## Run minerd
{% highlight shell %}
$ cd $HOME/cpuminer/bin
$ ./minerd -a scrypt -o http://127.0.0.1:19332 -u myusername -p mypassword -t 20 -D --no-stratum  --coinbase-addr=mx555Wkr5Wh59J8gbycEDzuk2aEtKtrRTh
{% endhighlight %}

The username and password in the above command should be the same as the one configured in the litecoind configuration file (*litecoin.conf*). The "-t" switch specifies how many number of threads to spawn for computing the hashes. Also, notice that "-a" specifies the hashing algorithm to use. Since Litecoin uses scrypt, the choice is obvious.

Once you start minerd, it will start the mining process, some time later, when it successfully mines a block, you will see something like:

{% highlight shell %}
[2017-08-07 21:23:17] DEBUG: hash <= target
Hash:   00000389676cb01126bf970464167205cfaa758ae3f919791c8b880a5144ae9d
Target: 000003ffff000000000000000000000000000000000000000000000000000000
[2017-08-07 21:23:17] thread 19: 71520 hashes, 3.07 khash/s
[2017-08-07 21:23:17] LONGPOLL pushed new work
[2017-08-07 21:23:17] accepted: 2/2 (100.00%), 71.91 khash/s (yay!!!)
{% endhighlight %}

Once you see output like the above, the coinbase address will be sent the reward amount of coins. Since this amount will be paid using a transaction, you can list the recent transactions using:

{% highlight shell %}
$ ./litecoin-cli listtransactions
[
  {
    "account": "",
    "address": "mx555Wkr5Wh59J8gbycEDzuk2aEtKtrRTh",
    "category": "immature",
    "amount": 50.00000000,
    "label": "",
    "vout": 0,
    "confirmations": 1082,
    "generated": true,
    "blockhash": "5e6190c21f73c290c12f7aec8ceb1321252aafbf0858b34b5f458a1a92a08447",
    "blockindex": 0,
    "blocktime": 1502166579,
    "txid": "c4cfedcd2962772513998ddbb1795c7d4bcec2dff89002cfde78021e584a171b",
    "walletconflicts": [
    ],
    "time": 1502166579,
    "timereceived": 1502166582,
    "bip125-replaceable": "no"
   }
  ]
{% endhighlight %}

Notice the category in the above output is *immature*. This means that the reward is not available to spend yet and will only become eligible to spend after a certain number of blocks have been mined after the block that generated this reward.

If you execute the same command at a later time, you will see that the category would have changed to *generate*. Then, you can verify that you have the reward amount by checking your current balance.

{% highlight shell %}
$ ./litecoin-cli getbalance ""
50.00000000
{% endhighlight %}

There you have it! You just got some shiny new testnet Litecoins :)

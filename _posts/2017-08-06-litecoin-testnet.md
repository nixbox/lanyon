---
layout: post
title: Litecoin testnet
---

Digital currencies ka cryptocurrencies have been gaining a lot traction recently. After the multi-year surge of [Bitcoin](https://bitcoin.org/en/) and more recently [Ethereum](https://www.ethereum.org/), there are now [hundreds](https://coinmarketcap.com/currencies/views/all/) of digital currencies.

[Litecoin](https://litecoin.org/) is an [altcoin](https://en.bitcoin.it/wiki/Altcoin) that was created by [Charlie Lee](https://twitter.com/satoshilite). Some of the main differences compared to Bitcoin include:
- Four times as many coins as Bitcoin (21,000,000 * 4 = 84,000,000)
- Four times less average block creation time (600 / 4 = 150 seconds)
- Hash function (scrypt)[https://en.wikipedia.org/wiki/Scrypt#Introduction] vs. [SHA-256](https://en.bitcoin.it/wiki/SHA-256)

In addition to the above, Litecoin has been pretty much one step ahead of Bitcoin for some time. For instance, Litecoin started using [Segregated Witness (SegWit)](http://www.investopedia.com/terms/s/segwit-segregated-witness.asp) [earlier](https://www.coindesk.com/litecoin-successfully-activates-long-debated-segwit-upgrade/) this year. SegWit is a technology that among other things enables much better scaling of the Bitcoin/Litecoin network.

To some extent, I view Litecoin as the testing ground for upcoming technologies in Bitcoin. If you have not been following the recent happenings in Bitcoin, it would be a very interesting read to understand why it took so long for Bitcoin to adopt SegWit.

Personally, I see huge potential in cryptocurrencies, the reasons for which will become apparent through my posts. Due to that reason, I want to better understand the underlying technology mainly to appease my curiosity and be aware of what is going on in this space.

In this post, I will detail, how you can get started with setting up a Litecoin testnet node. Briefly, testnet is a Litecoin network that is separate from the mainnet and is used for development/testing.

If you want to learn about the fundamental concepts of Bitcoin, please read the excellent book by Andreas Antonopolous, [Mastering Bitcoin: Programming the Open Blockchain](https://www.amazon.com/gp/product/1491954388/ref=as_li_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=1491954388&linkCode=as2&tag=bigendian08-20&linkId=ad35132b2cfa17f5434903a4b632da68)

## Getting Litecoin

Download source code for Litecoin
{% highlight shell %}
$ git clone https://github.com/litecoin-project/litecoin.git
{% endhighlight %}

### Compile the code
{% highlight shell %}
$ cd litecoin
$ ./autogen.sh
$ mkdir $HOME/litecoin
$ ./configure --prefix=$HOME/litecoin/
$ make -j8
{% endhighlight %}

### Install the binaries
{% highlight shell %}
$ make install
{% endhighlight %}

The above will install the binaries under the *prefix* directory specified above (*$HOME/litecoin/*). If you want, you can add that directory to your PATH environment by editing the rc file for your shell (*.bashrc* for bash) variable for easy access.

### Configuring Litecoin

You will find several files under the *bin* directory under Litecoin installation path. In my case it is under *$HOME/litecoin/*

These files include
- litecoind
- litecoin-cli
- litecoin-qt

We will first create a *litecoin.conf* file under *$HOME/.litecoin* to specify the configuration for the Litecoin daemon. Here is the configuration I am using:

{% highlight shell %}
rpcuser=ltcuser
rpcpassword=mypassword
rpcallowip=127.0.0.1
testnet=1
server=1
{% endhighlight %}

The username and password above is to access the Litecoin daemon over RPC. For instance, when we later run *litecoin-cli* it will need to access the Litecoin daemon in order to execute a command. That access happens using RPCs. In this case, we also limit the access to localhost so that no remote hosts could access our Litecoin daemon. We also set the *testnet* flag to 1 to indicate to the Litecoin daemon that it needs to connect to other peers in the Litecoin testnet and not the mainnet.

### Running litecoind

{% highlight shell %}
$ ./litecoind &
{% endhighlight %}

Now that the daemon is running, we could use the *litecoin-cli* command to get some information about the network:

{% highlight shell %}
$ ./litecoin-cli getinfo
{
  "version": 149900,
  "protocolversion": 70015,
  "walletversion": 130000,
  "balance": 500.01926600,
  "blocks": 135009,
  "timeoffset": 0,
  "connections": 2,
  "proxy": "",
  "difficulty": 0.0002441371325370145,
  "testnet": true,
  "keypoololdest": 1500868253,
  "keypoolsize": 100,
  "paytxfee": 0.00000000,
  "relayfee": 0.00100000,
  "errors": "This is a pre-release test build - use at your own risk - do not use for mining or merchant applications"
}
{% endhighlight %}

The above output shows that we are connected to the testnet (two connections), the most recent block seen by the dawmon and the balance in terms of LTCT (testnet Litecoins).

In a later post, we will continue from here and I will take you guys through setting up a solo CPU miner to mine some Litecoins on the testnet!

-----

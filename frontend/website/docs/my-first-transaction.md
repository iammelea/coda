# My First Transaction

In this section, we'll make our first transaction on the Coda network. After [setting up `coda`](../getting-started), we'll need to create a new account before we can send or receive coda. Let's first start up the node so that we can start issuing commands.

## Start up a node

Run the following command to start up a Coda node instance and connect to the network:

    $ coda daemon -peer strawberry-milkshake.o1test.net:8303

The host and port specified above refer to the seed peer address - this is the initial peer we will connect to on the network. Since Coda is a [peer-to-peer](../glossary/#peer-to-peer) protocol, there is no single centralized server we rely on. 

!!!note
    The daemon process needs to be running whenever you issue commands from `coda client`, so make sure you don't kill it by accident.

### Troubleshooting

If you're running Coda on macOS and see the following time out error `monitor.ml.Error "Timed out getting connection from process"`, you'll need to add your hostname to `/etc/hosts` by running the following:

- `$ hostname` to get your hostname
- `$ vim /etc/hosts` to open your hostfile and add the mapping:

```    
##
# Host Database
#
# localhost is used to configure the loopback interface
# when the system is booting.  Do not change this entry.
##
127.0.0.1       localhost
127.0.0.1       <ADD YOUR HOSTNAME HERE>
```

This is necessary because sometimes macOS doesn't resolve your hostname to your local IP address.


## Checking connectivity

Now that we've started up a node and are running the Coda daemon, open up another shell (if you're on macOS, cd to the place where you've extracted the zip and `export PATH=$PWD:$PATH`), and run the following command:

    $ coda client status

!!!note
   For now, it will take up to a minute before `coda client status` will even successfully connect to the daemon when you are first starting it up. So if you see `Error: daemon not running. See coda daemon`, just a wait a bit and try again.

Most likely we will see a response that include the fields below:

    ...
    Peers:                                         Total: 4 (...)
    ...
    Sync Status:                                   Synced

This step requires waiting for approximately ~5 minutes to sync with the network. When sync status reaches `Synced` and the node is connected to 2 or more peers, we will have successfully connected to the network.

### Troubleshooting hints:

- If the number of peers is 1 or fewer, there may be an issue with the IP address - make sure you typed in the IP address and port exactly as specified in [Start a Coda node](#start-a-coda-node).
- If sync status is `Offline` for more than 10 minutes, you may need to [configure port forwarding for your router ](/docs/getting-started/#port-forwarding). Otherwise you may need to resolve connectivity issues with your home network.
- If sync status is `Bootstrap`, you'll need to wait for a bit for your node to catch up to the rest of the network. In the Coda network, we do not have to download full transaction history from the genesis block, but nodes participating in block production and compression need to download recent history and the current account data in the network.

## Create a new account

Once our node is synced, we'll create a public/private key-pair so that we can sign transactions and generate an address to receive payments. For security reasons, we'll want to put the keys under a directory that is harder for attackers to access.

Run the following command which creates a public and private key `my-wallet` and `my-wallet.pub` under the `keys` directory:

    $ coda client generate-keypair -privkey-path keys/my-wallet

!!! warning
    The public key can be shared freely with anyone, but be very careful with your private key file. Never share this private key with anyone, as it is the equivalent of a password for your funds.

## Request coda

In order to send our first transaction, we'll first need to get some Coda to play with. Head over to the [Coda Discord server](https://bit.ly/CodaDiscord) and join the `faucet` channel. Once there, ask our faucet bot for some tokens (you'll receive 100). Here's an example:

    $request tNciWRVyhakSXV1gzHg8KdvWccJ4HXorwUPTUS2SgHVi3gKk4WUbcPqSvRBGSHSUVjZGhJooyLvSkQaxf8eFnAW5sQsAiDF1zRj1hDnnRVFRQsck3kQYna1ELv4UxBt6VP232wpCcrwh8g

Once a faucet-mod thumbs up your request, keep an eye on the discord channel to see when the transaction goes through on that side. 

We can check our balance to make sure that we received the funds by running the following command, passing in our public key:

    $ coda client get-balance -address <public_key>

You might see `No account found at that public_key (zero balance)`. Be patient! Depending on the traffic in the network, it may take a few blocks before your transaction goes through.

While you're waiting take a look at your daemon logs for new blocks being generated. Run the following command to see the current block height:

    $ coda client status

## Make a payment

Finally we get to the good stuff, sending our first transaction! For testing purposes, there's already an echo service set up that will immediately refund your payment minus the transaction fees.

Let's send some of our newly received coda to this service to see what a payment looks like:

    $ coda client send-payment -amount 10 -receiver tNciF85uM2yA1QHWc24vdQCGUe7EykM4cqaJma8FXqp64JDssnv5ywPsWNv3417akmKRwBmVaMwrSkXjZrBpJaCtfcAbNupLwSx1PEd9135kEZek7muGySzq1bQZ6nGR4oNVoy3qygX1ph -fee 2 -privkey-path keys/my-wallet

If you're wondering what we passed in to the commands above:

- For `amount`, we're sending a test value of `10` coda
- The `receiver` (public key) of the echo service is `TODO`
- For `fee`, let's use the current market rate of `2` coda
- The `privkey-path` is the private key file path of the private key we generated the `keys` folder

If this command is formatted properly, we should get a response that looks like the following:

    Dispatched payment with ID 3XCgvAHLAqz9VVbU7an7f2L5ffJtZoFega7jZpVJrPCYA4j5HEmUAx51BCeMc232eBWVz6q9t62Kp2cNvQZoNCSGqJ1rrJpXFqMN6NQe7x987sAC2Sd6wu9Vbs9xSr8g1AkjJoB65v3suPsaCcvvCjyUvUs8c3eVRucH4doa2onGj41pjxT53y5ZkmGaPmPnpWzdJt4YJBnDRW1GcJeyqj61GKWcvvrV6KcGD25VEeHQBfhGppZc7ewVwi3vcUQR7QFFs15bMwA4oZDEfzSbnr1ECoiZGy61m5LX7afwFaviyUwjphtrzoPbQ2QAZ2w2ypnVUrcJ9oUT4y4dvDJ5vkUDazRdGxjAA6Cz86bJqqgfMHdMFqpkmLxCdLbj2Nq3Ar2VpPVvfn2kdKoxwmAGqWCiVhqYbTvHkyZSc4n3siGTEpTGAK9usPnBnqLi53Z2bPPaJ3PuZTMgmdZYrRv4UPxztRtmyBz2HdQSnH8vbxurLkyxK6yEwS23JSZWToccM83sx2hAAABNynBVuxagL8aNZF99k3LKX6E581uSVSw5DAJ2S198DvZHXD53QvjcDGpvB9jYUpofkk1aPvtW7QZkcofBYruePM7kCHjKvbDXSw2CV5brHVv5ZBV9DuUcuFHfcYAA2TVuDtFeNLBjxDumiBASgaLvcdzGiFvSqqnzmS9MBXxYybQcmmz1WuKZHjgqph99XVEapwTsYfZGi1T8ApahcWc5EX9
    Receipt chain hash is now A3gpLyBJGvcpMXny2DsHjvE5GaNFn2bbpLLQqTCHuY3Nd7sqy8vDbM6qHTwHt8tcfqqBkd36LuV4CC6hVH6YsmRqRp4Lzx77WnN9gnRX7ceeXdCQUVB7B2uMo3oCYxfdpU5Q2f2KzJQ46

## Check account balance

Now that we can send transactions, it might be helpful to know our balance, so that we don't spend our testnet tokens too carelessly! Let's check our current balance by running the following command, passing in the public key of the account we generated:

    $ coda client get-balance -address <public-key>

We'll get a response that looks like this:

    50

Once you feel comfortable with the basics of creating an address, and sending & receiving coda, we can move on to the truly unique parts of the Coda network - [participating in consensus and helping compress the blockchain](/docs/node-operator).
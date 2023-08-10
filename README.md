# Bug report

Sending a transaction with inputData through Nautilus Testnet
0.8.2 produces the following error: 

```
{code: -2, info: '[GraphQL] ERGO_NODE_ADDRESS is undefined in .env file.'}
```


## How to reproduce:

Step 1: clone and run
```
git clone git@github.com:SavonarolaBit/bug-tx-inputData.git && npm i && npm run dev
```

Step 2: press "send 1 erg to self with oracle box (ERROR)"
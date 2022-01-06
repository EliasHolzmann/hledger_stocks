# hledger-get-market-prices
You can use this project to get historical stock and fonds prices from the Alpha Vantage API and convert them to the hledger market price format. This is useful to keep track of the current value of your portfolio.

## How to install
```
$ cargo install hledger-get-market-prices
```

## How to use
Let's say you are interested in how the fonds with ISIN IE00BJ0KDQ92 traded on XETRA. In hledger, you call the commodity of this fonds `MSCIWRLD`. The Euro is used at XETRA to trade `MSCIWRLD`, and hledger knows this currency as the commodity `€`.
### Getting a Alpha Vantage API key
First, you need an Alpha Vantage API key. You can receive a key for free [here](https://www.alphavantage.co/support/#api-key).

`hledger-get-market-prices` expects this key in the environment variable `HLEDGER_GET_MARKET_PRICES_API_KEY`, so either add this to your environment for all applications (for example inside `~/.bashrc` if you are using Bash) or always call `hledger-get-market-prices` with the key prepended (`HLEDGER_GET_MARKET_PRICES_API_KEY=<key> hledger get-market-prices ...`).

### Finding out the correct symbol
Next, you need to find out the symbol used for the fonds. In theory, you can search via `hledger get-market-prices search-stock-symbol`, but the search API of Alpha Vantage is a bit tricky. You can try to search by the ISIN:

```
$ hledger get-market-prices search-stock-symbol IE00BJ0KDQ92
```

Currently, this does not return any results. Searching for the name of the fonds ("Xtrackers MSCI World UCITS ETF 1C") doesn't work either. But you can search for the ticker symbol XETRA uses. As you can see [here](https://www.boerse-frankfurt.de/en/etf/xtrackers-msci-world-ucits-etf-1c), XETRA uses the ticker symbol *XDWD*. Searching for `XDWD` leads to multiple results:

```
$ hledger get-market-prices search-stock-symbol XDWD
```
```
              Region |    Symbol – Name                

           Frankfurt |  XDWD.FRK – Xtrackers (IE) Public Limited Company - Xtrackers MSCI World UCITS ETF
      United Kingdom |  XDWD.LON – Xtrackers (IE) Plc - Xtrackers MSCI World UCITS ETF 1C
               XETRA |  XDWD.DEX – Xtrackers (IE) Plc - Xtrackers MSCI World UCITS ETF 1C
```

You are interested on results for XETRA, so `XDWD.DEX` is the correct symbol.

### Getting historic data
Now, you can use the symbol to ask for historic data:

```
hledger get-market-prices history XDWD.DEX MSCIWRLD €
```
```
; Generated by hledger-get-market-prices V1.0.0
P 2022-01-07 MSCIWRLD 84.838 €
P 2022-01-06 MSCIWRLD 85.4 €
P 2022-01-05 MSCIWRLD 86.752 €
P 2022-01-04 MSCIWRLD 86.752 €
P 2022-01-03 MSCIWRLD 86.594 €
P 2021-12-30 MSCIWRLD 86.706 €
...
```

## FAQ
### What price is used by `hledger-get-market-prices`? Open price, close price, average price or something different?
Currently, `hledger-get-market-prices` uses close prices because that's what I am interested in. If you want additional functionality, a pull request is welcome.
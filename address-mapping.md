# Name-Address Mapping Standard (Draft)

## Abstract

Sending a cryptocurrency transaction requires a long, human-hostile sequence of characters By pointing a human-readable name at the address, users can send cryptocurrency to the name instead of needing to ask for an address. This enables a more user-friendly experience, improved security and enables additional functionality such as automated-recurring transactions. In this document, we propose a standard for global name systems that map names to addresses.

## 1. Introduction & Use Cases

Sending a cryptocurrency transaction to another requires knowing the preferred address (or account) of the recipient. This is typically is a long, human-hostile sequence of letters and numbers which is inconvenient to type and error-prone to verify. A single mis-typed character can make the address invalid requiring the user to re-type it or at worse result in loss of funds.

By pointing a human-readable name at the account or address enable, users can send cryptocurrency directly to the name instead of needing to ask for an address enabling a much more user-friendly experience. In addition, when this name-address mapping is stored in a name system that supports cryptographic authentication, the user can also be sure that the address a query returns was entered by the person in control of the name.

### 1.1 Example: Sending BTC to a name

A donor wishing to donate BTC to the Bitcoin Association of Hong Kong has to locate the donation address on [bitcoin.org.hk](https://www.bitcoin.org.hk) , _1BAHK1Esvn6L1icZREB8SFqTy7bBSRDwaS_, and then copy and paste or type it into his wallet before sending the donation. With this standard and wallet support, the user could enter “bitcoin.org.hk” in his wallet and send bitcoin directly to the appropriate address.

### 1.2 Example: Automated, recurring payments

The previous donor might want to make a monthly BTC donation to the Bitcoin Association of Hong Kong. To do this, he would have to look up the current address for the association each month or the association would have to commit to never changing the address. The association might not want to make that commitment because The association, however, might not want to make such a commitment because changing the address has a number of potential benefits. It might enable support a new address format bringing possibly lower fees, more functionality or improved security. Changing the address might reflect a new multi-sig wallet generated to reflect a change in board membership.

Instead, with this standard, he could authorize his wallet to periodically send cryptocurrency to “bitcoin.org.hk” and his wallet could figure out the appropriate address at which the association wishes to receive donations on his behalf.

## 2. Terminology

### 2.1 Address

An _address_ refers to the public key representation of an cryptocurrency key pairs in a format commonly presented to end users. In some cryptocurrencies, address is referred to as an account. Users of this protocol _MAY_ use either term, but this specification will use the term “address” to refer to both.

### 2.2 NS

NS refers to a globally accessible key-value Name System. This standard works best with name systems that support a subset of the zone file standard as defined in RFC1034 and RFC1035.

Examples include but are not limited to:

- BNS - the Bitcoin Name System - anchored in Bitcoin with smart contract logic on the Stacks blockchain
- DNS - the Domain Name System - run by the corporation ICANN
- ENS - the Ethereum Name System - built on Ethereum
- HNS - the Handshake Name System - built on Handshake

## 3. NS Binding

A binding using NS TXT RRs as an address or account service is hereby defined. All implementations _MUST_ support this binding.

### 3.1 Resource Record Namespace

All addresses or accounts are stored in an [underscored attribute lea](https://datatracker.ietf.org/doc/html/rfc8552)[f](https://datatracker.ietf.org/doc/html/rfc8552) named `_addr`. prepended with coin’s ticker symbol name. The resource record name for the BTC address of DNS domain `example.com` would be `_btc._addr.example.com`.

### 3.2 Resource Record Types for Address Storage

The DNS Resource Record type used is specified by an option to the
query-type ("q=") tag. The only option defined in this base standard is "txt", indicating the use of a TXT RR. A later extension of this standard may define another RR type. Strings in a TXT RR MUST be concatenated together before use with no intervening whitespace. TXT RRs MUST be unique for a particular selector name; (eg. only one `_btc._addr.example.com`). If there are multiple records in an RRset, the results are undefined. TXT RRs are encoded as described in Section 3.3

### 3.3 Textual Representation

Addresses are stored as a comma-delimited list. The first address in the list is the default address and _MUST_ be presented as the default or preferred address in applications that permit multiple address to displayed. Trailing commas

### 3.4 TXT Resource Record Examples

#### 3.4.1 DNS name with single BTC address

```
	_btc._addr.bitcoin.org.hk.   IN   TXT   "1BAHK1Esvn6L1icZREB8SFqTy7bBSRDwaS"
```

DNS name `bitcoin.org.hk` resolves to BTC address `1BAHK1Esvn6L1icZREB8SFqTy7bBSRDwaS`

```javascript
resolveName("bitcoin.org.hk).getDefaultAddress("BTC") // returns { coin: "BTC", address:"1BAHK1Esvn6L1icZREB8SFqTy7bBSRDwaS" }
resolveName("bitcoin.org.hk).getAddresses("BTC") // returns [{ coin: "BTC", address:"1BAHK1Esvn6L1icZREB8SFqTy7bBSRDwaS" }]
resolveName("bitcoin.org.hk).getDefaultAddress("STX") // returns null
resolveName("bitcoin.org.hk).getAddresses("STX") // returns null
```

#### 3.4.2 BNS name with single BTC address

```
	_btc._addr.muneeb.btc.   IN   TXT   "1BAHK1Esvn6L1icZREB8SFqTy7bBSRDwaS"
```

BNS name `muneeb.btc` owned by `SP132QXWFJ11WWXPW4JBTM9FP6XE8MZWB8AF206FX` resolves to BTC address `1BAHK1Esvn6L1icZREB8SFqTy7bBSRDwaS`.

```javascript
	resolveName("muneeb.btc").getDefaultAddress("BTC") // returns { coin: "BTC", address:"1BAHK1Esvn6L1icZREB8SFqTy7bBSRDwaS" }

	resolveName("muneeb.btc").getAddresses("BTC") // returns [{ coin: "BTC", address:"1BAHK1Esvn6L1icZREB8SFqTy7bBSRDwaS" }]

	resolveName("bitcoin.org.hk).getDefaultAddress("STX") // returns { coin: "STX", address:"SP132QXWFJ11WWXPW4JBTM9FP6XE8MZWB8AF206FX" }

	resolveName("bitcoin.org.hk).getAddresses("STX") // returns [{ coin: "STX", address:"SP132QXWFJ11WWXPW4JBTM9FP6XE8MZWB8AF206FX" }]
```

#### 3.4.3 DNS name with multiple BTC addresses

```
	_btc._addr.example.com.   IN   TXT   "1BAHK1Esvn6L1icZREB8SFqTy7bBSRDwaS,1111111111111111111114oLvT2"
```

DNS name `example.com` resolves to the BTC addresses `1BAHK1Esvn6L1icZREB8SFqTy7bBSRDwaS` and `1111111111111111111114oLvT2`. The default BTC address is `1BAHK1Esvn6L1icZREB8SFqTy7bBSRDwaS`.

```javascript
resolveName("example.com").getDefaultAddress("BTC"); // returns { coin: "BTC", address:"1BAHK1Esvn6L1icZREB8SFqTy7bBSRDwaS" }
resolveName("example.com").getAddresses("BTC"); // returns [{ coin: "BTC", address:"1BAHK1Esvn6L1icZREB8SFqTy7bBSRDwaS" },{ coin: "BTC", address:"1111111111111111111114oLvT2" }]
resolveName("example.com").getDefaultAddress("STX"); // returns null
```

#### 3.4.4 BNS name with a BTC address and a STX account

```
	_btc._addr.muneeb.btc.   IN   TXT   "1BAHK1Esvn6L1icZREB8SFqTy7bBSRDwaS"
	_stx._addr.muneeb.btc.   IN   TXT   "SP000000000000000000002Q6VF78"
```

BNS name `muneeb.btc` resolves to the BTC address `1BAHK1Esvn6L1icZREB8SFqTy7bBSRDwaS` and the STX address `SP000000000000000000002Q6VF78`.

```javascript
resolveName("muneeb.btc").getDefaultAddress("BTC"); // returns { coin: "BTC", address:"1BAHK1Esvn6L1icZREB8SFqTy7bBSRDwaS" }
resolveName("muneeb.btc").getAddresses(); // returns [{ coin: "BTC", address:"1BAHK1Esvn6L1icZREB8SFqTy7bBSRDwaS" },{ coin: "STX", address:"SP000000000000000000002Q6VF78" }]
resolveName("muneeb.btc").getDefaultAddress("STX"); // returns { coin: "STX", address:"SP000000000000000000002Q6VF78" }
```

## 4. Specific Name System Considerations

### 4.1 BNS - Bitcoin Name System

- Zone file support
- Built-in cryptographic authentication
- BTC/STX coin types can default to the owner of the name

### 4.2 DNS - Domain Name System

- Full zone file support
- DNSSEC provides a degree of cryptographic authentication
- No default address

### 4.3 HNS - Handshake Name System

- Zone file support
- Built-in cryptographic authentication of TLD, unclear about the cryptographic guarantees for subdomains of TLDs
- HNS coin types can default to owner of the name

### 4.4 ENS - Ethereum Name System

- Generally, does not support zone files. Possible to adapt to standard to txt type.
- Built-in cryptographic authentication
- ETH coin types can default to owner of the name

## 6. References

### 6.1 Normative references

- [RFC1034](https://www.rfc-editor.org/rfc/rfc1034)
- [RFC1035](https://datatracker.ietf.org/doc/html/rfc1035)
- [RFC1464](https://www.rfc-editor.org/rfc/rfc1464)
- [RFC2119](https://www.rfc-editor.org/rfc/rfc2119)
- [RFC8552](https://datatracker.ietf.org/doc/html/rfc8552)
- [Lightning Address](https://github.com/andrerfneves/lightning-address/blob/master/README.md)

### 6.2 Informative references

- [https://bitcoinmagazine.com/technical/simplifying-bitcoin-addresses-dns](https://bitcoinmagazine.com/technical/simplifying-bitcoin-addresses-dns)

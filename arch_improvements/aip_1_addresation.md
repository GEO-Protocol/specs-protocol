# Addressation 

#### Protocol version
New communication protocol **must** contain protocol version included into each one message. It is neccesary for ability to upgrade the protocol in the future.

```c++
class BaseMessage {

public:
    enum PROTOCOL_VERSION {
        Latest = 0,
    }

private:
    byte mVersion;
}
```

#### Primitives 
Primitives that might be useful for addressing purposes.
All suported formats should be listed here.
Also, we should reserve possibility to add other addresation primitives in future.


```c++
class IPv4Address { /* 4B  */ };
class IPv6Address { /* 16B */ };
class DNSRecord { /* host -> string, 255 symb. max */ }

class BaseGNSRecord {
    string provider;    // Top-level domain in terms of classic DNS. 
                        // Max length - 255.
                        // Min length - 1.
}

/*
 * Regular GNS record.
 */
class GNSRecord: 
public BaseGNSRecord {
    string name;		// Internal name, used inside of this domain.
                        // Max length - 255.
                        // Min length - 1.
}

/* 
 * Useful for IoT addressation purposes.
 * GNS is able to store binary data, so it might a replacement for UUIDv4 or 
 * IPv6 address.
 */
class BinaryGNSRecord:
public BaseGNSRecord {
    byte id[16];
}
```


#### Communicator primitives
```c++

/*
 * Endpoints collects addresses that are available for one remote node.
 * Each node is able to be addressable via several addresation methods. 
 * Each node is able to have several addresation methods of the same type 
 * (for example several static IPv4Addresses).
 */
class Endpoints {
public:
    Endpoints(
        vector<IPv4Address> &&ipv4Addresses); // uses move semantics.

    /**
     * Returns count of ipv4 addresses available.
     * If 0 - ipv4Addresses() must return ipv4AddressesEnd().
     */
    const uint8 ipv4AddressCount() const;
    vector::const_iterator<IPv4Address> ipv4Addresses() const;
    vector::const_iterator<IPv4Address> ipv4AddressesEnd() const;

    // ...
    // The same structure is desired for all other addresation methods.
    // ...

private:
    vector<IPv4Address> mIPv4Addresses;
    // ...
}
```

#### Communicator helpers
```c++
class GNSResolver {
public:
    /**
     * Returns IPv4 and/or IPv6 received from the GNS provider,
     * or "nullptr" in case if (one|both) address(es) resolving is impossible.
     * checks internal cache before any providers request.
     */ 
    pair<IPv4Address::Shared, IPv6Address::Shared> resolve(
        GNSRecord &record)
        const; 

    pair<IPv4Address::Shared, IPv6Address::Shared> resolve(
        BinaryGNSRecord &record)
        const; 
}

```

```c++
class GNSCache {
public:
    pair<IPv4Address::Shared, IPv6Address::Shared> resolve(
        GNSRecord &record)
        const; 

    pair<IPv4Address::Shared, IPv6Address::Shared> resolve(
        BinaryGNSRecord &record)
        const; 

}
```

```c++
class DNSResolver {
public:
    /**
     * Returns IPv4 and/or IPv6 received from the DNS provider,
     * or "nullptr" in case if (one|both) address(es) resolving is impossible.
     * checks internal cache before any providers request.
     */ 
    pair<IPv4Address::Shared, IPv6Address::Shared> resolve(
        DNSRecord &record)
        const; 
}

```

```c++
class DNSCache {
public:
    pair<IPv4Address::Shared, IPv6Address::Shared> resolve(
        DNSRecord &record)
        const; 
}
```



#### Engine primitives
```c++
class Contractor {
    const uint16 id() const;

    // ...
    
    const Endpoints& endpoints() const;
}
```


# Packet Capture

> PII = Personally Identifiable Information
, PHI = Personal Health Information

Network Interface Cards (NICs) are programmed to only forward frames to the OS whose destination MAC is either the NICs MAC or the broadcast MAC (ff:ff:ff:ff:ff:ff). To forward all to the OS we need to enable promiscuous mode.

In the OSI model, each chunk of data associated to a layer has a different name. This is the Protocol Data Unit (PDU).

- Layer 2 -> PDU = frame
- Layer 3 -> PDU = packet
- Layer 4 -> PDU = segment (TCP) datagram (UDP)

Fragmentation may be forced by the Maximum Transmission Unit (MTU) at layer 2 (frame layer).

## tcpdump

```
tcpdump
```

- No name resolution:

```
tcpdump -n
```

- Verbosity (add details as L4 headers).

```
tcpdump -vv  -w output.pcap
```

- Hex representation of packet

```
tcpdump -i eth0 -X
```

## tshark

- Basic run

```
tshark
```

- Print certain fields (commonly same name as in tcpdump)

```
tshark -Tfields -e frame.number -e ip.src -e ip.dst -e ip.tos -e ip.ttl
```

## wireshark

It will accept RSA keys for performing decoding.

# Berkeley Packet Filter (BPF)

BPF is an interface to the Data Link layer on a system. tcpdump, tshark and wireshark compatible.

```
not port 80
not port https
tcp
tcp and \(host 192.168.1.1\)
src host 192.168.1.1
not dst port https
```

# Packet Analysis

> Spoofing: In the context of information security, and especially network security, a spoofing attack is a situation in which a person or program successfully identifies as another by falsifying data, to gain an illegitimate advantage.

Follow TCP stream in wireshark.

# Spoofing Attacks

## ARP (Address Resolution Protocol) Spoofing

ARP:

1. ARP Request asking for MAC Addr for a particular IP Addr. There's nothing to authenticate this request.

> ARP poisoning

Systems will take ARP responses even if they didn't ask. Senginf ARP responses without a previous ARP requests is known as gratuitous ARP.

On linux, default cache time is 60s. On windows, 30000 ms multiplied by a (random) factor between 0.5 and 1.5.

```
cat /proc/sys/net/ipv4/neigh/default/gc_stale_time
```

We need to forward this message sent to us! Otherwise the victim wouldn't reach their destination.

Tools for ARP spoofing: arpspoof and fragroute.

```
sudo arpspoof -i eth0 -c both 192.168.1.1
```

> -t \<IP\> -r would also collect reverse connections (from router to victim)

## DNS Spoofing

More targeted than ARP spoofing.

You could use ettercap. DNS requests are usually cached so we only need to spoof once.

DNS config in ettercap: /etc/ettercap/etter.dns.

WoW: ARP spoof to intercept DNS requests and poison DNS response. This will only work in LAN because of need to access to MAC.

## sslstrip

To decrypt SSL messages. Won't work for TLS>=1.2. It actas as a transparent proxy and changes HTTPS to HTTP.

It needs to have ARP spoofing in place. sslstrip needs to know which firewall command is being used so it can set up a redirect in the firewall.

## Spoofing detection

Look at MAC addr from ARP answer: Three first bytes are ornganizationally unique identifier (OUI). Use a OUI lookup tool.

At network layer, your router can perform reverse path verification. It performs a lookup in the routing table: it checks the routing table for a given IP to know which interface a response packet would go out on. If this is different from the incoming interface, the message is maybe spoofed.

DNS spoofing is harder to detect: you could check for the IP addr for DNS server, but DNS works in a way thay you could get a response from a different server to the one you performed the request to. Better option to always use DNS Security Extensions (DNSSEC) that uses cryptographic verification of servers.

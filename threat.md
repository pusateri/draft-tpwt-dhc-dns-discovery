# Threat analysis of DHCP private DNS discovery options

New DHCPv6 options for distributing private DNS configuration parameters are proposed in order to mitigate some of the current vulnerabilities associated with DHCP DNS server IP address-only distribution.

For this discussion, a distinction will be made between authenticating a DNS server and establishing a trust relationship with the DNS server. A client may be able to verify the DNS server is authentic but this makes no claims as to its trustworthiness. A trust relationship is established in different ways or not at all and it may require out of band mechanisms to establish that trust. Trust isn't automatically established by a DHCP reply, but it may be given the environement the client is operating in.

## Data to be protected

1. DNS queries associated with an IP address or that can be associated with a client identifier

## Prior vulnerabilities

The following vulnerabilities exist prior to the addition of additional DHCPv6 private DNS discovery options.

1. (information disclosure) - the servers associated with these IP addresses are meant to be used unencrypted over UDP and fallback to TCP under certain error conditions exposing the queries to local observation.
2. (spoofing) - network administrators send out DNS server IP address information only providing no external means for verification. These DNS servers are not guaranteed to be the actual servers represented by the network administrators due to spoofing or man in the middle attacks.
3. (tampering, denial of service) - these servers may be tampering with the queries or modify the responses in order to taint or filter the results.
4. (information disclosure) - These queries may be logged, analyzed, and/or use the private data contained within at the DNS server for illegitimate purposes.

## Mitigations prior to private DNS DHCP options

For the above four vulnerabilities, there are some mitigations available using existing mechanisms prior to having an Authentication Domain Name or knowing for sure that private DNS services are offered.

Of course, the client can always choose to not trust any of the DNS servers received via DHCP, opting instead for a known DNS service with whom it has established a prior trust relationship.

1. (information disclosure) - the DHCP client attempting to connect to the DNS server MAY attempt to connect using DNS over TLS or DNS over HTTPS to determine if these servers also support DNS privacy. However, the client has no means to verify the integrity of the server using methods such as discussed in RFC 6125. Trying to attempt connections to DNS servers using multiple protocols can significantly increase the delay for receiving DNS responses.
2. (spoofing) - if the DHCP client can connect to the DNS server using TLS and an SPKI pin is available out of band, the client can have some assurance the DNS server is the server it claims to be.
3. (tampering, denial of service) - Transaction signatures using the SIG(0) specification can be used to validate the DNS requests/responses from tampering. 
4. (information disclosure) - no known mitigation

## Mitigations with the addition of ADN Option

Adding a verifyable authentication domain name increases the ability to authenticate a DNS server.

1. (information disclosure) - knowing that a form of private DNS is available and how to connect to it will prevent local observation of DNS queries. Additionally, connecting directly to a know private DNS server without having to search for it should reduce the delay in receiving a response.
2. (spoofing) - with a known DNSSEC root trust anchor, it is possible to use DNSSEC verification of the IP address records as well as the Authtication Domain Name records of the DNS servers. A DHCP client can verify the DNS server it is communicating with is indeed the server it claims to be.
3. (tampering, denial of service) - no additional mitigation
4. (information disclosure) - no known mitigation

## Conclusion

1. The addition of DNSSEC provides the biggest gain in server identify verification but having an authentication domain name provides additional information to ease the verification.

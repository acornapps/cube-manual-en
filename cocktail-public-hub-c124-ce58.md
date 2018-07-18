# Cocktail Public Hub Server Installation

An SSL certificate must be issued (VeriSign, Thawte, GeoTrust, etc.) when applying HTTPS to a web service. Due to the additional costs, however, such action should be avoided unless it's applied to an actual operational server.

In this case, you can create a certificate authority via OpenSSL to generate a self-signed certificate and issue an SSL certificate.

> Reference: What is a Self-Signed Certificate\(SSC\)?  
> A digital certificate is data that is digitally-signed on a private key holder's public key using a certificate authority's private key. All certificates must have a certificate authority (CA). Because no higher authority exists to sign for the highest-level certificate authority (root CA), a certificate is self-signed with the private key of the root CA to create a top-level certificate. This type of root CA certificate is referred to as a self-signed certificate.
>
> Developers of web browsers such as IE, Firefox, and Chrome pre-registered the certificates of well-known root CAs like VeriSign and Comodo as trusted CAs. Trusted SSL certificates issued by such agencies are required for browsers. Because root CA and SSL certificates created via OpenSSL are not recognized by browsers, such certificates will trigger a browser security alert. However, OpenSSL-based certificates pose no harm for testing purposes. 
>
> A root CA certificate can be added to a browser while avoiding a security warning.
>
> * CSR\(Certificate Signing Request\)은?
>
> A public key infrastructure (PKI) consists of private and public keys. A certificate is an electronically-signed proof that a certificate authority (CA) deemed a public key to be legitimate. Secure communications can be conducted between two parties having the other party use the public key within one's certificate.
>
> A CSR is a special ASN.1 file that requests a certificate authority to issue a certificate and contains public key information and related algorithm information. Since a private key must remain secure without exposure to third parties, a special type of file such as a CSR is created and sent to the certificate authority to obtain a certificate.
>
> CSR generation is performed on a web server when an SSL certificate is issued. Because the methods vary by web server, users face difficulty creating CSRs, and so certificate-issuing agencies generate and transmit private keys.

Cocktail Public Hub uses digital certificates for communications between Harbor and Cocktail build servers.

Cocktail Private Hub generates self-signed certificates for Harbor Registry and Cocktail build servers to internally generate Harbor and Docker certificates.

![](/assets/PrivateHubOverview.jpeg)


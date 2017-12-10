# Cocktail Public Hub 서버 설치

웹서비스에 https 를 적용할 경우 SSL 인증서를 VeriSign 이나 Thawte, GeoTrust 등에서 인증서를 발급받아야 하지만 비용이 발생하므로 실제 운영 서버가 아니면 발급 받는데 부담이 된다.

이런 경우에, OpenSSL 을 이용하여 인증기관을 만들고 Self signed certificate 를 생성하고 SSL 인증서를 발급하는 사용한다.

> 참고. Self Signed Certificate\(SSC\)란 ?  
> 인증서\(digital certificate\)는 개인키 소유자의 공개키\(public key\)에 인증기관의 개인키로 전자서명한 데이타다. 모든 인증서는 발급기관\(CA\) 이 있어야 하나 최상위에 있는 인증기관\(root ca\)은 서명해줄 상위 인증기관이 없으므로 root ca의 개인키로 스스로의 인증서에 서명하여 최상위 인증기관 인증서를 만든다. 이렇게 스스로 서명한 ROOT CA 인증서를 Self Signed Certificate 라고 부른다.
>
> IE, FireFox, Chrome 등의 Web Browser 제작사는 VeriSign 이나 comodo 같은 유명 ROOT CA 들의 인증서를 신뢰하는 CA로 미리 등록해 놓으므로 저런 기관에서 발급된 SSL 인증서를 사용해야 browser 에서는 해당 SSL 인증서를 신뢰할수 있는데 OpenSSL 로 만든 ROOT CA와 SSL 인증서는 Browser가 모르는 기관이 발급한 인증서이므로 보안 경고를 발생시킬 것이나 테스트 사용에는 지장이 없다.
>
> ROOT CA 인증서를 Browser에 추가하여 보안 경고를 발생시키지 않을 수 있다.
>
> * CSR\(Certificate Signing Request\)은?
>
> 공개키 기반\(PKI\)은 private key\(개인키\)와 public key\(공개키\)로 이루어져 있다. 인증서라고 하는 것은 내 공개키가 맞다고 인증기관\(CA\)이 전자서명하여 주는 것이며 나와 보안 통신을 하려는 당사자는 내 인증서를 구해서 그 안에 있는 공개키를 이용하여 보안 통신을 할 수 있다.
>
> CSR 은 인증기관에 인증서 발급 요청을 하는 특별한 ASN.1 형식의 파일이며 그 안에는 내 공개키 정보와 사용하는 알고리즘 정보등이 들어 있다. 개인키는 외부에 유출되면 안 되므로 저런 특별한 형식의 파일을 만들어서 인증기관에 전달하여 인증서를 발급 받는다.
>
> SSL 인증서 발급시 CSR 생성은 Web Server 에서 이루어지는데 Web Server 마다 방식이 상이하여 사용자들이 CSR 생성등을 어려워하니 인증서 발급 대행 기관에서 개인키까지 생성해서 보내주고는 한다.

Cocktail public Hub는 공인인증서를 이용하여 Harbor &lt;-&gt; Cocktail build server 간 통신 하게 된다.

Cocktail Private Hub는 Harbor Registry와 Cocktail build Server를 위한 인증서 Self signed certificate를 생성하여 내부에서

Harbor와 Docker 인증서를 생성한다.

![](/assets/cocktailhub.jpeg)


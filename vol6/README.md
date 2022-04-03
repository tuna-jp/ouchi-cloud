## ドメインを購入せず、HTTP-01チャレンジでTLS証明書を発行する方法

https://sslip.io を使用すればドメインを購入することなくLet's Encryptで証明書を発行可能です。
sslip.ioは`foo.a-b-c-d.sslip`が`a.b.c.d`に解決されるようなDNSサービスです。
`a.b.c.d`をグローバルIPに置き換えて使用できます。

例えば`ouchi.168-138-44-162.sslip.io`は`168.138.44.162`に解決されます。

```
$ dig ouchi.168-138-44-162.sslip.io

; <<>> DiG 9.16.1-Ubuntu <<>> ouchi.168-138-44-162.sslip.io
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 25377
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 65494
;; QUESTION SECTION:
;ouchi.168-138-44-162.sslip.io.	IN	A

;; ANSWER SECTION:
ouchi.168-138-44-162.sslip.io. 86400 IN	A	168.138.44.162

;; Query time: 168 msec
;; SERVER: 127.0.0.53#53(127.0.0.53)
;; WHEN: Sun Apr 03 13:56:06 UTC 2022
;; MSG SIZE  rcvd: 74
```

このドメインを使って`certbot`コマンドでHTTP-01チャレンジを実行します。次のコマンドを実行してください。

```
sudo certbot certonly --manual --preferred-challenges=http -d ouchi.168-138-44-162.sslip.io
```

次のようなログが出力されます。

```
Saving debug log to /var/log/letsencrypt/letsencrypt.log
Renewing an existing certificate for ouchi.168-138-44-162.sslip.io

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Create a file containing just this data:

pjIEJhwlIrhfL-iBjH-aG61DxHL_XphmI9SQZyEfbvc.MjuiAvK-KomEEtcr9MIT-l17-F-qvmbk_BKj4wz_vfI

And make it available on your web server at this URL:

http://ouchi.168-138-44-162.sslip.io/.well-known/acme-challenge/pjIEJhwlIrhfL-iBjH-aG61DxHL_XphmI9SQZyEfbvc

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Press Enter to Continue
```

`http://ouchi.168-138-44-162.sslip.io/.well-known/acme-challenge/pjIEJhwlIrhfL-iBjH-aG61DxHL_XphmI9SQZyEfbvc` というURLにアクセスされるt `pjIEJhwlIrhfL-iBjH-aG61DxHL_XphmI9SQZyEfbvc.MjuiAvK-KomEEtcr9MIT-l17-F-qvmbk_BKj4wz_vfI` という文字列を返せれば良いです。
これを実現するために次ように`tsunagu.acme-challenge`オプションを使用してください。`tsunagu.acme-challenge`オプションを自分の環境に合わせて変更し、`certbot`を実行したターミナルとは別のターミナルで次のコマンドを実行してください。

```
sudo tsunagu-server --server.port=80 --management.server.port=0 --tsunagu.acme-challenge.pjIEJhwlIrhfL-iBjH-aG61DxHL_XphmI9SQZyEfbvc=pjIEJhwlIrhfL-iBjH-aG61DxHL_XphmI9SQZyEfbvc.MjuiAvK-KomEEtcr9MIT-l17-F-qvmbk_BKj4wz_vfI
```

しばらくすると`certbot`を実行したターミナルで次のようなログが出力されます。

```
Successfully received certificate.
Certificate is saved at: /etc/letsencrypt/live/ouchi.168-138-44-162.sslip.io/fullchain.pem
Key is saved at:         /etc/letsencrypt/live/ouchi.168-138-44-162.sslip.io/privkey.pem
This certificate expires on 2022-07-02.
These files will be updated when the certificate renews.

NEXT STEPS:
- This certificate will not be renewed automatically. Autorenewal of --manual certificates requires the use of an authentication hook script (--manual-auth-hook) but one was not provided. To renew this certificate, repeat this same certbot command before the certificate's expiry date.

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
If you like Certbot, please consider supporting our work by:
 * Donating to ISRG / Let's Encrypt:   https://letsencrypt.org/donate
 * Donating to EFF:                    https://eff.org/donate-le
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
```

証明書が `/etc/letsencrypt/live/ouchi.168-138-44-162.sslip.io` いかに作成されました。

この証明書を使用してTsunagu Serverを実行するには次のコマンドを実行してください。

```
sudo tsunagu-server --server.port=443 --tsunagu.tls.crt=/etc/letsencrypt/live/ouchi.168-138-44-162.sslip.io/fullchain.pem --tsunagu.tls.key=/etc/letsencrypt/live/ouchi.168-138-44-162.sslip.io/privkey.pem --tsunagu.token=CHANGEME
```

https://ouchi.168-138-44-162.sslip.io にアクセスしてブラウザのアドレスバーに鍵マークが出ればOKです。

<img width="1024" alt="image" src="https://user-images.githubusercontent.com/106908/161431241-84f8f9ac-194b-4cdd-b223-5dcc65d8a7bc.png">

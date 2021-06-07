Para HP necesitamos configurar principalmente dos urls.

1. hushpuppies.pe

A record, hushpuppies.pe
```
TXT	@	juntoz-asw-hushpuppies-web-production.azurewebsites.net
A	@	104.45.154.200
```
2. CNAME www.hushpuppies.pe

```
CNAME www to juntoz-asw-hushpuppies-web-production.azurewebsites.net
```

3. checkout.hushpuppies.pe

```
Type	Host	Value
CNAME	www or subdomain	juntoz-asw-checkout-web-production.azurewebsites.net
```

4. certificado wildcard ssl para que soporte hushpuppies.pe y *.hushpuppies.pe

1, 2, 3 ellos tienen configurar su DNS
4 tienen que comprar su certificado digital y enviarnos los archivos (en namecheap.com al comprarlo reciben un email con un zip).
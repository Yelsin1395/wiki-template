Para crear un certificado self-signed para pruebas seguimos el siguiente procedimiento.

Notar que este certificado NO se puede usar en el browser, igual lo detecta como invalido asi que solo servira en algunos escenarios.

1. Grabar este script como archivo genpfx.ps1

```
# Create and export a self-signed certificate
# https://4sysops.com/archives/create-a-self-signed-certificate-with-powershell/
# Script by Tim Buntrock

# automatically generates the wildcard and naked domain
$YearsToExpire = 10
# In Subject set the wildcard, while in DNSName set the naked and wilcard
# This TextExtension is needed by Azure (means server authentication)
$Cert = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -subject "*.juntozstagingv2.com" -dnsname "juntozstagingv2.com,*.juntozstagingv2.com" -NotAfter (Get-Date).AddYears($YearsToExpire) -TextExtension "2.5.29.37={text}1.3.6.1.5.5.7.3.1"
# Set password to export certificate
$pw = ConvertTo-SecureString -String "3@gl3c3rt-1641" -Force -AsPlainText
# Get thumbprint
$thumbprint = $Cert.Thumbprint
# Export certificate
Export-PfxCertificate -cert cert:\localMachine\my\$thumbprint -FilePath my.pfx -Password $pw
```

1. Modificar el archivo en los siguientes puntos:
--Subject: usar el wildcard
--dnsname: usar el wildcard y el naked
--Password: setear un password distinto y esto guardarlo

2. Correr el script y se generara un archivo my.pfx en la misma carpeta. Ir a CMD.
```
> powershell
> set-executionpolicy unrestricted
> .\genpfx.ps1
```
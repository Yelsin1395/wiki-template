1. Ir al directorio `Web.Juntoz\.vs\config`
2. Abrir el archivo `applicationhost.config`
3. Modificar el archivo cambiando la linea de `bindings` perteneciente al `site name="Site"`:


```
           <site name="Site" id="2">
                <application path="/" applicationPool="Clr4IntegratedAppPool">
                    <virtualDirectory path="/" physicalPath="{{PhysicalPath}}" />
                </application>
                <bindings>
                    <binding protocol="http" bindingInformation="*:60000:*" /> <---- IMPORTANTE -->
                    <binding protocol="https" bindingInformation="*:44384:localhost" />
                </bindings>
            </site>
```

A tener en cuenta:
- Correr VS como administrador
- Si tienes problemas usando * como dominio y quieres probar solo con un subdomain, agregar linea:
`<binding protocol="http" bindingInformation="*:60000:bottplie.localhost" />`

## A tomar en cuenta al correr en localhost: ##
- Los Cookies no van a funcionar con subdominios!!
Esto es debido a que el cookieName (configurado en appsettings bajo `SiteDomainCookie` ) no soporta .localhost
Por ende por ej. add to cart no va funcionar desde el subdominio.
La unica manera de hacerlo funcionar es registrar en tu hosts file un dominio ficticio, ie. juntoz.localhost.com y con eso registrar los cookies.


# Registro de datos del nuevo cliente para *Identity Server* en la base de datos

Se debe ingresar los siguientes registros en la base de datos **IdSrv4Db**. A continuación se mostrará los *scripts* que se necesitan para registrar un nuevo cliente en la base de datos de *Asp.Net Identity*

1. Insertar un nuevo cliente. Tener en cuenta que en este *script* se debe cambiar el **ClientId**, **ClientName** y **ClientUri***. Los demás valores pueden ingresarse tal cual se muestran en el *script* de ejemplo

```sql
INSERT INTO Clients (AbsoluteRefreshTokenLifetime, AccessTokenLifetime, AccessTokenType, AllowAccessTokensViaBrowser, AllowOfflineAccess, AllowPlainTextPkce, AllowRememberConsent, AlwaysIncludeUserClaimsInIdToken, 
AlwaysSendClientClaims, AuthorizationCodeLifetime, ClientId, ClientName, ClientUri, EnableLocalLogin, [Enabled], IdentityTokenLifetime, IncludeJwtId, LogoUri, LogoutSessionRequired, LogoutUri, PrefixClientClaims, 
ProtocolType, RefreshTokenExpiration, RefreshTokenUsage, RequireClientSecret, RequireConsent, RequirePkce, SlidingRefreshTokenLifetime, UpdateAccessTokenClaimsOnRefresh)
VALUES(2592000, 3600, 0, 0, 1, 0, 1, 0, 0, 300, 'm2912434ae844eb48be398d5bfa77c3806c7a1bcc1bf44ee8acd8cdbf7e1d5e16', 'kidsmadehere', 'https://juntoz-asw-kidsmadehere-web-staging.azurewebsites.net/', 1, 1, 300, 0, NULL, 1, NULL, 1, 
'oidc', 1, 1, 1, 0, 0, 1296000, 0)
```
2. Agregamos los valores necesarios en la tabla **ClientScopes**. Tener en cuenta que el valor de la columna *ClientId* es igual al valor *Id* del registro insertado en la tabla *Clients* líneas arriba.
```sql
INSERT INTO ClientScopes (ClientId, Scope) VALUES (52, 'openid')
INSERT INTO ClientScopes (ClientId, Scope) VALUES (52, 'profile')
INSERT INTO ClientScopes (ClientId, Scope) VALUES (52, 'offline_access')
INSERT INTO ClientScopes (ClientId, Scope) VALUES (52, 'juntoz')
```
3. Adicionamos un registro en la tabla **ClientGrantTypes **
```sql
INSERT INTO ClientGrantTypes (ClientId, GrantType) VALUES (52, 'hybrid')
```
4. Los registros ingresados en la tabla **ClientRedirectUris** Son importantes para que el servidor *Identity Server* sepa a que url regresar luego de crear la sesión de un usuario.
```sql
INSERT INTO ClientRedirectUris (ClientId, RedirectUri) VALUES (52, 'http://localhost:60000/signin-oidc')
INSERT INTO ClientRedirectUris (ClientId, RedirectUri) VALUES (52, 'https://juntoz-asw-kidsmadehere-web-staging.azurewebsites.net/signin-oidc')
INSERT INTO ClientRedirectUris (ClientId, RedirectUri) VALUES (52, 'http://juntoz-asw-kidsmadehere-web-staging.azurewebsites.net/signin-oidc')
INSERT INTO ClientRedirectUris (ClientId, RedirectUri) VALUES (52, 'https://my.juntoz-staging.com/signin-oidc')
```
5. Adicionamos un registro en la tabla **ClientSecrets** con estos datos el nuevo cliente podrá ser reconocido en el servidor *Identity Server*
```sql
INSERT INTO ClientSecrets (ClientId, [Description], Expiration, [Type], [Value])
VALUES (52, NULL, NULL, 'SharedSecret', '94vyklnQUoGnTUEdg8hv9QvkxfoRhpwMeq6utTB0MxE=')
```

6. Los registros ingresados en la tabla **ClientPostLogoutRedirectUris** sirven para que *Identity Server* redireccione a la url registrada luego de cerrar la sesión.
```sql
INSERT INTO ClientPostLogoutRedirectUris (ClientId, PostLogoutRedirectUri) VALUES (52, 'http://localhost:60000/signout-callback-oidc')
INSERT INTO ClientPostLogoutRedirectUris (ClientId, PostLogoutRedirectUri) VALUES (52, 'https://juntoz-asw-kidsmadehere-web-staging.azurewebsites.net/signout-callback-oidc')
INSERT INTO ClientPostLogoutRedirectUris (ClientId, PostLogoutRedirectUri) VALUES (52, 'http://juntoz-asw-kidsmadehere-web-staging.azurewebsites.net/signout-callback-oidc')
INSERT INTO ClientPostLogoutRedirectUris (ClientId, PostLogoutRedirectUri) VALUES (52, 'https://my.juntoz.com/signout-callback-oidc')
```

# Registro de datos del nuevo cliente para *Identity Server* en los *settings* de la aplicación que usará *Identity Server*
En los *settings* de la aplicación web debemos registrar el *ClientId* creado y registrado en la base de datos de *Identity Server*.

```json
{
    "ApplicationInsights": {
        "InstrumentationKey": "a178462f-afea-4021-8a6c-412f3dd3cf68"
    },
    "IdentitySettings": {
        "Authority": "https://account.juntoz-staging.com/",
       "ClientId": "m2912434ae844eb48be398d5bfa77c3806c7a1bcc1bf44ee8acd8cdbf7e1d5e16",
        "ClientSecret": "fb5d61b0328040f9921668f98ed30f3051149a81ef064094a1f8ca9a70046084"        
    },
    "WhiteLabelSettings": {
        "CheckOutUrl": "https://checkout.kidsmadehere.com/",
        "CountryId": 604,
        "CultureCode": "es-PE",
        "GoogleTagManagerCode": "GTM-TSP77FS",
        "MetaDescription": "KidsMadeHere description",
        "SiteId": 1249,
        "SiteName": "Kidsmadehere",
        "SiteUrl": "https://www.kidsmadehere.com/",
        "StoreIds": [ 1249 ],
        "Email": "info@kidsmadehere.pe"
    }
}
```

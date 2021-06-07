En Lifemiles, quieren que desde lifemiles.com se pueda hacer una busqueda de productos a su web.

Lograrlo es moderadamente facil.

1. Crear un client secondary (estos solo permiten client_credentials).

Como? Utilizando `https://api.jzte.xyz/client/create/secondary` o en el futuro, haciendolo a traves de Site Central por el rol `siteadmin`.

Estos client NO tendran un usuario asociado.

Resultado: `Client 957cc2f0543a11eba342d1eaea8dbd26 (staging)`

2. Crear un client secret.

Se usa un endpoint del api client que es `https://api.jzte.xyz/client/generate/secret`

IMPORTANTE: El client secret que el endpoint retorna, lo retorna una vez y nunca mas, por lo tanto debe guardarse rapidamente de manera segura

Resultado: `Secret a7527f10543a11eba342d1eaea8dbd26 (staging)`

3. Para crear un token se debe usar este endpoint
```
POST https://id.jzte.xyz/connect/token
grant_type client_credentials
client_id xxx
client_secret xxx
```
4. Una vez que el token es generado, hay que entonces llamar al endpoint de busqueda
```
GET https://api.jzte.xyz/p/product/v2/search?q=xxx

Headers:
Authorization Bearer {{token}}
X-JZ-SITE b58ac3ec05f44414b674d9244e4538a0
```
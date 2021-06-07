# common-ext app service

Hay un app service llamado common-ext que adentro tiene una aplicacion php **de la que no tenemos codigo fuente**.

Segun Fabricio Rojas, el servicio se llama desde api-payment-int cuando el response viene desde EPAYCO (Colombia) para desencriptar algunos valores.

TO DO: mover esa logica dentro de payment-int sin necesidad de apuntar a un endpoint que no se mantiene.


El procedimiento es el siguiente:

1. Hacer una copia de `ApplicationInsights.config` si se le ha hecho algún cambio manual.

1. En el `Solution Explorer`, click derecho sobre el proyecto y seleccionar del menú `Manage NuGet packages`.
<center>

![Items.png](/.attachments/Items-3e6cf0eb-3bcf-4dc4-ab64-d8b149b3a69c.png)

</center>

3. Seleccione la opción `Browse`.

![Items2.png](/.attachments/Items2-0cfd6df7-91fb-451d-a87e-edcd0cf7e538.png)


4. Buscar `Microsoft.ApplicationInsights.Web`, elegirla y seleccionar `Install/Update`. Se necesita al menos la versión 2.2.0 (o superior).

1. Si se había hecho cambios en `ApplicationInsights.config` volveros a hacer. La mayoría de los cambios que hace app insights tienen que ver con módulos que se han quitado y otros que ahora son parametrizables. 

1. Hacer rebuild a la solución.

1. Subir con el proceso normal de build/release,

Verificar que el InstrumentionKey del ApplicationInsight.config sea el mismo que el Azure:
![Items3.png](/.attachments/Items3-94f7be27-45c1-4716-8cc1-f2bff659c746.png)
 


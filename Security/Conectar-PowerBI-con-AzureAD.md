En 2019-03 movimos todas las cuentas ieholding y juntoz del antiguo Azure AD al oficial (Juntoz Peru).

Esto trajo la dificultad que corto el acceso a PowerBI, ademas que hubo cruces con la cuenta de Azure AD y que la cuenta era Office 365.

1. Borramos la cuenta `bi-bot@ieholding.com` y eso hizo que ya no fuera Azure AD.
2. Daniel Huerta creo la cuenta en PowerBI nuevamente y esta vez creo una cuenta Office 365.
3. Al estar esta cuenta con el dominio `@ieholding.com` al parecer si la asocio al Azure AD.
4. Para este momento, efectivamente SI vimos la cuenta en Azure AD y ahora estaba asociada a una licencia de PowerBI.
5. Luego fui a Azure AD/Enterprise Relationships e increiblemente PowerBI Service estaba ahi (o de repente siempre estuvo ahi).
6. Entre a PowerBI Service y en Properties mostraba:
Allow Sign In : Disabled
![Items.png](/.attachments/Items-8b56bdc2-8ae8-411e-a05b-ad6e95d040fc.png)

7. Lo setee en Enabled.
8. Ahora el usuario bi-bot@ieholding.com pudo entrar a PowerBI normalmente.
9. Adicionalmente configure como owner a `daniel@ieholding.com` y a `katlim@ieholding.com`.
![Items (1).png](/.attachments/Items%20(1)-80266477-6591-454a-8a90-18b3fc1f68a2.png)

NOTA: no deberia usarse una cuenta generica `bi-bot`. Es una mala practica de seguridad.

10. Adicionalmente hubo un problema. `bi-bot` no estaba agregado a ningun grupo en Azure por lo que no podia acceder al servidor en si. Se creo un grupo `Juntoz-Azure-BI-PowerBI` y se agrego como cuenta en `SalesDB_BI` y se le dio permiso `db_datareader` y con eso ya funciono.
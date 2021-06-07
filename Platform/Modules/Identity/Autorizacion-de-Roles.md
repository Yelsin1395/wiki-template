En la nueva plataforma de Juntoz, hemos refactorizado los roles para que realmente cumplan el papel importante que se les necesita.

Los roles son clave para que tengamos una plataforma segura y predecible, especialmente en una plataforma que tendra miles de endpoints.

La nueva estructura de roles es ahora jerarquica y se han creado muchos mas roles para que la seguridad sea mas granular y podamos separar bien lo que puede hacer y no cada rol.

**Esta nueva logica esta encapsulada en el paquete @juntoz/authorize en el repo Juntoz.Api.Infra/Authorize.**

Todas las APIs tienen acceso a este paquete y pueden usar la logica segun sea necesario. En NestJS se ejecuta a traves de los RolesGuard. Y el claim `role` del token es el que se usa para verificar la seguridad de los endpoints.

La lista de roles actuales es (2020-07):
``` js
export const roleList = [
    {
        "id": "sysadmin",
        "description": "god level admin for the whole system",
        "level": "sys",
        "god": true
    },
    {
        "id": "systech",
        "description": "juntoz tech team to do support or execute certain tasks",
        "level": "sys"
    },
    {
        "id": "sysfinance",
        "description": "juntoz tech finance department to bill the site owners",
        "level": "sys"
    },
    {
        "id": "sysfinanceadmin",
        "description": "admin for sysfinance",
        "level": "sys"
    },
    {
        "id": "syssiterep",
        "description": "juntoz tech customer support rep for site owners",
        "level": "sys",
        "isCustRep": true
    },
    {
        "id": "syssiterepadmin",
        "description": "admin for syssiterep",
        "level": "sys",
        "isCustRep": true
    },
    {
        "id": "siteadmin",
        "description": "admin user belonging to the site owner organization to maintain site level settings",
        "level": "site"
    },
    {
        "id": "sitecms",
        "description": "regular user belonging to the site owner organization to maintain cms for the site",
        "level": "site"
    },
    {
        "id": "sitecmsadmin",
        "description": "admin for sitecms",
        "level": "site"
    },
    {
        "id": "sitecatalog",
        "description": "regular user belonging to the site owner organization to maintain catalog of the site",
        "level": "site"
    },
    {
        "id": "sitecatalogadmin",
        "description": "admin for sitecatalog",
        "level": "site"
    },
    {
        "id": "sitesale",
        "description": "regular user belonging to the site owner organization to maintain sales settings for the site",
        "level": "site"
    },
    {
        "id": "sitesaleadmin",
        "description": "admin for sitesale",
        "level": "site"
    },
    {
        "id": "sitefinance",
        "description": "regular user belonging to the site owner organization to maintain billing and finance settings for the site",
        "level": "site"
    },
    {
        "id": "sitefinanceadmin",
        "description": "admin for sitefinance",
        "level": "site"
    },
    {
        "id": "sitelogistic",
        "description": "regular user belonging to the site owner organization to maintain logistics operations and settings for the site",
        "level": "site"
    },
    {
        "id": "sitelogisticadmin",
        "description": "admin for sitelogistic",
        "level": "site"
    },
    {
        "id": "siteenduserrep",
        "description": "regular user belonging to the site owner organization to help site end users (as customer rep)",
        "level": "site",
        "isCustRep": true
    },
    {
        "id": "siteenduserrepadmin",
        "description": "admin for siteenduserrep",
        "level": "site",
        "isCustRep": true
    },
    {
        "id": "sitemerchantrep",
        "description": "regular user belonging to the site owner organization to help site merchants (as customer rep)",
        "level": "site",
        "isCustRep": true
    },
    {
        "id": "sitemerchantrepadmin",
        "description": "admin for sitemerchantrep",
        "level": "site",
        "isCustRep": true
    },
    {
        "id": "siteint",
        "description": "special role for integration reference user",
        "level": "site"
    },
    {
        "id": "merchantcms",
        "description": "regular user belonging to a merchant organization to operate merchant cms",
        "level": "merchant"
    },
    {
        "id": "merchantcatalog",
        "description": "regular user belonging to a merchant organization to operate merchant catalog",
        "level": "merchant"
    },
    {
        "id": "merchantsale",
        "description": "regular user belonging to a merchant organization to operate merchant sales",
        "level": "merchant"
    },
    {
        "id": "merchantlogistic",
        "description": "regular user belonging to a merchant organization to operate merchant logistic",
        "level": "merchant"
    },
    {
        "id": "merchantadmin",
        "description": "admin user belonging to a merchant organization to maintain merchant level settings",
        "level": "merchant"
    },
    {
        "id": "merchantint",
        "description": "special role for integration reference user",
        "level": "merchant"
    },
    {
        "id": "logisticuser",
        "description": "regular user belonging to a logistic organization to advance order status",
        "level": "logistic"
    },
    {
        "id": "logisticadmin",
        "description": "admin user belonging to a logistic organization to maintain logistic level settings",
        "level": "logistic"
    },
    {
        "id": "user",
        "description": "lowest role that every user must have to login to the platform",
        "level": "user"
    },
    {
        "id": "guest",
        "description": "guest role used to generate dummy users for anonymous navigation in the ecommerce website",
        "level": "user"
    }
];

```

Cada rol esta definido con su id, descripcion y nivel (level). El nivel puede ser:


| Nivel | Descripcion |
|--|--|
| sys | El rol se le debe asignar a aquellas personas administradores de la plataforma como un todo |
| site | El rol se le debe asignar a aquellas personas que operan y administran UN site |
| merchant | El rol se le debe asignar a aquellas personas que pertenecen a un merchant DENTRO de un site |
| logistic | El rol se le debe asignar a aquellas personas que pertenecen a un logistic o carrier DENTRO de un site |
| user | Estos roles son aquellos que se asignan automaticamente para que el usuario pueda usar las webs ecommerce |

Ademas un rol puede tener algunos otros atributos:
- god: este rol puede hacer todo. Sin embargo este atributo creo que estara en desuso dado que tenemos el arbol jerarquico de roles y que todos llegan al god (sysadmin).
- isCustRep: este rol es para indicar que representa a un customer representative. Este es un flag especial que agrupa a todos los custreps sin importar el nivel dado que estos roles deben tener un tratamiento especial en donde deben poder hacer algunas tareas administrativas especiales (y delicadas como ver y tocar la informacion del usuario), pero que no son administradores de la plataforma.

*NOTA: Aunque estos atributos estan definidos en el rol, por ahora realmente no se usan. El `god` no es necesario dado que todos los roles siempre terminan dentro de `sysadmin`. Y el isCustRep igual se maneja a traves de la jerarquia.*

Cuando un usuario es creado o un rol es asignado o eliminado, el sistema automaticamente asignara el nivel mas alto que un usuario tiene en base a sus roles.

* Asi, si el usuario tiene solamente el rol [`user`], su nivel sera `user` porque es el "maximo" obtenido.
* Pero si el usuario tiene los roles [`user`, `siteadmin`], su nivel sera `site`.
* Si el usuario tiene los roles [`user`, `merchantadmin`] su nivel sera `merchant`.
* Si el usuario tiene algun rol como [`systech`], su nivel sera `sys`.
* Etc.

## Jerarquia
Una parte novedosa de este modelo es que hemos podido organizar los roles de manera jerarquica, lo que ayudara a que el mantenimiento de la seguridad sea facil y eficiente.

Esta es la jerarquia:
``` js
export const roleTree = {
    "sysadmin": {
        "systech": null,
        "sysfinanceadmin": {
            "sysfinance": null
        },
        "syssiterepadmin": {
            "syssiterep": {
                "siteenduserrepadmin": {
                    "siteenduserrep": null
                },
                "sitemerchantrepadmin": {
                    "sitemerchantrep": {
                        "merchantadmin": null
                    }
                }
            }
        },
        "siteadmin": {
            "sitecmsadmin": {
                "sitecms": null
            },
            "sitecatalogadmin": {
                "sitecatalog": null
            },
            "sitesaleadmin": {
                "sitesale": null
            },
            "sitefinanceadmin": {
                "sitefinance": null
            },
            "sitelogisticadmin": {
                "sitelogistic": null
            },
            "siteenduserrepadmin": {
                "siteenduserrep": null
            },
            "sitemerchantrepadmin": {
                "sitemerchantrep": null
            },
            "siteint": null,
            "merchantadmin": {
                "merchantcms": null,
                "merchantcatalog": null,
                "merchantsale": null,
                "merchantlogistic": null,
                "merchantint": null,
            },
            "logisticadmin": {
                "logisticuser": null
            }
        },
        "user": null,
        "guest": null
    }
}
```

Como pueden ver, la raiz del arbol es `sysadmin` incluso de `user` y `guest`. `sysadmin` es el `god` de los roles.

Que significa que un rol contenga al otro? Significa que tiene una dependencia de "jefatura" o de "override".

Por ejemplo, el `merchantadmin` tiene poder sobre `merchantcatalog`. Esto quiere decir que toda accion que `merchantcatalog` puede hacer, el `merchantadmin` tambien lo podra hacer.

Y asi entonces el `siteadmin` contiene a `merchantadmin` por lo tanto podra hacer lo mismo que `merchantadmin` y `merchantcatalog`. Y asi sucesivamente.

Esto quiere decir que si un endpoint se define asi:
```
.get('/product/:productId', authorize([merchantcatalog]), getProduct)
```
- El token del usuario Juan que tiene el rol `merchantcatalog` podra llamarlo.
- El token del usuario Maria que tiene el rol `merchantadmin` podra llamarlo.
- El token del usuario Jose que tiene el rol `siteadmin` podra llamarlo.
- Pero el token del usuario Pedro que tiene el rol `merchantsale` NO podra llamarlo.

Obviamente la jerarquia **solo funciona hacia arriba**. Si un endpoint es para `merchantadmin`, el rol `merchantcatalog` NO podra llamarlo.

## Customer Representatives
En el caso de los customer reps, la jerarquia funciona pero se extiende un poco mas alla.

Estos son los roles que se han definido como isCustRep:
```
  syssiterepadmin:
    syssiterep:
      siteenduserrepadmin:
        siteenduserrep: null
      sitemerchantrepadmin:
        sitemerchantrep: null
---
  siteadmin:
    siteenduserrepadmin:
      siteenduserrep: null
    sitemerchantrepadmin:
      sitemerchantrep: null
```

| Rol | Descripcion |
|-|-|
| sitemerchantrep | Rol que pertenece al Site y da soporte al merchant para operar su tienda |
| sitemerchantrepadmin | Jefe del sitemerchantrep |
| siteenduserrep | Rol que pertenece al Site y da soporta al usuario final (el que compra) |
| siteenduserrepadmin | Jefe del siteenduserrep |
| syssiterep| Rol que pertenece a la plataforma y da soporta a los usuarios que operan el Site |
| syssiterepadmin| Jefe del syssiterep |

Ejemplos reales:
* `sitemerchantrep`: Hoy en Juntoz.com no existe este rol, pero podrian ser los KAM que ayudan a operar a los merchant.
* `siteenduserrep`: Son los miembros de SAC y `siteenduserrepadmin` seria Michael Ruiz.
* `syssiterep`: Seria el departamento de soporte tecnico L1 y L2 de JuntozTech.

Y como se puede ver en el grafico, los custrep tienen doble jerarquia. Esto es para que los customer representatives del SYSTEM pueden ayudar a todos los niveles inferiores de customer representatives.

## Roles de integracion (siteint, merchantint)
Estos roles `siteint` y `merchantint` NO SON APLICABLES a usuarios regulares. Se deben aplicar solo a aquellos usuarios que estan asociados a `Client`'s de integracion. Un `Client` es una entidad que representa una aplicacion externa que interactua con nuestra plataforma. Es un concepto relacionado a openid.

Crear un registro en la tabla `IdentityDb.Site`.

Crear un registro en la tabla `IdentityDb.SiteMeta`. Esta tabla es muy importante dado que contiene todos los settings de infrastructura necesarios para que el Site ejecute de manera aislada y segregada.

Hay que crear entonces
- Indices de algolia para producto, category, brand y store. NO CREAR UNA NUEVA APP. Debe usarse JuntozTech como application de algolia.
- Indice de algolia para ordenes.
- Un server de postmark por site y ingresar el servername y el apikey aqui debajo.

Todas las demas piezas de infrastructura se pueden compartir dado que permiten tener rutas segregadas por site.

```
{
    "id": "b58ac3ec05f44414b674d9244e4538a0",
    "juntoz": "juntoz",

    "product_index_name": "stg_catalog_product_b58ac3ec05f44414b674d9244e4538a0",
    "product_index_application": "N7XJ7FJ9CM",
    "product_index_apikey_search": "5f84a53b8300213152d7d412170b9cb6",
    "product_index_apikey_write": "77050b422083d11232124e0ea6a9f8e7",

    "brand_index_name": "stg_catalog_brand_b58ac3ec05f44414b674d9244e4538a0",
    "brand_index_application": "N7XJ7FJ9CM",
    "brand_index_apikey_search": "5f84a53b8300213152d7d412170b9cb6",
    "brand_index_apikey_write": "77050b422083d11232124e0ea6a9f8e7",

    "category_index_name": "stg_catalog_category_b58ac3ec05f44414b674d9244e4538a0",
    "category_index_application": "N7XJ7FJ9CM",
    "category_index_apikey_search": "5f84a53b8300213152d7d412170b9cb6",
    "category_index_apikey_write": "77050b422083d11232124e0ea6a9f8e7",

    "store_index_name": "stg_store_b58ac3ec05f44414b674d9244e4538a0",
    "store_index_application": "N7XJ7FJ9CM",
    "store_index_apikey_search": "5f84a53b8300213152d7d412170b9cb6",
    "store_index_apikey_write": "77050b422083d11232124e0ea6a9f8e7",

    "product_image_storage_uri": "https://jzcatalogstg.blob.core.windows.net",
    "product_image_storage_key": "HMWoj4jrekPCDxeC3R1ZHcm50ZksV4FOCogeAuGxy1mqdPdjeE7VZL7EdowAfX22iAi2glJzQmhGv1Hix6dHHw==",
    "product_image_storage_account": "jzcatalogstg",
    "product_image_storage_container": "products",

    "brand_image_storage_uri": "https://jzcatalogstg.blob.core.windows.net",
    "brand_image_storage_key": "HMWoj4jrekPCDxeC3R1ZHcm50ZksV4FOCogeAuGxy1mqdPdjeE7VZL7EdowAfX22iAi2glJzQmhGv1Hix6dHHw==",
    "brand_image_storage_account": "jzcatalogstg",
    "brand_image_storage_container": "brands",

    "store_image_storage_uri": "https://jzcatalogstg.blob.core.windows.net",
    "store_image_storage_key": "HMWoj4jrekPCDxeC3R1ZHcm50ZksV4FOCogeAuGxy1mqdPdjeE7VZL7EdowAfX22iAi2glJzQmhGv1Hix6dHHw==",
    "store_image_storage_account": "jzcatalogstg",
    "store_image_storage_container": "stores",

    "longprocess_storage_uri": "https://jzlongprocstg.blob.core.windows.net",
    "longprocess_storage_key": "mAch74sneclkHxE8/h7+8pKwMWFFftz4pz7sx1jk9408WlDWOs56dg8mqjH19cEsiY1Rwrn28DTuT+r9XsPWmA==",
    "longprocess_storage_account": "jzlongprocstg",
    "longprocess_storage_container": "longproc",

    "cms_storage_uri": "https://jzcmsstg.blob.core.windows.net",
    "cms_storage_key": "kmMWYmt9RZHakAgb2ZdNWb975oO3EAL+YZmP4Jl+FTeLZa73fs1l1fgKf0qjGys3u67XOeOUavdRHkRfMG3nUg==",
    "cms_storage_account": "jzcmsstg",
    "cms_storage_container": "media"
}
```

Crear registro en `IdentityDb.Client` para la web y configurarlo como interactivo.

Crear registros en `NotificationDb.NotificationDestination` para que los eventos generen las notificaciones necesarias.

Crear registros en `NotificationDb.NotificationTemplate` para que las notificaciones tengan templates de donde generarse.

Configurar los indices de algolia para que use facets de categorias, marcas, tiendas, atributos (color, talla, capacidad, estilo).



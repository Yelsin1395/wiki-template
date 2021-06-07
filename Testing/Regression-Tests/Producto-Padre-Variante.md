**Validacion en Cart**

- PPadre - Apagar producto por BD (ispublished=0), verificar que si está en Cart no llegue a checkout
Tablas: Product y ProductinStore
- PVariante - Apagar producto por BD (ispublished=0), verificar que si está en Cart no llegue a checkout
Tablas: Product y ProductinStore
- PPadre/PVariante - Deshabilitar producto por BD (isavailable=0), verificar que si está en Cart no llegue a checkout
- PPadre - Eliminar producto por BD (isdelete=1), verificar que si está en Cart no llegue a checkout
Tablas: Product y ProductinStore
- PVariante - Eliminar producto por BD (isdelete=1), verificar que si está en Cart no llegue a checkout
Tablas: Product y ProductinStore
- PPadre/PVariante - Apagar producto en MC, verificar que si está en Cart no llegue a checkout
- PPadre/PVariante - Eliminar producto en MC, verificar que si está en Cart no llegue a checkout

**Validaciones en PPage**

- PPadre - Apagar producto por BD (ispublished=0), Verificar que si está PPage no pase a cart
  Tablas: Product y ProductinStore
- PVariante - Apagar producto por BD (ispublished=0), Verificar que si está PPage no pase a cart
  Tablas: Product y ProductinStore
- PPadre/PVariante - Deshabilitar producto por BD (isavailable=0), Verificar que si está PPage no pase a cart

- PPadre - Eliminar producto por BD (isdelete=1), Verificar que si está PPage no pase a cart
  Tablas: Product y ProductinStore
- PVariante - Eliminar producto por BD (isdelete=1), Verificar que si está PPage no pase a cart
  Tablas: Product y ProductinStore
- PPadre/PVariante - Apagar producto en MC, Verificar que si está PPage no pase a cart
- PPadre/PVariante - Eliminar producto en MC, Verificar que si está PPage no pase a cart


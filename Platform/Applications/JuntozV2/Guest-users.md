## Sobre el AuthCustomMiddleware y Guest users:
Una de las funcionalidades que debemos manejar es poder traquear usuarios no registrados, ie. guest users. Lo que hacemos es que cuando un usuario no registrado es detectado (por sus cookies), le auto generamos un usuario con un `Userid` Guid, y el pais.
Esto lo hacemos en todos los requests usando un Middleware llamado `AuthCustomMiddleware`.

El servicio que maneja el logueo del usuario se llama `GuestUserService` y esta abstraido para poderse llamar de otros controladores en caso sea necesario y por SoC.

Las dos cookies que importan son: 
- `.AspNetCore.Cookies` - parte del .net sign in.
- `__guest__` el cookie guest. el valor es el UserId guid del guest user.

El role del usuario Guest es `guest`. Esto nos permite diferenciar a los usuarios guest de los registrados. Por ejemplo en el menu de Iniciar sesion, si el user es guest, queremos mostrar siempre el link para hacer login.

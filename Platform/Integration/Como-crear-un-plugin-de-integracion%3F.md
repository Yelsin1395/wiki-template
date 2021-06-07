En este wiki veremos como crear un plugin de integracion. Lee el wiki de arquitectura para entender mejor como se maneja esto.

# Crear proyecto y nombrarlo
Para crearlo usar `npm init -y`.

El nombre es muy importante y debe tener el prefijo `@juntoz/plugin-`. Esto es para que el `directory.ModuleFinder` lo pueda encontrar.

El nombre del plugin en si, debe seguir el formato:
`${entityName}-${pluginDirection}${pluginAction}-${pluginTarget}-${pluginAuthor}`

Es decir, el `package.json`/`name` debe ser `@juntoz/plugin-stock-outbound-get-prestashop-author` mientras que el pluginFullName por lo tanto ya es `stock-outbound-get-prestashop-author`.

# Instalar el paquete `@juntoz/integration-plugin-base`

Usar
```
npm i @juntoz/integration-plugin-base
```
(este proyecto esta dentro del npm privado de juntoz actualmente, sin embargo pronto estara en github y en npm publico).

Primero notar lo siguiente: todas las interfaces heredan de Plugin, por lo tanto son varios campos que hay que implementar que provienen de Plugin.

## InboundReceive
Para Inbound Receive (es decir cuando la plataforma de Juntoz recibira el objeto externo de manera proactiva), se debe implementar la interface `PluginInboundReceive`.

```
export interface PluginInboundReceive extends Plugin {
    /**
     * Receives the input from the target system and converts it into a Juntoz object.
     * @param ctx runtime context
     * @param input input coming from source system
     */
    receive(ctx: RuntimeContext, input: TargetObject): Promise<PluginResponseToJuntoz>;
};
```

## InboundPull
Para InboundPull (es decir la plataforma debe obtener un universo de objetos dentro de la plataforma de Juntoz para luego ir al partner externo y obtener informacion acerca de cada uno para luego hacer la actualizacion dentro de la plataforma), se debe implementar la interface `PluginInboundPull `.

Esta interface permite tambien declarar que el plugin permite multiples objetos. Si lo soporta, el plugin es responsable por lo tanto de retornar tambien un array en el output.

```
export interface PluginInboundPull extends Plugin {
    /**
     * Whether the inbound pull can accept multiple inputs or not.
     * If true, the plugin is responsible to return also multiple outputs.
     */
    allowMultiple(): boolean;

    /**
     * Based on the given input Juntoz object, pull the information from the target system, and generate needed Juntoz output objects.
     * @param ctx runtime context
     * @param input Juntoz objects used to process the integration
     */
    pull(ctx: RuntimeContext, input: JuntozObject | JuntozObject[]): Promise<PluginResponseToJuntoz>;
}
```

## OutboundPush
Para OuboundPush (cuando la plataforma Juntoz genera una creacion o actualizacion a un objeto, este debe ser convertido a un formato externo y luego ser enviado al partner externo) se debe implementar la interface `PluginOutboundPush`.

```
export interface PluginOutboundPush extends Plugin {
    /**
     * With the given input Juntoz object, map it to the target schema, and send it to the target system
     * @param ctx runtime context
     * @param input Juntoz object to convert to target schema and be pushed to the target system
     */
    push(ctx: RuntimeContext, input: JuntozObject): Promise<PluginResponseToTarget>;
}
```

## OutboundGet
Para OutboundGet (que es cuando el partner externo llama a un API de integracion para obtener informacion de la plataforma de Juntoz), se debe implementar la interface `PluginOutboundGet`.

Esta interface permite tambien declarar que el plugin permite multiples objetos. Si lo soporta, el plugin es responsable por lo tanto de retornar tambien un array en el output.

```
export interface PluginOutboundGet extends Plugin {
    /**
     * Whether the outbound get can accept multiple inputs or not.
     * If true, the plugin is responsible to return also multiple outputs.
     */
    allowMultiple(): boolean;
    /**
     * With the given input Juntoz object, convert to the target schema
     * @param ctx runtime context
     * @param input Juntoz object to convert to target schema
     */
    get(ctx: RuntimeContext, input: JuntozObject | JuntozObject[]): Promise<PluginResponseToTarget>;
}
```

# Ahora si a crear el plugin (InboundReceive)
Lo primero es implementar una de esas interfaces. Por ejemplo usemos PluginInboundReceive.

Declaramos la clase que implemente esa interface e implementamos todos los campos necesarios.

De nuevo, el nombre es muy importante, la idea es que tenga "redundancias" por muchos lados siempre para estar seguro que el plugin sea el correcto.

## Campos de metadata a implementar
```
class StockInboundReceivePrestashopJuntozPlugin: PluginInboundReceive
{
    entityName: EntityName;
    pluginDirection: PluginDirection;
    pluginAction: PluginAction;
    pluginTarget: string;
    pluginAuthor: string;
    pluginVersion: string;
    constructor() {
        this.entityName = 'stock';
        this.pluginDirection = 'inbound';
        this.pluginAction = 'receive';
        this.pluginTarget = 'prestashop';
        this.pluginAuthor = 'juntoz';
        this.pluginVersion = '1.0.0';
    }
}
```

- entityName: nombre de la entidad asociado a la integracion. Por ahora solo es `stock|price|order|product`.
- pluginDirection: `inbound|outbound`.
- pluginAction: `receive|pull|push|get`.
- pluginTarget: string libre corto que contiene el nombre del sistema integrado: prestashop, magento, sial, rms, etc.
- pluginAuthor: string libre corto que contiene el autor del plugin. Deberia ser el nombre de la empresa, no del developer singular (a menos que el developer sea "la empresa").
- pluginVersion: La version que este plugin representa. Esta parte es un poco tricky dado que hay dos campos de version: en `package.json` y aqui. La idea es que esten coordinados. Hasta sugeriria que esto se convierta en `getter` y se obtenga del `package.json` del paquete (no de la aplicacion).

## Full Name
A esta clase ahora hay que agregarle un metodo importante, tipicamente sera asi.
```
class StockInboundReceivePrestashopJuntozPlugin: PluginInboundReceive
{
    pluginFullName(): string {
        return `${this.entityName}-${this.pluginDirection}${this.pluginAction}-${this.pluginTarget}-${this.pluginAuthor}`;
    }
}
```

El fullname es muy importante dado que es con el que sera registrado el plugin en la subscripcion para poder inyectarlo en la ejecucion del motor de integracion.

## Metadata de Configuracion
Por ultimo, la mayoria de plugins necesitara configuracion que solo el dueno de quien ejecuta la integracion puede saber. Esta configuracion por lo tanto, se registrara dentro de la subscripcion. Sin embargo, dado que los plugins pueden ser muy distintos entre si, cada plugin debera declarar un arreglo de campos de configuracion (en el formato key: string, value: string) que la UI de la subscripcion debera recopilar para que a la hora de ejecutar el plugin, este pueda usar esos parametros.

Algunos plugins no necesitaran configuracion (por ejemplo aquellos que solo hacen mapping de datos de objeto a objeto), asi que el metodo puede retornar un array vacio.

```
class StockInboundReceivePrestashopJuntozPlugin: PluginInboundReceive
{
    pluginConfigMeta(): ConfigField[] {
        return [
        ];
    }
}
```

Cada campo debe ser definido usando la clase `ConfigField`:
```
export class ConfigField {
    name!: string;
    displayName!: string;
    description?: string;
    required?: boolean = true;
}
```

## allowMultiple
Para las interfaces InboundPull y OutboundGet, el plugin tambien debe implementar el metodo allowMultiple().

Si el plugin declara que SI puede permitir multiples, entonces en el metodo de accion, el argumento `input` puede obtener un array o un objeto singular.

Es responsabilidad del plugin de retornar en el campo `result` del response un array tambien.

```
class StockInboundPullPrestashopJuntozPlugin: PluginInboundPull
{
    allowMultiple(): boolean {
        return false;
    }
}
```

## Action!
Cada tipo de integracion tiene un metodo que representa la accion que se ejecutara por el plugin.

```
InboundReceive.receive()
InboundPull.pull()
OutboundPush.push()
OutboundGet.get()
```

La clase debe implementar el metodo que corresponda segun la interface escogida. Y notar que hay metodos que retornan `PluginResponseToJuntoz` o `PluginResponseToTarget` que son dos interfaces de response que se ven identicas pero que el tipo de dato cambia para el campo `result`: `JuntozObject` o `TargetObject`. 

## Catch All
Aunque el runtime ya tiene varios try-catch para evitar cualquier interrupcion, es una buena practica (para los plugins) de que estos tambien tengan uno global y el response siempre se de. Es una forma de mantener tambien la estabilidad del runtime y de lograr que todo quede bien encadenado.

```
pull(ctx, input) {
  try {
    // catch global
  } catch (error) {
    return {
      status: 'error',
      result: null,
      errors: [error]
    };
  }
}
```
## Finalmente asi quedaria un InboundReceive (y OutboundGet)

InboundReceive y OutboundGet seran faciles porque son simples mappings de data. Seran muy similares. No necesitaran conexiones externas. Solo es manipulacion de data.
(En un futuro se espera que se pueda hacer un sandboxing para que el plugin no se le permita hacer eso).

```
import { PluginInboundReceive, EntityName, RuntimeContext, PluginDirection, PluginAction, TargetObject, PluginResponseStatus, PluginResponseToJuntoz, ConfigField } from '@juntoz/integration-plugin-base';

export default class StockInboundReceivePrestashopPlugin implements PluginInboundReceive {
    entityName: EntityName;
    pluginDirection: PluginDirection;
    pluginAction: PluginAction;
    pluginTarget: string;
    pluginAuthor: string;
    pluginVersion: string;

    constructor() {
        this.entityName = 'stock';
        this.pluginDirection = 'inbound';
        this.pluginAction = 'receive';
        this.pluginTarget = 'prestashop';
        this.pluginAuthor = 'juntoz';
        this.pluginVersion = '1.0.0';
    }

    pluginConfigMeta(): ConfigField[] {
        // no config needed for now
        return [];
    }

    pluginFullName(): string {
        return `${this.entityName}-${this.pluginDirection}${this.pluginAction}-${this.pluginTarget}-${this.pluginAuthor}-${this.pluginVersion}`;
    }

    async receive(ctx: RuntimeContext, input: TargetObject): Promise<PluginResponseToJuntoz> {
        try {
            ctx.logger.debug('mapping start');

            const response = {
                status: <PluginResponseStatus>'success',
                result: {
                    sku: input.sku,
                    quantity: input.stock,
                    storeId: input.storeId,
                    warehouseExternalId: input.warehouseExternalId,
                    eventName: this.pluginFullName()
                }
            };

            ctx.logger.debug('mapping done');
            return response;
        } catch(error) {
            return {
                status: 'error',
                result: null,
                errors: [error]
            };
        }
    }
}
```

# InboundPull (y OutboundPush)
InboundPull es el mas complicado de todos. Un IP se necesita primero obtener un universo de items de Juntoz, pasarlos al plugin para que haga un request al partner, obtenga la data y le haga un mapping hacia el formato de Juntoz. Esto igual ocurre con el OutboundPush.

Entonces aqui si hay que permitir la conectividad hacia afuera.

Para crear un plugin InboundPull, hay que seguir los mismos pasos y agregar algunos mas:

## allowMultiple
Los plugins InboundPull y OutboundPush deben implementar el metodo `allowMultiple(): boolean`.

Cuando es `true`, el runtime engine validara que el input sea un array y que el output tambien sea un array.

Para los IP, el metodo a implementar se llama `pull()`. Para los OP, se llama `push()`.

## Datos para la conexion u otros
Como mencionamos antes, los plugins tienen un metodo llamado `pluginConfigMeta()` que debe retornar una array de `ConfigField` con cada uno de los campos necesarios para que el plugin pueda ejecutarse. Por lo tanto, es en ese metodo donde el plugin debe declarar: "necesito el campo X, Y, Z".

Y entre ellos seguramente estara el `url` a donde debe conectarse, y algun otro campo para hacer la autenticacion como un `apiKey` o un `clientId`/`clientSecret`. Depende de cada plugin y la subscripcion es responsable de tener los valores necesarios para que esta configuracion este disponible a la hora de ejecutar el plugin.

# Como publicarlo?

**TODA ESTA SECCION SE BASA EN QUE HOY SE USARA MODULEFINDER**. Cuando se use otro finder distinto, se usara otro metodo.

Lo primero seria publicar el package a un feed npm (en este caso iria en el npm privado de juntoz).

Para hacer eso siempre el primer paso es **cambiar la version del paquete** usando el comando `npm version patch|minor|major`, usando la metodologia `semver`. 

Y este cambio de version **tambien debe hacerse dentro del plugin** en el campo `pluginVersion` (esto es por orden dado que el que esta en la clase es el que realmente se usa para encontrarlo).

Una vez que tenga el numero de version asignado, se debe usar el comando `npm publish`. Esto lo subira al npm privado.

Una vez publicado, **dado que estamos usando ModuleFinder**, debes ir al componente donde se estara usando el plugin runtime (por ejemplo dentro de InboundReceiveProcessor), e instalar la nueva version del plugin. De esta manera `Directory.find` encontrara el paquete a traves de la clase `ModuleFinder`.

# Typescript
La recomendacion es que el plugin se haga en typescript, pero si aun estas un poco novato en typescript hay algunas cosas que hay que considerar entonces para que el plugin se publique correctamente.

## tsconfig.json
Lo primero es que en todo proyecto TS se debe iniciar con el comando `tsc --init`. Esto generara un archivo `tsconfig.json` por defecto y que tenemos que modificar asi:

```
{
  "compilerOptions": {
    "target": "es2015",                       /* Specify ECMAScript target version: 'ES3' (default), 'ES5', 'ES2015', 'ES2016', 'ES2017', 'ES2018', 'ES2019', 'ES2020', or 'ESNEXT'. */
    "declaration": true,                   /* Generates corresponding '.d.ts' file. */
    "sourceMap": true,                     /* Generates corresponding '.map' file. */
    "outDir": "./lib",                        /* Redirect output structure to the directory. */
```

Minimo necesitamos tres configuraciones: `declaration`, `sourceMap` y `outDir`. `target` es necesario segun "lo avanzado" que sea nuestro programa.

Luego, ir al archivo `package.json` y anadir o modificar las siguientes entradas:
```
{
...
  "main": "lib/index.js",
  "types": "lib/index.d.ts"
...
}
```
Esto dira que al ejecutar `require` e importar el plugin, se usara el archivo `lib/index.js` como punto de entrada.

Todo esto es obviamente asi si es que tienes un archivo `index.ts` y la configuracion `outDir: ./lib` en el `tsconfig.json`. Si no es asi, modificar el main y types acorde a lo que se tiene.

NOTA: por lo general los archivos .ts se hacen uno por clase por lo tanto siempre es bueno tener un index.ts que haga el `barrel export` de todas las clases necesarias. Google it.

Por ultimo, en el mismo archivo `package.json` agregar los siguientes comandos:

```
  "scripts": {
    "build": "tsc",
    "prepublishOnly": "npm run build"
  },
```
Esto hara que cuando ejecutes `npm publish` para subir el plugin al repo (claro que cuando tengamos un feed propio separado sera distinto), automaticamente ejecute tsc y genere los archivos javascript en al carpeta `lib`.

Haciendo todo esto, tu plugin deberia ser posible compilarlo y publicarlo correctamente cada vez que hay cambios.


## Preguntas

1. Que patrones de diseño de codigo conoces y Como los aplicaste

1. Cual ha sido la arquitectura más compleja que has usado o disenado? Describela con detalles.

1. Como crearias una aplicacion pública ecommerce desde cero? Que tecnologias usarias y por que? Solo a nivel tecnico, asumiendo que ya conoces los requerimientos y etc. 6 meses, 20000 dólares. Y como seria tu metodología de trabajo.

1. Como manejaría el SEO?

1. Tu aplicación ya corre por un año, más usuarios, más ordenes. Como escalarias la aplicación?

1. Como manejarias el CI CD?

1. Que metodología de desarrollo usan?

1. Cual es el problema de performance más complejo q has visto?

1. Dime todo lo que sabes del protocolo http

1. Que tecnologia nueva te gusta y cuales crees que ya no deberia usarse?

## Prueba tecnica

Inscribete en hackerrank.com y haz estas pruebas

https://www.hackerrank.com/challenges/minimum-swaps-2/problem?h_l=interview&playlist_slugs%5B%5D=interview-preparation-kit&playlist_slugs%5B%5D=arrays

https://www.hackerrank.com/challenges/count-triplets-1/problem?h_l=interview&playlist_slugs%5B%5D=interview-preparation-kit&playlist_slugs%5B%5D=dictionaries-hashmaps

Tiempo maximo 1 hora. Aun si no lo terminas, igual envias:
- codigo fuente
- screenshot de los resultados de los tests (hackerrank muestra una consola de resumen test 1 check, test 2 check, etc)
- IMPORTANTE: debes hacer Submit a tu codigo para que ejecuten alrededor de 12-15 tests. Los 3 sample tests no son suficientes.

Puedes escoger el lenguaje que quieras.

TIP: Prioriza y arma una estructura base para que al final del tiempo igual se pueda ver un esquema de trabajo.

```
// KRUIZ: Verified solution, all tests passed :)
// Complete the minimumSwaps function below.
function minimumSwaps(arr) {
    var swaps = 0;
    for (var i = 0; i < arr.length; i++) {
        // thisnum is, in itself, the final position the number should be located - 1
        // e.g. thisnum = 3, it should be in position 2
        var thisnum = arr[i];
        if (thisnum == i + 1) {
            // this num is in the right location
        } else {
            // swap
            var swapped = arr[thisnum - 1];
            arr[i] = swapped;
            arr[thisnum - 1] = thisnum;

            // restart the loop so it starts again
            i = 0;
            swaps++;
        }
    }

    return swaps;
}
```

```
// Complete the countTriplets function below.
function countTriplets(arr, r) {
    if (arr.length < 3)
        return 0;
    var result = 0;
    for (var i = 0; i < arr.length; i++) {
        for (var j = i + 1; j < arr.length; j++) {
            for (var k = j + 1; k < arr.length; k++) {
                var n1 = arr[i];
                var n2 = arr[j];
                var n3 = arr[k];
                if ((n1 * r) == n2 && (n2 * r) == n3) {
                    result++;
                }
            }
        }
    }

    return result;
}
```
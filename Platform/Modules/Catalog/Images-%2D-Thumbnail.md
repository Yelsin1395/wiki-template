# jz-af-image-processor

Al momento de procesar una imagen para sus diferentes dimensiones se hace a través de una cola para evitar saturar la aplicación porque podría ser una tarea que tarde un poco más de lo habitual en sistema de alta concurrencia.

Este proceso se realiza a través de Azure Function, la función se llama. **jz-af-image-processor**.

## Mensaje
El mensaje encolado tiene la siguiente estructura

```
const body = {
	entityType: 'product',
	images: images.map(x => {
		return {
			id: x.id,
			source: x.imageOriginal
		};
	}),
	basePath,
	callbackUriResponse,
	conversions: [
		{ type: 'large', field: 'imageLarge', width: 1000, height: 1000 },
		{ type: 'medium', field: 'imageMedium', width: 500, height: 500 },
		{ type: 'small', field: 'imageSmall', width: 166, height: 166 }
	]
};

const message = {
	body,
	userProperties: {
		event: 'thumbnail',
		siteId: this.inSite,
		bearerToken: this.bearerToken
	}
};

await this.azureBusSender.sendToQueue('image_process', message);
```

- **entityType**: es el tipo de identidad con el que se esta trabajando. Es importante para poder mapear la información de metada relacionada a la entidad como podría ser obtener los accessos a azure_storage, entre otros.
- **images**: la colección de imágenes a procesar.
- **basePath**: es el path base a donde se va a publicar la imagen (foo/bar/images/).
- **callbackUriResponse**: luego de procesar las imágenes, Azure Function necesita saber a donde debe responder para actualizar la ruta de las nuevas imágenes. Ejemplo ``/c/product/update/image/thumbnail/update/${productId}``
- **conversions**: son las medidas a las que se quiere procesar la imagen.
  - **type**: el tipo de imagen que queremos. Esto sirve para agregarlo al nombre de la nueva imagen.
  - **field**: el nombre de la propiedad al que pertenece dicha imagen según el schema de JOI para imágenes.
  - **width & height**: las medidas a la que queremos procesar.

## Nombre de la cola
El nombre de la cola a usar se llama **image_process**.
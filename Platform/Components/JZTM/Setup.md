# Juntoz Tag Manager – Setup
El presente documento servirá de modelo para configurar el tracking de una tienda mediante el componente JuntozTagManager.

En una primera etapa se ha considerado trackear hacia Google Analytics, es por eso que se los requisitos y pasos detallados a continuación se centran en ello.

Requisitos previos:
    • Una cuenta de Google para configuración.
    • Una cuenta de Google Analytics configurada.

## Creación de cuenta Google Tag Manager

- Ingresar a https://tagmanager.google.com y acceder a su cuenta:
![Items.png](/.attachments/Items-cd476c43-3a55-41d3-9ca4-6c783bbc3c96.png)

- Registrar los datos para la cuenta:
![Items2.png](/.attachments/Items2-88083e68-2d35-4e17-847f-5474e6df5f33.png)

- Aceptar los términos y condiciones
- Se genera un código GTM que será el identificador de la cuenta (GTM-XXXXX):
![Items5.png](/.attachments/Items5-96b0ed0e-3298-474d-bc44-6efd36a123f4.png)

## Importar template para JZTM en Google Tag Manager

- Ingresar a la Administración de la cuenta y seleccionar Importar Container:
![Items6.png](/.attachments/Items6-02d80059-1b9d-46f5-a9cb-80ca7b9c5952.png)

- Seleccionar el template a cargar (JZTM-TEMPLATE):
![Items7.png](/.attachments/Items7-28302558-a806-4ae7-b38b-8e7003e386ba.png)
![Items8.png](/.attachments/Items8-4de03f7b-91fa-49c4-a78c-fa894457cd21.png)

- Ingresar a “Folders”:
![Items10.png](/.attachments/Items10-ded42620-7ca6-41a0-ac9c-9b9f6a35594a.png)

- Ingresar a la carpeta “JuntozTagManager”:
![Items11.png](/.attachments/Items11-da540a71-631e-4be3-ba14-e692aeafa119.png)

- En la variable ID-GoogleAnalytics, se debe setear el valor correspondiente:
![Items13.png](/.attachments/Items13-9e7b3f8a-4dfa-4382-96d3-1ec4deb490b4.png)

- Preparar la publicación de la cuenta:
![Items14.png](/.attachments/Items14-84c44e88-1bfc-43bb-96eb-cba2925a740b.png)

3.- Configuración de GTM en Merchant Central.
- Ingresar a https://merchant.juntoz.com/ y acceder a su cuenta.
![Items15.png](/.attachments/Items15-fe6a0aa1-d149-4ee4-8b2e-f1ee087ee693.png)

- Ingresar a la opción “Configuración/Configuraciones”:
![Items16.png](/.attachments/Items16-46201679-b640-4980-96d3-ba9c9dcbdacc.png)

- Agregar el key “GoogleTagManagerCode” con el identificador de la cuenta creada:
![Items17.png](/.attachments/Items17-37b03c55-17c0-41de-8c4f-d59b4684cbf2.png)

## Preview GTM

-En el workspace de GTM, clickear la opción “Preview”
![Items18.png](/.attachments/Items18-753d960f-b16f-48b2-8f19-5dad9feeab93.png)

-Ingresar a la tienda : xxx.juntoz.com, se visualizará la consola de GTM donde se puede apreciar el tracking resuelto:
![Items19.png](/.attachments/Items19-ba00b1dd-9e38-424d-8e6b-882561b78407.png)

## Template

[JZTM-TEMPLATE-v1.0-0b9ef476-8e1a-4650-88ec-fd16fdc8e0c0.json](/.attachments/JZTM-TEMPLATE-v1.0-0b9ef476-8e1a-4650-88ec-fd16fdc8e0c0-99708bf4-94af-4ba1-a1a0-c8fe66fbf639.json)
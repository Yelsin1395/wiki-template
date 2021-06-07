**Removed code**
- Payment-Int
Se borró Handlers y Controllers de VisaNet en el Commit 37b6c4ac con fecha 04 de Marzo del 2019


- Eliminacion de evento SyncPartialProductStoreEvent
changelist:
Web.MC c421292d, 55369841
Api.Catalog 307c656e, fd8d9c1d
Se borro codigo de Campaigns y ProductListV1 que ya no usaba el POST productstores/sync/batch.
Esto elimina entonces la creacion del mensaje SyncPartialProductStoreEvent en Catalog.Rest.ProductStoreBatchController.
Y por lo tanto elimina el Eagle.Bus.Catalog.SyncStock.Host

- Eliminacion de PriceEventQueue

El PriceEventQueue se estaba llamando desde el url products/store/{productInStoreId}/param, sin embargo, la unicas pantallas que lo usaban era MC productBulkUpload, productBulkUpdate, productExcelUpdateParam.

Las dos primeras no usaban esos metodos, y la ultima no se usaba para nada.

Api.Catalog 8424e4f4 (commented code)
Web.MC d875be71, defa6b79, 90e0f200


— Eliminacion de filescataloginboundeventqueue queue
Web.MC 2e81cd3a, 90e0f200
Api.Catalog 1758cafa, 5486396e, e4d916cb, 9eb747e5

- Eliminacion de Eagle.Bus.Catalog.Campaign.Host
La cola BusNames.CampaignNames.EventQueue campaigninboundeventqueue no estaba siendo usada
Api.Catalog c2e33ff4

- Elminacion de Eagle.Bus.Catalog.SyncBoost.Host
La cola BusNames.CatalogNames.BoostEventQueue cataloginboundeventqueueboost no estaba siendo usada
Api.Catalog ad5bb59c

- Eliminacion de Eagle.Bus.Catalog.Integration.Host
La cola BusNames.ExternalIntegrationsNames.DefaultQueue externalintegrationsinboundqueue and EventQueue externalintegrationsinboundeventqueue were not used.
There were some uses but we decided to push to catalog_syncstockevent queue

Api.Catalog bb922eaa, e1e9fb47, 3cea149c, fd9fcb8b
Api.Inventory b3f53afc, edac7647




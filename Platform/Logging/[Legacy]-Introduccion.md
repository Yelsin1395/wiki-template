# Logging

La generacion de logs de Juntoz se hace a traves de varias herramientas.

Hasta hace un anho, solo se usaba `Application Insights`. Sin embargo, AI no funciona bien para tener seguimiento de logs exactos.

Por ello es que escogimos `papertrail.com` como herramienta de log principal (2 week search, 1 year archive) y para comunicarse con papertrail, usamos `NLog` como libreria oficial de logging.
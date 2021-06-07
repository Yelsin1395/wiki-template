#NLog Configuration

01. Instalar NLog (capas que se van a usar)
02. Instalar Microsoft.ApplicationInsights.NLogTarget (Solo webhost o donde configura el NLog)
03. Crear una clase de configuración (/Config/NLogConfig) y llamarlo en el StartUp

```csharp
    public static class NLogConfig
    {
        public static void ConfigureNLog()
        {
            var config = new LoggingConfiguration();
            var target = new ApplicationInsightsTarget();

            var rule = new LoggingRule("*", LogLevel.Info, target);
            config.LoggingRules.Add(rule);

            LogManager.Configuration = config;
        }
    }
```

04. Crear el ExceptionCustomFilter para capturar todos los requests que fallen y mandarlo al applicationinsights

```csharp
    public class ExceptionCustomFilter : ExceptionFilterAttribute
    {
        private static readonly Logger _logger = LogManager.GetCurrentClassLogger();

        public override void OnException(HttpActionExecutedContext actionExecutedContext)
        {
            _logger.Error(actionExecutedContext.Exception);
        }
    }
```

05. Hacer una prueba desde localhost, mandar un mensaje y buscarlo en el ApplicationInsight

Nota:
* Retirar del Untity TelemetryClient si existía (el registro de la dependencia)
* Validar las dependencias (posibles conflictos detectados)
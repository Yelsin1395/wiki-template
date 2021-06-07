Si el siguiente error aparece en tu ambiente local al hacer `docker run`,

```
The authorization token is not valid at the current time. Please create another token and retry
```

La solucion es reiniciar Docker Desktop.

Al parecer tiene que ver con que los relojes de Docker y Windows se desincronizan.

https://github.com/Azure/azure-cosmos-dotnet-v2/issues/506

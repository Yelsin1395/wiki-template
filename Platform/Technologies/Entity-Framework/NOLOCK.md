Agregar Referencia: `System.Transactions`
Ejemplo:

```
public async Task<Brand> FindById(int brandId)
        {
            using (var transaction = new TransactionScope(
                TransactionScopeOption.Required,
                new TransactionOptions { IsolationLevel = IsolationLevel.ReadUncommitted },
                TransactionScopeAsyncFlowOption.Enabled))
            {
                var entity = await this.Table.Where(c => c.BrandId == brandId && !c.IsDeleted).FirstOrDefaultAsync();
                return entity;
            }
        }
```

## Comentario
Sin embargo, esto no es necesariamente una buena solucion. `(NOLOCK)` es un hack que en general no es recomendado y que se hace debido a que los queries y los updates son constantes contra una tabla (los updates bloquean la tabla).

Hay muchas otras estrategias que son mejores y las tocaremos en otro articulo.
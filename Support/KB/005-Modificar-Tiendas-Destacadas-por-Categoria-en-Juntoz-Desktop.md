Por ejemplo si solicitan colocar el siguiente listado en la categoria "TV, Audio y Foto":
TV AUDIO FOTO
- 1 Samsung
- 2 LG
- 3 Panasonic
- 4 Skullcandy
- 5 Sennheiser
- 6 Phantom
- 7 LAWGAMERS
- 8 Sudio
- 9 Audiomusica
- 10    Pioneer
- 11    Sony
1. Identificar en la tabla STORE los Ids de las tiendas:
select * from Store
where [name] in (
'Samsung',
'LG',
'Panasonic',
'Skullcandy',
'Sennheiser',
'Phantom',
'LAWGAMERS',
'Sudio',
'Audiomusica',
'Pioneer',
'Sony'
)
2. Identificar en la tabla STORECATEGORY los Ids de los registros a actualizar, filtrando por la categoria a trabajar (TV, Audio y Foto, categoryId : 988735)
select * from [dbo].[StoreCategory] 
where StoreId in (
7
,1084
,1863
,1970
,1989
,2019
,2762
,2861
,3184
,3577
,3650
,3651
,3698
,3764
,3811
)
and CategoryId = 988735
3. Realizar los update necesarios para lograr el ordenamiento requerido, por ejemplo:
- update storeCategory set DisplayOrder = 1 where storeCategoryId = 16202   
- update storeCategory set DisplayOrder = 2 where storeCategoryId = 16185   
- update storeCategory set DisplayOrder = 3 where storeCategoryId = 14312   
- update storeCategory set DisplayOrder = 5 where storeCategoryId = 16208   
- update storeCategory set DisplayOrder = 6 where storeCategoryId = 16188   
- update storeCategory set DisplayOrder = 7 where storeCategoryId = 16205   
- update storeCategory set DisplayOrder = 8 where storeCategoryId = 15936   
- update storeCategory set DisplayOrder = 9 where storeCategoryId = 16206   
- update storeCategory set DisplayOrder = 10    where storeCategoryId = 16207   
- update storeCategory set DisplayOrder = 11    where storeCategoryId = 15031   
4. Validar adicionalmente que otros registros en storeCategory para ese mismo CategoryId = 988735, tengan un valor superior a 11. De esta manera aparecerán luego de los primeros
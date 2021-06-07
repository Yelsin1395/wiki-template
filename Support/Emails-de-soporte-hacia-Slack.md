# Como los emails a techsupport@juntoz.com llegan a slack?
Se hace a traves de zapier con mi cuenta (katlim@ieholding.com).

Cree un email address llamado juntoztechalerter@gmail.com que esta dentro del grupo techsupport@juntoz.com.
Cree un filtro en esa cuenta para que cada vez que haya un email hacia techsupport, se marque con el label ALERT.

Cree en zapier una regla que acceda a esta cuenta de gmail y lea los nuevos threads qeu se estan creando.
Cree en zapier un segundo paso para que envie el titulo del email hacia #bugreporting. Esto lo configure con mi cuenta de slack katlim@ieholding.com.
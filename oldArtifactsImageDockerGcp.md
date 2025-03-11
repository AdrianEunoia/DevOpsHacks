
# Script para eliminar imágenes Docker en Google Cloud Registry

Este script está diseñado para eliminar imágenes Docker en Google Cloud Registry que no tienen el tag `latest` y que están fuera del rango de fechas especificado (más antiguas que 30 días pero no más de 360 días). El script realiza las siguientes acciones:

## Descripción del proceso:

1. **Establecer las fechas de inicio y fin**: 
   - La fecha de inicio se establece como 360 días atrás.
   - La fecha de fin se establece como 30 días atrás.
   
2. **Filtrar las imágenes**: 
   - El script lista las imágenes en el repositorio de Google Cloud Registry cuyo tag no sea `latest` y cuya fecha de creación esté dentro del rango especificado.
   
3. **Eliminar las imágenes**: 
   - Las imágenes que cumplen con los criterios se eliminan junto con sus tags asociados.

## Script

```bash
#!/bin/bash

# Establecer las fechas de inicio y fin
initialDate=$(date -u -d '360 days ago' +'%Y-%m-%dT00:00:00Z')
limitDate=$(date -u -d '30 days ago' +'%Y-%m-%dT00:00:00Z')

# Listar y eliminar las imágenes según la fecha
gcloud container images list-tags YOUR_REGISTRY/YOUR_REPOSITORY --filter="NOT tags=latest AND timestamp.datetime >= '$initialDate' AND timestamp.datetime <= '$limitDate'" --format="get(digest)" > tags

# Eliminar las imágenes listadas
while read listedImgs; do
  gcloud container images delete "YOUR_REGISTRY/YOUR_REPOSITORY@$listedImgs" --force-delete-tags --quiet
done < tags
```

## Explicación:

- **initialDate y limitDate**: Estas variables contienen las fechas de inicio y fin para filtrar las imágenes por antigüedad.
  - `360 days ago` es el límite superior (más de 360 días) para no eliminar imágenes demasiado antiguas.
  - `30 days ago` es el límite inferior (más de 30 días) para asegurarse de que solo se eliminan imágenes que no son recientes.
  
- **gcloud container images list-tags**: Este comando lista las imágenes que cumplen con los filtros de fechas y el hecho de que no sean `latest`. Se utiliza el formato `get(digest)` para obtener solo los `digest` de las imágenes que cumplen los criterios.
  
- **gcloud container images delete**: Para cada digest listado, se elimina la imagen correspondiente, junto con todos sus tags asociados.

### Reemplazar valores en el script

- **YOUR_REGISTRY**: Reemplaza con la URL de tu registro de Docker en Google Cloud.
- **YOUR_REPOSITORY**: Reemplaza con el nombre de tu repositorio donde están almacenadas las imágenes.

### Advertencias

- Asegúrate de que las imágenes que se están eliminando son las que realmente deseas borrar.
- Este script elimina permanentemente las imágenes, incluyendo sus tags, por lo que no podrás recuperarlas después de ser eliminadas.
# Documentación: Cancelar y Eliminar Trabajos de GitHub Actions

Esta guía describe cómo cancelar trabajos en cola y eliminar trabajos completados en un repositorio de GitHub Actions utilizando la API de GitHub.

---

## Requisitos previos

1. **Token de acceso personal (PAT)**:
   - Ve a **Settings > Developer settings > Personal access tokens > Tokens (classic)**.
   - Genera un token con los permisos `repo` y `workflow`.
   - Guarda el token para usarlo en los comandos.

2. **Herramientas necesarias**:
   - Acceso a la línea de comandos.
   - `curl` instalado.
   - `jq` para procesar JSON (en Ubuntu puedes instalarlo con `sudo apt install jq`).

---

## Cancelar trabajos en cola

### 1. Identificar trabajos en cola
Ejecuta el siguiente comando para listar los trabajos en cola de un workflow específico:

```bash
curl -H "Authorization: token <TU_TOKEN_DE_ACCESO_PERSONAL>" \
     -X GET https://api.github.com/repos/<DUEÑO>/<REPOSITORIO>/actions/runs \
     | jq '.workflow_runs[] | select(.status=="queued" and .name=="<NOMBRE_DEL_WORKFLOW>") | .id'
```

- Reemplaza `<TU_TOKEN_DE_ACCESO_PERSONAL>` por tu token.
- Reemplaza `<DUEÑO>` y `<REPOSITORIO>` con el propietario y nombre del repositorio.
- Reemplaza `<NOMBRE_DEL_WORKFLOW>` con el nombre del workflow.

Esto devolverá una lista de IDs de trabajos en cola.

### 2. Cancelar los trabajos en cola
Usa el siguiente script para cancelar todos los trabajos en cola:

```bash
queued_jobs=$(curl -H "Authorization: token <TU_TOKEN_DE_ACCESO_PERSONAL>" \
                   -X GET https://api.github.com/repos/<DUEÑO>/<REPOSITORIO>/actions/runs \
                   | jq -r '.workflow_runs[] | select(.status=="queued" and .name=="<NOMBRE_DEL_WORKFLOW>") | .id')

for job_id in $queued_jobs; do
  curl -H "Authorization: token <TU_TOKEN_DE_ACCESO_PERSONAL>" \
       -X POST https://api.github.com/repos/<DUEÑO>/<REPOSITORIO>/actions/runs/$job_id/cancel
done
```

---

## Eliminar trabajos completados

### 1. Identificar trabajos completados
Ejecuta este comando para listar los IDs de los trabajos que han terminado:

```bash
curl -H "Authorization: token <TU_TOKEN_DE_ACCESO_PERSONAL>" \
     -X GET https://api.github.com/repos/<DUEÑO>/<REPOSITORIO>/actions/runs \
     | jq '.workflow_runs[] | select(.status=="completed") | .id'
```

### 2. Eliminar trabajos completados
Usa el siguiente script para eliminar los trabajos completados:

```bash
completed_jobs=$(curl -H "Authorization: token <TU_TOKEN_DE_ACCESO_PERSONAL>" \
                   -X GET https://api.github.com/repos/<DUEÑO>/<REPOSITORIO>/actions/runs \
                   | jq -r '.workflow_runs[] | select(.status=="completed") | .id')

for job_id in $completed_jobs; do
  curl -H "Authorization: token <TU_TOKEN_DE_ACCESO_PERSONAL>" \
       -X DELETE https://api.github.com/repos/<DUEÑO>/<REPOSITORIO>/actions/runs/$job_id
done
```

---

## Notas adicionales

1. **Seguridad del token**: No compartas tu token públicamente. Si crees que se ha filtrado, revócalo y genera uno nuevo.
2. **Eliminación permanente**: La eliminación de trabajos completados es irreversible.
3. **Verificación**:
   - Después de cancelar trabajos en cola o eliminar trabajos completados, revisa la pestaña **Actions** de tu repositorio para confirmar los cambios.

---

## Ejemplo de uso

### Cancelar trabajos en cola
Si tu token es `<TU_TOKEN_DE_ACCESO_PERSONAL>`, el repositorio es `<DUEÑO>/<REPOSITORIO>` y el workflow se llama `<NOMBRE_DEL_WORKFLOW>`, ejecuta:

```bash
queued_jobs=$(curl -H "Authorization: token <TU_TOKEN_DE_ACCESO_PERSONAL>" \
                   -X GET https://api.github.com/repos/<DUEÑO>/<REPOSITORIO>/actions/runs \
                   | jq -r '.workflow_runs[] | select(.status=="queued" and .name=="<NOMBRE_DEL_WORKFLOW>") | .id')

for job_id in $queued_jobs; do
  curl -H "Authorization: token <TU_TOKEN_DE_ACCESO_PERSONAL>" \
       -X POST https://api.github.com/repos/<DUEÑO>/<REPOSITORIO>/actions/runs/$job_id/cancel
done
```

### Eliminar trabajos completados
Para eliminar los trabajos completados en el mismo repositorio, ejecuta:

```bash
completed_jobs=$(curl -H "Authorization: token <TU_TOKEN_DE_ACCESO_PERSONAL>" \
                   -X GET https://api.github.com/repos/<DUEÑO>/<REPOSITORIO>/actions/runs \
                   | jq -r '.workflow_runs[] | select(.status=="completed") | .id')

for job_id in $completed_jobs; do
  curl -H "Authorization: token <TU_TOKEN_DE_ACCESO_PERSONAL>" \
       -X DELETE https://api.github.com/repos/<DUEÑO>/<REPOSITORIO>/actions/runs/$job_id
done
```
### Script simplificado y en bucle
```bash
#!/bin/bash

# Define el repositorio
   REPO="organizacion/repositorio"

# Token de GitHub (asegúrate de almacenarlo de forma segura en lugar de hardcodearlo)
GITHUB_TOKEN="COMPLETA CON TU GITHUB TOKEN"

# Obtener los IDs de los workflows completados
completed_jobs=$(curl -s -H "Authorization: token $GITHUB_TOKEN" \
  -X GET "https://api.github.com/repos/$REPO/actions/runs" | jq -r '.workflow_runs[] | select(.status=="completed") | .id')

# Eliminar cada workflow completado
for job_id in $completed_jobs; do
  echo "Eliminando workflow con ID: $job_id"
  response=$(curl -s -o /dev/null -w "%{http_code}" -H "Authorization: token $GITHUB_TOKEN" \
    -X DELETE "https://api.github.com/repos/$REPO/actions/runs/$job_id")

  if [ "$response" -eq 204 ]; then
    echo "Workflow $job_id eliminado correctamente."
  else
    echo "Error eliminando workflow $job_id. Código de respuesta: $response"
  fi
done
```

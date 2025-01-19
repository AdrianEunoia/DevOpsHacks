# Script para Configurar Ventanas de Mantenimiento en Clústeres de GKE

## Descripción

Este script automatiza la configuración de **ventanas de mantenimiento** para clústeres de Google Kubernetes Engine (GKE) en **todos los proyectos** de Google Cloud asociados a la cuenta del usuario. Utiliza la herramienta de línea de comandos `gcloud` para:

1. Buscar todos los proyectos activos en la cuenta de Google Cloud.
2. Para cada proyecto, identificar un clúster de GKE en ejecución.
3. Configurar una ventana de mantenimiento recurrente semanal para el clúster encontrado, estableciendo la hora de inicio, duración y frecuencia de la ventana.

Este proceso es útil para automatizar la gestión de mantenimiento de múltiples clústeres sin necesidad de configurar cada proyecto de forma manual.

## Requisitos

- **gcloud SDK**: Debes tener la herramienta de línea de comandos `gcloud` instalada y configurada.
- **Permisos**: Necesitas tener permisos adecuados para gestionar proyectos y clústeres de GKE en tu cuenta de Google Cloud.
- **API de GKE habilitada**: La API de Kubernetes Engine debe estar habilitada para los proyectos que el script va a modificar.

## Variables

### Fechas y Días de Mantenimiento
- `START_DATE`: Fecha de inicio de la ventana de mantenimiento en formato **UTC**. (Ejemplo: `2025-01-20T00:00:00Z`).
- `END_DATE`: Fecha de finalización de la ventana de mantenimiento en formato **UTC**. La duración entre `START_DATE` y `END_DATE` debe ser la duración deseada de la ventana de mantenimiento (Ejemplo: `2025-01-20T12:00:00Z`).
- `RECURRENCE`: Define la frecuencia y los días de la semana en los que se repetirá la ventana de mantenimiento. El formato es **RRULE** de **iCalendar**. En este caso, está configurado para que sea semanal y se repita todos los sábados (`FREQ=WEEKLY;BYDAY=SA`).

### Ejemplo:
```bash
START_DATE="2025-01-20T00:00:00Z"
END_DATE="2025-01-20T12:00:00Z"
RECURRENCE="FREQ=WEEKLY;BYDAY=SA"
```

## Flujo de Trabajo del Script

### Obtener la lista de proyectos: El script obtiene todos los proyectos activos utilizando el siguiente comando:
```bash
gcloud projects list --format="value(projectId)"
Iterar sobre los proyectos: El script recorre cada uno de los proyectos obtenidos en el paso anterior. Para cada proyecto, se realiza lo siguiente:
```

### Se configura el proyecto actual con el comando:
```bash
gcloud config set project $PROJECT_ID
```

### Buscar el clúster en el proyecto:
Se busca el nombre del clúster GKE que esté en ejecución:
```bash
gcloud container clusters list --filter="status=RUNNING" --format="value(name)" --limit=1
```

Si no se encuentra un clúster en ejecución, el script continuará con el siguiente proyecto.

## Configurar la ventana de mantenimiento:

Si se encuentra un clúster, se configura la ventana de mantenimiento utilizando el siguiente comando:
```bash
gcloud container clusters update $CLUSTER_NAME \
  --maintenance-window-start $START_DATE \
  --maintenance-window-end $END_DATE \
  --maintenance-window-recurrence "$RECURRENCE" \
  --region=$REGION
```

Resultado: El script configura la ventana de mantenimiento para cada clúster encontrado y muestra un mensaje indicando que la configuración ha sido aplicada con éxito.

## Uso
**Editar variables**: Ajusta las variables START_DATE, END_DATE, y RECURRENCE según tus necesidades antes de ejecutar el script.
**Ejecutar el script**: Una vez configuradas las variables, simplemente ejecuta el script:

```bash
 script.sh
```

**Verificación**: El script imprimirá un mensaje indicando que la ventana de mantenimiento ha sido configurada correctamente para cada clúster y proyecto donde se haya aplicado.

## Notas
El script está diseñado para iterar sobre todos los proyectos de la cuenta de Google Cloud, por lo que afectará a todos los clústeres GKE que estén activos en dichos proyectos.
La ventana de mantenimiento se aplica solo si se encuentra un clúster en ejecución en el proyecto.

## Posibles Errores
Si no se encuentra un clúster en el proyecto, el script continuará con el siguiente proyecto.
Si las fechas no son válidas o el formato no es el adecuado, el script puede fallar al aplicar la configuración. **Asegúrate** de que las fechas están en formato UTC y siguen el formato adecuado de YYYY-MM-DDTHH:MM:SSZ.
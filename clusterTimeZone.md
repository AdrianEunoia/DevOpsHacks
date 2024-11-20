# Configuración de Zona Horaria en Kubernetes con `TZ`

En Kubernetes, la zona horaria predeterminada es UTC (`UTC+0`). Si necesitas que tus aplicaciones usen una zona horaria diferente, puedes configurar la variable de entorno `TZ` en los contenedores. Esto es una solución sencilla y eficaz que no requiere modificar el sistema operativo de los nodos.

## Pasos para Configurar la Zona Horaria

### 1. **Edición del `Deployment` o `Pod`**
Al definir un `Deployment`, `StatefulSet` o `Pod`, añade la variable de entorno `TZ` al contenedor deseado. Aquí tienes un ejemplo básico:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: example-app
  namespace: example-namespace
spec:
  replicas: 1
  selector:
    matchLabels:
      app: example-app
  template:
    metadata:
      labels:
        app: example-app
    spec:
      containers:
      - name: example-container
        image: your-image:latest
        env:
        - name: TZ
          value: "Asia/Bangkok" # Cambiar por la zona horaria deseada
```

###  2. Definir la Zona Horaria Correcta
La variable TZ utiliza las zonas horarias de la base de datos de zonas horarias de IANA, que son ampliamente compatibles. Algunos ejemplos comunes son:

- Asia/Bangkok: UTC+7
- America/New_York: UTC-5 (o UTC-4 durante horario de verano)
- Europe/London: UTC+0 (o UTC+1 durante horario de verano)

Consulta una lista completa en tz database.

### 3. Aplicar el Manifest
Guarda el archivo YAML y aplícalo en el clúster con:

```bash
kubectl apply -f <nombre-del-archivo>.yaml
```

### 4. Verificar la Configuración
Para asegurarte de que la zona horaria está configurada correctamente, puedes ejecutar un comando dentro del contenedor y verificar la hora:

``` bash
kubectl exec -it <nombre-del-pod> -- date
```

El resultado debe coincidir con la zona horaria configurada.

## Ventajas de Usar TZ

- Portabilidad: La variable de entorno TZ no depende del sistema operativo del nodo.
- Facilidad de Implementación: Solo requiere editar los manifiestos de Kubernetes.
- Aislamiento: Cada contenedor puede tener su propia zona horaria, si es necesario.

## Consideraciones
1. Si la aplicación interna utiliza bibliotecas de tiempo que ignoran TZ, esta configuración no tendrá efecto. Asegúrate de que la aplicación soporte el uso de esta variable.
2. Si necesitas configurar la zona horaria a nivel global para todo el clúster, considera otras opciones como la modificación del sistema operativo de los nodos o el uso de un ConfigMap con el archivo de zona horaria.
Con esta configuración, puedes asegurarte de que tus aplicaciones funcionen en la zona horaria correcta sin cambios significativos en el entorno subyacente. 🚀
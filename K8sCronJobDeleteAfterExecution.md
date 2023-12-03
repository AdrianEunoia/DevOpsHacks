# Ejecución Manual de Trabajos en Kubernetes y Eliminación Automática

## Introducción

Esta documentación detalla los pasos para ejecutar manualmente trabajos en Kubernetes utilizando un CronJob y cómo configurar la eliminación automática de estos trabajos después de su finalización.

## Pasos

1. **Crear un Trabajo Manual:**
   Utiliza el comando `kubectl create job` para ejecutar manualmente un trabajo desde un CronJob.

  ```bash
   kubectl create job --from=cronjob/your-cronjob manual-run-job-1 -n your-namespace
  ```

  Asegúrate de personalizar el nombre del CronJob y del trabajo manual según tus necesidades.

2. **Configurar Eliminación Automática:**
  Crea un manifiesto YAML para el trabajo con eliminación automática. Puedes utilizar el siguiente ejemplo como base.
  ```yaml
        apiVersion: batch/v1
      kind: Job
      metadata:
        name: manual-run-job-1
        namespace: your-namespace
      spec:
        ttlSecondsAfterFinished: 0
        template:
          spec:
            containers:
            - name: manual-run-job-1
              image: <your-container-image>
              # ... otras configuraciones ...
  ```
  Reemplaza <your-container-image> con la imagen de tu contenedor y ajusta otras configuraciones según sea necesario.

3. **Aplicar Configuración:**
  Aplica la configuración del trabajo con eliminación automática utilizando el comando kubectl apply.

  ```bash
  kubectl apply -f manual-run-job-1.yaml
  ```
  Esto establecerá ttlSecondsAfterFinished: 0, asegurando que el trabajo se elimine automáticamente después de la finalización de los pods.

## Conclusión:

Siguiendo estos pasos, podrás ejecutar y gestionar trabajos manuales en Kubernetes de manera eficiente, garantizando la eliminación automática después de su finalización.
# DevOpsHacks 🦔

¡Bienvenido al mundo macarra de DevOps! Aquí no hay lugar para el código aburrido y las operaciones monótonas. Prepárate para una dosis de conocimiento DevOps con un toque rebelde. 🔥

## 🚀 ¿Qué encontrarás aquí?

Este repositorio es tu guarida de sabiduría operativa y consejos hardcore de DevOps. Desde automatización hasta despliegues continuos, estamos aquí para elevar tus habilidades a un nivel superior.

## 📚 Índice de Sabiduría

1. [Integración de Cuenta de Servicio en GitHub Actions](GCPServiceAccountAsGHActionSecret.md)
   - Consejos para integrar cuentas de servicio de Google Cloud Platform en GitHub Actions.

2. [Guía de Manejo de Variables de Entorno JSON en Scripts Bash](JSONEnvVarsFromSecretsOrConfigmaps.md)
   - Se incluye un ejemplo práctico utilizando secretos de Kubernetes para ilustrar el enfoque correcto.

3. [Parámetro para la autodestrucción post-ejecución en un K8s CronJob](K8sCronJobDeleteAfterExecution.md)
   - Esto establecerá ttlSecondsAfterFinished: 0, asegurando que el trabajo se elimine automáticamente después de la finalización de los pods.

4. [Establecer un alias para cambiar de cluster de GKE rápidamente](SwapCLIBetweenGCPClusters.md)
   - Patata CLI 😅

5. [Analizar o filtrar logs de Docker con GREP](ANALyzeDockerLogswithGrep.md)
   - La redirección de logs en Docker puede requerir la combinación de docker logs 2>&1 y grep para abordar problemas de buffering y canal de error, asegurando una búsqueda efectiva en tiempo real.

6. [Guía de Ajuste de Tiempos de Espera en Ingress Nginx en GKE con Helm](ModifyHelmIngressNginx.md)
   - Ajuste de Tiempos de Espera en Ingress Nginx en GKE con Helm.

7. [Obtener IPs Reales de Clientes en Kubernetes detrás de Cloudflare desde tu INGRESS NGINX de Kubernetes](GetRealIPFromCloudFlare.md)
   - Nuestra aplicación en Kubernetes necesitaba capturar las IPs reales de los clientes, pero Cloudflare estaba mostrando solo las IPs de sus nodos. Esta es la solución que implementamos para obtener las IPs reales.

8. [Configuración del Tamaño Máximo de Carga en NGINX Ingress para ingress-nginx](IncreaseUploadLimitinNGINXIngress.md)
   - El error 413 Payload Too Large se puede solucionar al aumentar el límite de proxy-body-size en el recurso Ingress afectado.

9. [Configuración de uso horario para deployments diferentes a UTC+0](clusterTimeZone.md)
   - En Kubernetes, la zona horaria predeterminada es UTC (`UTC+0`). Si necesitas que tus aplicaciones usen una zona horaria diferente, puedes configurar la variable de entorno `TZ` en los contenedores. Esto es una solución sencilla y eficaz que no requiere modificar el sistema operativo de los nodos.

10. [Automatización de Ventanas de Mantenimiento para Clústeres GKE en Google Cloud](applyMaintenanceGKEScript.md)
   - Este script configura automáticamente ventanas de mantenimiento recurrentes para clústeres de GKE en todos los proyectos de Google Cloud asociados, asegurando un mantenimiento eficiente y sin interrupciones.

11. [Cancelar y Eliminar Trabajos de GitHub Actions](cancelNddeleteGHActionsQueue.md)
   - Esta guía describe cómo cancelar trabajos en cola y eliminar trabajos completados en un repositorio de GitHub Actions utilizando la API de GitHub.

12. [Eliminar imágenes de Artifacts Docker GCP](oldArtifactsImageDockerGcp.md)
   - Este documento explica cómo usar gcloud para eliminar imágenes Docker en Google Cloud Artifact Registry que tengan más de 30 días de antigüedad, manteniendo el repositorio optimizado.

13. [Cloud Scheduler Cron Cleanup](cloudSchedulerCronCleanup.md)
   - Eliminar los cron jobs antiguos de App Engine evita errores y logs obsoletos, asegurando que el cambio de formato de audit logs de Google Cloud no afecte al sistema.


## 💡 ¿Cómo contribuir?

¡Estamos ansiosos por recibir tus aportaciones macarras! Si tienes algún consejo o truco de DevOps que quieras compartir, sigue estos pasos:

1. Haz un fork del repositorio.
2. Crea una rama para tu aportación: `git checkout -b tu-rama-macarra`.
3. Añade tu contenido al README o crea nuevos archivos con tus tips.
4. Haz un commit con tu contribución: `git commit -m "Añade consejo macarra: [tu-consejo]"`.
5. Haz un push a tu rama: `git push origin tu-rama-macarra`.
6. Abre un pull request y cuéntanos qué hay de macarra en tu contribución.

¡Gracias por hacer de este repositorio el lugar más macarra para DevOps en GitHub!
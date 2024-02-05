# Ajuste de Tiempos de Espera en Ingress Nginx en GKE con Helm

Esta guía te mostrará cómo ajustar los tiempos de espera (timeouts) en un Ingress Nginx instalado en Google Kubernetes Engine (GKE) utilizando Helm.

## 1. Identificar la Release Name

Primero, identifica la release name que se utilizó al instalar Ingress Nginx. Puedes obtener esta información ejecutando el siguiente comando:

```bash
kubectl get deployment -n <NAMESPACE> <INGRESS_NGINX_DEPLOYMENT_NAME> -o=jsonpath={.metadata.labels."meta.helm.sh/release-name"}
```

Reemplaza `<NAMESPACE>` con el namespace en el que se instaló Ingress Nginx y `<INGRESS_NGINX_DEPLOYMENT_NAME>` con el nombre del deployment de Ingress Nginx.

## 2. Ajustar Tiempos de Espera con Helm

Utiliza el siguiente comando Helm para ajustar los tiempos de espera:

```bash
helm upgrade --set controller.config.proxy-connect-timeout="5s"
--set controller.config.proxy-send-timeout="600s"
--set controller.config.proxy-read-timeout="600s"
<RELEASE_NAME> ingress-nginx/ingress-nginx
```

Reemplaza `<RELEASE_NAME>` con la release name identificada en el paso anterior.

## 3. Verificar la Configuración

Después de la actualización, verifica que los cambios hayan sido aplicados correctamente:

```bash
kubectl get ing -n <NAMESPACE>

```

Observa la salida para confirmar que los nuevos tiempos de espera se reflejan en la configuración del Ingress Nginx.

## 4. Observar el Comportamiento

Observa el comportamiento de tu aplicación para asegurarte de que los cambios tengan el efecto deseado en el manejo de los tiempos de espera.

Con estos pasos, deberías poder ajustar los tiempos de espera en tu Ingress Nginx en GKE mediante Helm.

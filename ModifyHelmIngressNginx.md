# Guía para Modificar el Timeout del NGINX Ingress Controller en un Clúster de Kubernetes

### Pasos para aumentar los timeouts en NGINX Ingress Controller

1. **Obtener el ConfigMap actual:**

Para obtener el ConfigMap donde se almacenan las configuraciones de NGINX Ingress Controller, ejecuta el siguiente comando:

```bash
kubectl get configmap nginx-ingress-ingress-nginx-controller -n default -o yaml > nginx-configmap.yaml
```

Esto descargará el archivo de configuración del ConfigMap a tu máquina local.

2. **Editar el archivo del ConfigMap:**

Abre el archivo nginx-configmap.yaml en tu editor favorito, como nano:

```bash
nano nginx-configmap.yaml
```

3. **Agregar o modificar los parámetros de timeout:**

Dentro del archivo, busca o agrega las siguientes líneas para establecer los timeouts a 900 segundos (15 minutos):

```yaml

apiVersion: v1
data:
  proxy-read-timeout: "900"
  proxy-send-timeout: "900"
  proxy-connect-timeout: "900"
kind: ConfigMap
metadata:
  annotations:
    meta.helm.sh/release-name: nginx-ingress
    meta.helm.sh/release-namespace: default
  creationTimestamp: "2024-05-13T08:20:37Z"
  labels:
    app.kubernetes.io/component: controller
    app.kubernetes.io/instance: nginx-ingress
    app.kubernetes.io/managed-by: Helm
    app.kubernetes.io/name: ingress-nginx
    app.kubernetes.io/part-of: ingress-nginx
    app.kubernetes.io/version: 1.12.0
    helm.sh/chart: ingress-nginx-4.12.0
  name: nginx-ingress-ingress-nginx-controller
  namespace: default
  resourceVersion: "265550000"
  uid: 61bebbd1-404d-452e-a30b-575fa8bfb55a
```

4. **Aplicar los cambios:**

Una vez que hayas realizado los cambios, aplica el nuevo ConfigMap al clúster:

```bash
kubectl apply -f nginx-configmap.yaml
```

5. **Reiniciar el Deployment del NGINX Ingress Controller:**

Para aplicar los cambios, debes reiniciar el deployment del NGINX Ingress Controller. Usa el siguiente comando:

```bash
kubectl rollout restart deployment nginx-ingress-ingress-nginx-controller -n default
```

6. **Verificar los cambios:**

Puedes verificar los logs del Ingress Controller para asegurarte de que los cambios se aplicaron correctamente o realizar una prueba con una solicitud que normalmente habría expirado para comprobar si ahora se maneja correctamente el timeout.
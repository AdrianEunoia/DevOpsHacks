# Documentación de Configuración de Tamaño Máximo de Carga en NGINX Ingress para app-ingress

## Problema

Al realizar cargas de archivos a través de la API en el servicio Ingress `app-ingress`, se encontró un error **413 Payload Too Large**. Este error indica que el tamaño del archivo excede el límite máximo permitido por NGINX Ingress. La causa principal es que NGINX Ingress, por defecto, tiene un límite de tamaño de carga que restringe el tamaño de los archivos que pueden enviarse en las solicitudes HTTP.

## Solución

Para permitir cargas de archivos de mayor tamaño en `app-ingress`, es necesario aumentar el límite de tamaño de cuerpo (`client_max_body_size`) en NGINX Ingress. Esto se puede hacer agregando la anotación `nginx.ingress.kubernetes.io/proxy-body-size` al recurso Ingress correspondiente.

### Pasos para Configurar el Tamaño Máximo de Carga

#### 1. Editar el Recurso Ingress

La anotación `nginx.ingress.kubernetes.io/proxy-body-size` permite configurar el tamaño máximo de los cuerpos de las solicitudes HTTP en un recurso específico de Ingress. Para agregar esta anotación, sigue los pasos a continuación:

1. Si `app-ingress` ya está creado en tu clúster, abre el recurso Ingress para editarlo:

    ```bash
    kubectl edit ingress app-ingress
    ```

2. Agrega la siguiente anotación bajo `metadata`:

    ```yaml
    apiVersion: networking.k8s.io/v1
    kind: Ingress
    metadata:
      name: app-ingress
      annotations:
        nginx.ingress.kubernetes.io/proxy-body-size: "10m"
    spec:
      rules:
      - host: example.com
        http:
          paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: backend-backend
                port:
                  number: 80
    ```

   Esta configuración establece un límite de 10 MB para el tamaño de los cuerpos de las solicitudes HTTP en `app-ingress`.

#### 2. Aplicar el Archivo YAML (si prefieres gestionarlo como archivo)

Si deseas gestionar el Ingress mediante un archivo YAML, crea o edita el archivo con el contenido mostrado anteriormente y aplícalo al clúster:

```bash
kubectl apply -f <tu_archivo_yaml>.yaml
```

3. Verificar la Configuración
Para confirmar que la anotación se ha aplicado correctamente, puedes describir el recurso Ingress y revisar que proxy-body-size esté configurado:

```bash
kubectl describe ingress app-ingress
```

Deberías ver la anotación nginx.ingress.kubernetes.io/proxy-body-size: 10m en la salida.

## Conclusión
El error 413 Payload Too Large se puede solucionar al aumentar el límite de proxy-body-size en el recurso Ingress afectado. Esto permite cargas de archivos más grandes según lo configurado, sin modificar el límite global de NGINX Ingress en otros recursos. Esta anotación es efectiva para gestionar cargas de archivos de manera flexible y específica en Ingress.
# Documentación de Solución: Obtener IPs Reales de Clientes en Kubernetes detrás de Cloudflare

## Introducción

Nuestra aplicación en Kubernetes necesitaba capturar las IPs reales de los clientes, pero Cloudflare estaba mostrando solo las IPs de sus nodos. Esta es la solución que implementamos para obtener las IPs reales.

## Problema

Las IPs de los clientes no se mostraban correctamente debido a la intermediación de Cloudflare.

## Solución

### Paso a Paso

1. **Revisión de la Documentación de Cloudflare:**
   - Consultamos la documentación de Cloudflare sobre [encabezados HTTP](https://developers.cloudflare.com/fundamentals/reference/http-request-headers/#cf-connecting-ip).

2. **Configuración del ConfigMap del Ingress Nginx:**
   - Añadimos las siguientes anotaciones al `ConfigMap` del ingress nginx:

     ```yaml
     enable-real-ip: "true"
     compute-full-forwarded-for: "true"
     use-forwarded-headers: "true"
     ```

3. **Actualización del ConfigMap:**
   - Aplicamos el `ConfigMap` con las nuevas configuraciones.

4. **Reinicio del Ingress Nginx:**
   - Reiniciamos el ingress nginx para aplicar los cambios:

     ```bash
     kubectl rollout restart deployment nginx-ingress-controller -n ingress-nginx
     ```

5. **Verificación de Logs y Backends:**
   - Confirmamos que los logs muestran las IPs reales y que el backend puede leerlas mediante los encabezados `X-Forwarded-For` y `CF-Connecting-IP`.

### Ejemplo de Lectura de IPs en el Backend (Node.js)

```javascript
const express = require('express');
const app = express();

app.use((req, res, next) => {
  const realIp = req.headers['cf-connecting-ip'] || req.headers['x-forwarded-for'] || req.connection.remoteAddress;
  console.log('Client IP:', realIp);
  next();
});

app.get('/', (req, res) => {
  res.send('Hello, world!');
});

app.listen(3000, () => {
  console.log('Server is running on port 3000');
});
```

## Resultados
- Logs Actualizados: Los logs del ingress nginx ahora muestran las IPs reales.
- Backend: El backend puede leer las IPs reales usando los encabezados HTTP adecuados.

## Conclusión
- Configurando el ingress nginx y utilizando los encabezados proporcionados por Cloudflare, logramos obtener las IPs reales de los clientes, mejorando nuestro registro y análisis de tráfico.
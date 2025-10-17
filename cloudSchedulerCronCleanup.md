# 🔔 Aviso de Google Cloud: Cambio en Audit Logs de Cloud Scheduler

Google ha enviado un aviso informando que **a partir del 15 de septiembre de 2025**, los **audit logs de Cloud Scheduler** empezarán a emitirse en un **nuevo formato estándar (GFE format)**.
Durante un periodo de transición se mantendrán ambos formatos, pero **a partir del 30 de septiembre de 2026 el formato antiguo dejará de existir**.

---

## ❓ ¿Por qué puede afectarnos?

Se han detectado antiguos cron jobs de App Engine que ya no se usan, pero siguen ejecutándose automáticamente y generando errores y audit logs.

Estos logs provienen de **App Engine Cron (basado en Cloud Tasks / Scheduler)** y tienen esta estructura en Cloud Logging:

```
protoPayload.taskName="XXXXXXXX"
protoPayload.taskQueueName="__cron"
```

Aunque App Engine puede estar **deshabilitado**, los cron jobs quedaron **"zombies"** y **siguen ejecutándose en segundo plano**, generando:

* Errores innecesarios en Logging
* Audit logs en el **formato antiguo que Google va a eliminar**
* Posibles impactos en automatismos que leen esos logs

---

## ✅ Solución recomendada

### 1. Confirmar la región de App Engine

```bash
gcloud app describe --project=PROJECT_ID
```

Anotar el valor de `locationId`.

---

### 2. Reactivar App Engine temporalmente (si es necesario)

Si App Engine está detenido, hay que activarlo en la misma región:

```bash
gcloud app create --project=PROJECT_ID --region=REGION
```

> Sustituir `REGION` por el valor obtenido en el paso anterior.

---

### 3. Eliminar los cron jobs huérfanos cargando un `cron.yaml` vacío

Crear un fichero llamado `cron.yaml` con el siguiente contenido:

```yaml
cron:
```

Ejecutar:

```bash
gcloud app deploy cron.yaml --project=PROJECT_ID
```

Esto **sobrescribe y elimina todos los cron jobs existentes**.

---

### 4. (Opcional) Volver a desactivar App Engine / bloquear ejecuciones futuras

Si no queremos que ningún cron vuelva a ejecutarse:

```bash
gcloud projects remove-iam-policy-binding PROJECT_ID \
  --member="serviceAccount:service-PROJECT_NUMBER@gcp-sa-cloudscheduler.iam.gserviceaccount.com" \
  --role="roles/cloudscheduler.serviceAgent"
```

> Sustituir `PROJECT_NUMBER` por el ID numérico del proyecto.

---

## 🧹 Resultado esperado

| Estado                       | Resultado             |
| ---------------------------- | --------------------- |
| Cron jobs eliminados         | ✅ Dejan de ejecutarse |
| Errores en Logging           | ✅ Dejan de aparecer   |
| Audit logs obsoletos         | ✅ Ya no se generan    |
| Impacto del cambio de Google | 🚫 No nos afecta más  |

---

## 📌 Nota final

Este cambio de Google **no afecta a logging normal de Kubernetes o contenedores**, solo a **audit logs generados por Cloud Scheduler / App Engine Cron / Cloud Tasks**.

Al eliminar estos cron jobs antiguos, **el aviso deja de tener impacto en nuestro entorno.**
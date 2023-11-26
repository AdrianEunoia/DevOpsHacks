# Integración de Cuenta de Servicio en GitHub Actions

Este documento describe cómo integrar una cuenta de servicio de Google Cloud Platform (GCP) en GitHub Actions utilizando un secreto cifrado en formato base64.

## 1. Crear un archivo de cuenta de servicio JSON

Primero, crea un archivo JSON que contenga las credenciales de tu cuenta de servicio. Asegúrate de que el archivo tenga un formato JSON válido. Aquí tienes un ejemplo:

```json
{
  "type": "service_account",
  "project_id": "tu-id-de-proyecto",
  "private_key_id": "tu-id-de-clave-privada",
  "private_key": "-----BEGIN PRIVATE KEY-----\ntu-clave-privada\n-----END PRIVATE KEY-----\n",
  "client_email": "tucuenta@tudominio.iam.gserviceaccount.com",
  "client_id": "tu-id-de-cliente",
  "auth_uri": "https://accounts.google.com/o/oauth2/auth",
  "token_uri": "https://oauth2.googleapis.com/token",
  "auth_provider_x509_cert_url": "https://www.googleapis.com/oauth2/v1/certs",
  "client_x509_cert_url": "https://www.googleapis.com/robot/v1/metadata/x509/tucuenta%40tudominio.iam.gserviceaccount.com"
}
```

## 2. Convertir el archivo a base64
Usa el siguiente comando para convertir el archivo service-account.json a formato base64:

```bash
base64 service-account.json > service-account-base64.txt
```

## 3. Crear un secreto en GitHub
Sube el archivo base64 como un secreto en tu repositorio de GitHub. En el menú del repositorio, ve a "Settings" -> "Secrets" -> "New repository secret". Asigna un nombre como GCP_SVCACC y pega el contenido del archivo service-account-base64.txt.

## 4. GitHub Actions
Agrega el siguiente paso en tu flujo de trabajo de GitHub Actions para decodificar el secreto base64 y escribirlo en service-account.json:

```yaml
jobs:
  decode-secret:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout repository
      uses: actions/checkout@v3

    - name: Decodificar secreto base64 y escribir en service-account.json
      run: echo "${{ secrets.GCP_SVCACC }}" | base64 -d > service-account.json

    - name: Mostrar contenido del archivo decodificado
      run: cat service-account.json
```
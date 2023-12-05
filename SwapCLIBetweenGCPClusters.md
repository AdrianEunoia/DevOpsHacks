# CLI para Selección de Proyectos

## Descripción

Esta CLI (Interfaz de Línea de Comandos) simplifica la gestión de proyectos al permitir a los usuarios seleccionar fácilmente un proyecto específico y configurar las credenciales del clúster asociado. La CLI presenta opciones para varios proyectos, cada uno con su propia configuración única.

Script:
```bash
#!/bin/bash

# Crear directorio para scripts si no existe
mkdir -p ~/.scripts

# Crear el script ejecutar_proyecto.sh
cat <<EOF > ~/.scripts/ejecutar_proyecto.sh
#!/bin/bash

echo "Selecciona un proyecto:"
echo "1) Proyecto A"
echo "2) Proyecto B"
echo "3) Proyecto C"

read opcion

case \$opcion in
    1)
        # Configuración del Proyecto A
        gcloud config set project proyecto-a
        rm ~/.kube/config
        gcloud container clusters get-credentials cluster-a --zone=europe-west1-b
        cat ~/.kube/config
        ;;
    2)
        # Configuración del Proyecto B
        gcloud config set project proyecto-b
        rm ~/.kube/config
        gcloud container clusters get-credentials cluster-b --zone=europe-west1-c
        cat ~/.kube/config
        ;;
    3)
        # Configuración del Proyecto C
        gcloud config set project proyecto-c
        rm ~/.kube/config
        gcloud container clusters get-credentials cluster-c --zone=europe-west1-d
        cat ~/.kube/config
        ;;
    *)
        echo "Opción no válida"
        ;;
esac
EOF

# Dar permisos de ejecución al script
chmod +x ~/.scripts/ejecutar_proyecto.sh

# Configurar alias en el perfil de shell
echo "alias patata='~/.scripts/ejecutar_proyecto.sh'" >> ~/.bashrc   # o ~/.zshrc si usas Zsh

# Actualizar la sesión de terminal
export PATH=$PATH:~/.scripts
source ~/.bashrc   # o source ~/.zshrc si usas Zsh

echo "La instalación se ha completado. Ahora puedes ejecutar 'patata' en la consola para seleccionar un proyecto."

```

Uso
Ejecuta el siguiente comando para seleccionar un proyecto:

```
patata
```

La CLI te presentará una lista de proyectos disponibles. Elige el proyecto deseado ingresando el número correspondiente y presiona Enter. La CLI configurará automáticamente el proyecto seleccionado y obtendrá las credenciales del clúster asociado.
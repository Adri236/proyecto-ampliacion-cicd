# Proyecto de Despliegue Continuo (CI/CD) con GitHub Actions, Docker y AWS

Este repositorio contiene un pipeline automatizado de Integración y Despliegue Continuo (CI/CD) para una aplicación web estática. Cada vez que se realiza un cambio en el código fuente y se sube a la rama principal, el sistema se actualiza automáticamente en la nube sin intervención manual.

## 📋 Arquitectura y Componentes

El proyecto está compuesto por los siguientes elementos clave:

* **`index.html`**: La página web estática de la aplicación, diseñada con HTML5 y CSS3 adaptativo.
* **`Dockerfile`**: Archivo de configuración de Docker que define cómo empaquetar la aplicación. Utiliza una imagen ligera de **Nginx (Alpine)** como servidor web y copia los archivos locales en la ruta pública del contenedor.
* **`.github/workflows/deploy.yml`**: El cerebro de la automatización. Un archivo de configuración en formato YAML que define el pipeline de GitHub Actions.

## ⚙️ Funcionamiento del Pipeline (CI/CD)

Cuando se ejecuta un `git push origin main`, GitHub Actions dispara automáticamente un flujo de trabajo que realiza los siguientes pasos:

1.  **Descarga del código**: El robot obtiene la última versión del repositorio.
2.  **Autenticación en Docker Hub**: Se conecta de forma segura al registro de contenedores utilizando credenciales encriptadas.
3.  **Compilación y Push**: Construye la imagen Docker a partir del `Dockerfile` local y la sube a Docker Hub con la etiqueta `latest`.
4.  **Despliegue en AWS EC2**: Se conecta mediante SSH al servidor en la nube, descarga la nueva imagen desde Docker Hub, detiene el contenedor antiguo y levanta el nuevo contenedor en el puerto 80.

## 🔐 Seguridad y Secretos de GitHub

Para proteger la integridad del servidor y las credenciales de las plataformas, no se han incluido datos sensibles directamente en el código. En su lugar, se utilizan **GitHub Repository Secrets**, que guardan de forma encriptada las siguientes variables esenciales:

* `DOCKER_HUB_USERNAME`: Usuario de Docker Hub para el almacenamiento de imágenes.
* `DOCKER_HUB_TOKEN`: Token de acceso personal con permisos de lectura y escritura.
* `EC2_HOST`: La dirección IP Elástica del servidor de AWS.
* `EC2_USERNAME`: El usuario por defecto del sistema operativo del servidor (`ubuntu`).
* `EC2_SSH_KEY`: La clave privada OpenSSH (`.pem`) necesaria para realizar la conexión remota segura.

## 🚀 Infraestructura (AWS & Docker Hub)

* **Docker Hub**: Actúa como el nexo de unión y almacén de las imágenes de la aplicación, permitiendo que el servidor disponga siempre de la versión exacta compilada por el pipeline.
* **AWS EC2**: Instancia de servidor virtual en Amazon Web Services (Ubuntu Server) configurada con una IP Elástica permanente y reglas de entrada en el Grupo de Seguridad que permiten el tráfico por los puertos **22 (SSH)** para el despliegue y **80 (HTTP)** para que cualquier usuario pueda visitar la web.
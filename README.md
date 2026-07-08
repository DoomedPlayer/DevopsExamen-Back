# ⚙️ Innovatech Chile - Backend Microservicios

Este repositorio contiene la lógica de negocio y la capa de acceso a datos para la plataforma de Innovatech Chile. El backend está estructurado bajo una arquitectura de microservicios independientes que se comunican con una base de datos centralizada, diseñados para ser escalables y tolerantes a fallos en un entorno Cloud nativo.

## 🏗️ Arquitectura y Escalabilidad
El ecosistema consta de dos microservicios principales:
1. **Microservicio de Ventas (`port: 8082`):** Gestiona la creación y consulta de las órdenes de compra de los clientes.
2. **Microservicio de Despachos (`port: 8081`):** Administra la logística, asignación de patentes de camión, control de intentos de entrega y cierre definitivo de las órdenes (estado entregado).

La infraestructura productiva corre sobre **Amazon EKS** y cuenta con una **Estrategia de Autoescalado de Doble Capa**:
* **Autoescalado de Aplicación (HPA):** Configurado para escalar dinámicamente el número de réplicas si el consumo de CPU de los contenedores supera el 50%.
* **Autoescalado de Infraestructura (Cluster Autoscaler):** Integrado de forma nativa con un Auto Scaling Group de EC2 para aprovisionar automáticamente nuevos servidores físicos si la demanda de los contenedores supera la capacidad actual del clúster.

## 🐳 Contenedorización y Seguridad
Los microservicios fueron empaquetados siguiendo estrictas buenas prácticas de seguridad e ingeniería DevOps:
* **Multi-stage Builds:** Para optimizar el proceso de compilación y transferir únicamente el artefacto ejecutable final, reduciendo el tamaño de la imagen.
* **Imágenes Minimalistas:** Uso de distribuciones `alpine` para minimizar la superficie de ataque del sistema.
* **Mínimo Privilegio:** Los contenedores se ejecutan bajo un usuario dedicado (non-root) sin privilegios de administrador.

## 🚀 Instalación y Ejecución Local

### Prerrequisitos
* Java Development Kit (JDK) 17+
* Maven 3.8+
* Docker y Docker Compose (Recomendado)

### Pasos de Ejecución
1. Clonar el repositorio del examen:
   ```bash
   git clone [https://github.com/DoomedPlayer/DevopsExamen-Back.git](https://github.com/DoomedPlayer/DevopsExamen-Back.git)

2. Si deseas probar el código sin contenedores, compila el proyecto y descarga las dependencias:
   ```bash
   mvn clean install
3. Ejecutar los microservicios (debes levantar cada proyecto por separado):
   ```bash
   mvn spring-boot:run
   
## 🔄 Integración y Despliegue Continuo (CI/CD)
Este repositorio implementa automatización DevOps completa mediante GitHub Actions. Al recibir un git push en la rama principal:
1. Build: El pipeline compila el código y genera los archivos ejecutables .jar de ambos microservicios.
2. Dockerize: Se construyen las imágenes Docker individuales para Ventas y Despachos.
3. Push: Se realiza la subida (Push) de forma segura hacia un registro privado en Amazon ECR.
4. Cada imagen es versionada automáticamente utilizando el hash del commit (github.sha) para garantizar trazabilidad.Deploy: El pipeline despliega automáticamente las nuevas versiones en el clúster de AWS EKS mediante comandos kubectl, inyectando las credenciales de la base de datos de producción a través de secretos configurados en GitHub (GitHub Secrets), asegurando que ninguna contraseña o variable sensible quede expuesta en el código fuente.

# WildFly-e-nforme-RA3


# P5.2 - Despliegue de una API RESTful con Jakarta (Java) usando Gradle en WildFly (contenedor)

## 1. Preparar el entorno Docker

### 1.1 Descargar la imagen de WildFly

Primero abrimos la terminal y nos aseguramos de que Docker Desktop esté corriendo. Luego descargamos la imagen oficial de WildFly:

```bash
docker pull quay.io/wildfly/wildfly:latest
```

**¿Para qué sirve?** Descarga la imagen del servidor WildFly desde el registro de contenedores. Solo se hace una vez.

![Descarga de imagen WildFly](https://github.com/user-attachments/assets/839b0b12-f283-46b6-aa43-927cef20973b)

---

### 1.2 Levantar el contenedor WildFly

```bash
docker run -d --name wildfly -p 8080:8080 -p 9990:9990 quay.io/wildfly/wildfly:latest /opt/jboss/wildfly/bin/standalone.sh -b 0.0.0.0 -bmanagement 0.0.0.0
```

**¿Para qué sirve cada parámetro?**
| Parámetro | Descripción |
|-----------|-------------|
| `-d` | Ejecuta el contenedor en segundo plano |
| `--name wildfly` | Asigna el nombre "wildfly" al contenedor |
| `-p 8080:8080` | Expone el puerto 8080 (aplicaciones web) |
| `-p 9990:9990` | Expone el puerto 9990 (consola de administración) |
| `-b 0.0.0.0` | Permite conexiones desde cualquier IP |
| `-bmanagement 0.0.0.0` | Permite acceso externo a la consola de administración |

![Levantar contenedor](https://github.com/user-attachments/assets/af3d27e3-b66e-4be2-9c22-d18f9120d0dd)

---

### 1.3 Verificar que el contenedor está corriendo

```bash
docker ps
```

**¿Para qué sirve?** Muestra los contenedores activos. Debemos ver "wildfly" con STATUS "Up".

![docker ps](https://github.com/user-attachments/assets/ecf20f16-4549-4aae-974e-a9b35706194f)

---

## 2. Crear usuario de administración

```bash
docker exec -it wildfly /opt/jboss/wildfly/bin/add-user.sh
```

**¿Para qué sirve?** Ejecuta el script de creación de usuarios dentro del contenedor. Es necesario para acceder a la consola de administración.

**Respuestas a las preguntas:**
- **What type of user?** → Pulsar `a` (Management User)
- **Username** → Escribir un nombre (ej: `admin`)
- **Password** → Contraseña segura (mínimo 8 caracteres, con letra, número y símbolo)
- **Re-enter Password** → Repetir la contraseña
- **Groups** → Dejar vacío, pulsar Enter
- **Is this correct?** → Escribir `yes`
- **Conectar a otros procesos** → Escribir `no`

![Crear usuario](https://github.com/user-attachments/assets/794eb5b3-5f61-4367-83ac-dc0b4fa3c98f)

---

## 3. Verificar acceso a WildFly

### 3.1 Página de bienvenida

**URL:** http://localhost:8080

![Página bienvenida WildFly](https://github.com/user-attachments/assets/f017a4a7-a7bd-45fe-b6c0-fa33b4d1df4d)

### 3.2 Consola de administración

**URL:** http://localhost:9990

![Consola administración](https://github.com/user-attachments/assets/c0110a5b-4c49-4d27-8c4a-b0ca13b76051)

---

## 4. Clonar el repositorio del ejercicio

```bash
cd C:\Users\pabli
git clone https://github.com/revilofe/2526_DAW_u5.3_jakarta-wildfly-gradle-otra
```

**¿Para qué sirve?** Descarga el código fuente del proyecto desde GitHub a nuestra máquina local.

![Git clone](https://github.com/user-attachments/assets/4c9e42b5-3fcb-4784-a67c-305173acd33f)

Entramos en la carpeta del proyecto:

```bash
cd 2526_DAW_u5.3_jakarta-wildfly-gradle-otra
```

---

## 5. Compilar y generar el WAR

```bash
gradlew.bat clean build
```

**¿Para qué sirve?**
- `clean`: Limpia compilaciones anteriores
- `build`: Compila el proyecto y genera el archivo WAR

**Resultado esperado:** `BUILD SUCCESSFUL`

![Build successful](https://github.com/user-attachments/assets/8ffcc598-14e5-420d-be53-4c3cc949f976)

---

## 6. Verificar el WAR generado

```bash
dir build\libs
```

**¿Para qué sirve?** Muestra los archivos generados en la carpeta de salida. Buscamos el archivo `crud-file.war`.

![WAR generado](https://github.com/user-attachments/assets/dcda275e-8659-42ce-a5fe-277ee3349f65)

---

## 7. Desplegar el WAR en WildFly

```bash
docker cp build\libs\crud-file.war wildfly:/opt/jboss/wildfly/standalone/deployments/
```

**¿Para qué sirve?** Copia el archivo WAR desde nuestra máquina al directorio de despliegues del contenedor WildFly. WildFly detecta automáticamente el nuevo archivo y lo despliega.

![Docker cp](https://github.com/user-attachments/assets/dced4f2a-ad32-4d81-9f3a-e6afc1bcdd91)

---

## 8. Verificar el despliegue en los logs

```bash
docker logs -f wildfly
```

**¿Para qué sirve?** Muestra los logs del contenedor en tiempo real. Buscamos el mensaje `Deployed "crud-file.war"` que confirma el despliegue exitoso.

![Logs despliegue](https://github.com/user-attachments/assets/bb9e1d55-17a0-4467-b9a2-e658eb1dc8a3)

---

## 9. Probar la aplicación

### 9.1 Acceso desde el navegador

**URL:** http://localhost:8080/crud-file

![Aplicación CRUD](https://github.com/user-attachments/assets/8475c7cd-2978-4042-bb5f-b247a8adafb0)

### 9.2 Estructura del código fuente

```bash
dir src\main\java\com\example\tasks
```

**¿Para qué sirve?** Muestra las clases Java que componen la aplicación.

![Estructura código](https://github.com/user-attachments/assets/5cd89201-bea1-4f00-9d91-84e477495a6d)

### 9.3 Crear una tarea desde la interfaz web

![Nueva tarea](https://github.com/user-attachments/assets/ead59073-94ae-4da5-8224-bc85b8e0b771)

---

## 10. Pruebas con curl

### 10.1 Listar todas las tareas (GET)

```bash
curl http://localhost:8080/crud-file/api/tasks
```

**¿Para qué sirve?** Obtiene la lista de todas las tareas almacenadas.

![GET tasks](https://github.com/user-attachments/assets/d139c541-6bfb-4587-ab2c-cba2de04467c)

### 10.2 Crear una tarea nueva (POST)

```bash
curl -X POST http://localhost:8080/crud-file/api/tasks -H "Content-Type: application/json" -d "{\"title\": \"Tarea desde curl\", \"completed\": false}"
```

**¿Para qué sirve?** Crea una nueva tarea enviando datos en formato JSON.

| Parámetro | Descripción |
|-----------|-------------|
| `-X POST` | Indica que es una petición POST |
| `-H "Content-Type: application/json"` | Especifica que enviamos JSON |
| `-d "{...}"` | Los datos de la tarea a crear |

![POST task](https://github.com/user-attachments/assets/0e6c7e19-230e-4017-8fba-8f4d6189e490)

### 10.3 Obtener una tarea por ID (GET)

```bash
curl http://localhost:8080/crud-file/api/tasks/1
```

**¿Para qué sirve?** Obtiene los datos de una tarea específica (en este caso, la tarea con ID 1).

![GET task by ID](https://github.com/user-attachments/assets/691bc485-e1e7-43d5-b93d-10ad29bd3068)

### 10.4 Actualizar una tarea (PUT)

```bash
curl -X PUT http://localhost:8080/crud-file/api/tasks/1 -H "Content-Type: application/json" -d "{\"title\": \"Tarea actualizada\", \"completed\": true}"
```

**¿Para qué sirve?** Modifica los datos de una tarea existente.

![PUT task](https://github.com/user-attachments/assets/c028b8be-1b7a-44d8-9054-f22f23f49b4d)

### 10.5 Eliminar una tarea (DELETE)

```bash
curl -X DELETE http://localhost:8080/crud-file/api/tasks/1
```

**¿Para qué sirve?** Elimina una tarea por su ID.

![DELETE task](https://github.com/user-attachments/assets/9b2e4ece-7d74-4c6c-ba0f-5ba0d87b36c3)

---

## 11. Prueba de carga y rendimiento

### Herramienta utilizada: hey

```bash
hey.exe -n 1000 -c 10 -H "Accept: application/json" -H "Content-Type: application/json" http://localhost:8080/crud-file/api/tasks
```

**¿Para qué sirve?** Realiza una prueba de carga enviando múltiples peticiones simultáneas para evaluar el rendimiento del servidor.

| Parámetro | Descripción |
|-----------|-------------|
| `-n 1000` | Número total de peticiones a enviar |
| `-c 10` | Número de peticiones concurrentes (simultáneas) |
| `-H "..."` | Cabeceras HTTP necesarias |

![Prueba hey](https://github.com/user-attachments/assets/d39d0dc5-f13e-4ec2-9374-7dec2ed1d6fb)

### Resultados

| Métrica | Valor |
|---------|-------|
| Peticiones/segundo | ~4966 |
| Tiempo medio respuesta | 1.9 ms |
| Peticiones exitosas | 1000 (100%) |
| Código de estado | 200 OK |

(El servidor es estable, procesó las 1000 peticiones sin errores y con tiempos de respuesta muy buenos)





































# P5.3 - Informe RA3 sobre el despliegue (WildFly contenedor + Gradle)

## Índice
- [a) Componentes y funcionamiento](#a-componentes-y-funcionamiento-de-los-servicios-del-servidor)
- [b) Archivos de configuración](#b-archivos-principales-de-configuración-y-bibliotecas-compartidas)
- [c) Reverse Proxy y HTTPS](#c-cooperación-con-el-servidor-web-proxy--reverse-proxy-y-https)
- [d) Mecanismos de seguridad](#d-mecanismos-de-seguridad-del-servidor-de-aplicaciones)
- [e) Componentes web](#e-componentes-web-del-servidor-de-aplicaciones)
- [f) Parámetros de despliegue](#f-parámetros-necesarios-para-el-despliegue)
- [g) Pruebas de funcionamiento y rendimiento](#g-pruebas-de-funcionamiento-y-rendimiento)
- [h) Documentación de administración](#h-documentación-de-administración-y-recomendaciones)
- [i) Docker Compose](#i-virtualización-nube-o-contenedores-en-el-despliegue)

---

## a) Componentes y funcionamiento de los servicios del servidor

### Componentes del despliegue

| Componente | Descripción | Función |
|------------|-------------|---------|
| **Docker** | Motor de contenedores | Proporciona el entorno aislado y reproducible donde se ejecuta WildFly |
| **Contenedor WildFly** | Imagen `quay.io/wildfly/wildfly:latest` | Servidor de aplicaciones Jakarta EE que ejecuta nuestra API REST |
| **Aplicación WAR** | `crud-file.war` | Artefacto desplegable que contiene la API REST de gestión de tareas |
| **Puerto 8080** | Puerto HTTP | Exposición de las aplicaciones web desplegadas |
| **Puerto 9990** | Puerto de administración | Acceso a la consola de gestión de WildFly |
| **Endpoint REST** | `/crud-file/api/tasks` | API RESTful para operaciones CRUD sobre tareas |


### Evidencias

**Contenedor activo con puertos publicados (`docker ps`):**

<img width="2233" height="109" alt="image" src="https://github.com/user-attachments/assets/add2b8b4-f439-4a5d-828b-6b375b5a447d" />


**Logs de despliegue del WAR:**

<img width="1606" height="46" alt="image" src="https://github.com/user-attachments/assets/c4d7e3cc-d586-480a-9998-432941dacb35" />


**Logs de acceso durante prueba de carga:**

```
23:08:00,897 INFO  [com.example.tasks.TaskResource] (default task-26) Listar tareas
23:08:00,897 INFO  [com.example.tasks.FileTaskStore] (default task-26) Operacion findAll
23:08:00,898 INFO  [com.example.tasks.TaskResource] (default task-7) Listar tareas
...
```

---

## b) Archivos principales de configuración y bibliotecas compartidas

### Archivos de configuración de WildFly

**Ruta:** `/opt/jboss/wildfly/standalone/configuration/`

**Archivos principales:**

| Archivo | Descripción |
|---------|-------------|
| `standalone.xml` | Configuración principal del servidor (subsistemas, datasources, logging, etc.) |
| `standalone-full.xml` | Configuración completa con todos los subsistemas |
| `standalone-ha.xml` | Configuración para alta disponibilidad (clustering) |
| `mgmt-users.properties` | Usuarios de administración |
| `mgmt-groups.properties` | Grupos de administración |
| `application-users.properties` | Usuarios de aplicación |
| `logging.properties` | Configuración del sistema de logging |

**Contenido del directorio de configuración:**

```bash
$ docker exec -it wildfly ls /opt/jboss/wildfly/standalone/configuration

application-roles.properties  mgmt-groups.properties   standalone-full.xml       standalone-microprofile.xml
application-users.properties  mgmt-users.properties    standalone-full-ha.xml    standalone.xml
logging.properties            standalone-ha.xml        standalone-load-balancer.xml  standalone_xml_history
```

**Fragmento de standalone.xml (extensiones cargadas):**

```
<?xml version="1.0" encoding="UTF-8"?>
<server xmlns="urn:jboss:domain:community:20.0">
  <extensions>
    <extension module="org.jboss.as.clustering.infinispan"/>
    <extension module="org.jboss.as.connector"/>
    <extension module="org.jboss.as.deployment-scanner"/>
    <extension module="org.jboss.as.ee"/>
    <extension module="org.jboss.as.ejb3"/>
    <extension module="org.jboss.as.jaxrs"/>
    <extension module="org.jboss.as.jpa"/>
    <extension module="org.jboss.as.logging"/>
    <extension module="org.wildfly.extension.undertow"/>
  </extensions>
</server>
```

### Ajustes típicos en standalone.xml

| Sección | Qué se configura |
|---------|------------------|
| `<datasources>` | Conexiones a bases de datos (MySQL, PostgreSQL, etc.) |
| `<logging>` | Niveles de log, handlers, formatos |
| `<undertow>` | Servidor web: puertos, virtual hosts, filtros |
| `<security-domains>` | Autenticación y autorización |
| `<socket-binding-group>` | Puertos del servidor |

### Dependencias "provided" en el proyecto

**build.gradle:**

```groovy
dependencies {
    compileOnly 'jakarta.platform:jakarta.jakartaee-api:10.0.0'
}
```

**Explicación:**

- `compileOnly` (equivalente a `provided` en Maven) indica que la dependencia es necesaria para compilar, pero NO se incluye en el WAR final
- WildFly ya incluye la implementación completa de Jakarta EE 10
- **Ventajas:**
  - WAR más pequeño (13 KB en lugar de varios MB)
  - Evita conflictos de versiones entre la app y el servidor
  - Actualizaciones de seguridad del servidor se aplican automáticamente

---

## c) Cooperación con el servidor web (proxy / reverse proxy) y HTTPS

### Situación actual vs. Producción

**Despliegue actual (desarrollo):**
```
[Cliente] → localhost:8080 → [WildFly:8080]
[Cliente] → localhost:9990 → [WildFly:9990] (Consola admin)
```

**Despliegue propuesto (producción):**
```
[Cliente] → HTTPS:443 → [Nginx] → HTTP:8080 → [WildFly]
                                   (interno, no expuesto)
```

### Configuración de Nginx como Reverse Proxy

**nginx.conf:**

```nginx
upstream wildfly {
    server wildfly:8080;
}

server {
    listen 80;
    server_name midominio.com;
    
    # Redirigir todo HTTP a HTTPS
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name midominio.com;

    # Certificados SSL
    ssl_certificate /etc/nginx/ssl/fullchain.pem;
    ssl_certificate_key /etc/nginx/ssl/privkey.pem;
    
    # Configuración SSL moderna
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256;
    ssl_prefer_server_ciphers off;

    # Proxy hacia la API
    location /api/ {
        proxy_pass http://wildfly/crud-file/api/;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    # Bloquear acceso a la consola de administración
    location /console {
        deny all;
        return 404;
    }
}
```

### Cambios respecto al despliegue actual

| Aspecto | Desarrollo (actual) | Producción (propuesto) |
|---------|---------------------|------------------------|
| Puerto expuesto al exterior | 8080, 9990 | Solo 443 (HTTPS) |
| Acceso a consola admin | Público | Bloqueado/VPN |
| Cifrado | No (HTTP) | Sí (HTTPS/TLS) |
| URL de la API | `localhost:8080/crud-file/api/tasks` | `midominio.com/api/tasks` |

### Configuración TLS (HTTPS)

**Generación de certificados (Let's Encrypt con certbot):**

```bash
certbot certonly --webroot -w /var/www/html -d midominio.com
```

**Ventajas de HTTPS:**

1. **Cifrado**: Los datos viajan encriptados, protegiendo información sensible
2. **Integridad**: Se detecta cualquier modificación de los datos en tránsito
3. **Autenticación**: El cliente verifica que se conecta al servidor correcto
4. **SEO**: Google favorece sitios HTTPS en los rankings
5. **Requisito moderno**: Navegadores marcan HTTP como "No seguro"

**¿Cambiaría algo en WildFly o la aplicación?**

- **WildFly**: No requiere cambios. Sigue escuchando en HTTP internamente
- **Aplicación**: No requiere cambios. Nginx maneja el SSL termination
- **Configuración adicional**: Configurar WildFly para confiar en las cabeceras `X-Forwarded-*` si se necesita saber la IP real del cliente

---

## d) Mecanismos de seguridad del servidor de aplicaciones

### Medidas aplicadas en P5.2 (desarrollo)

| Medida | Implementación |
|--------|----------------|
| Usuario de administración | Creado con `add-user.sh` (usuario: admin) |
| Contraseña segura | Mínimo 8 caracteres, letra, número y símbolo |
| Contenedor aislado | Docker proporciona aislamiento del host |

### Medidas adicionales para producción (mínimo 4)

#### 1. Protección de la consola de administración (9990)

**Problema:** En desarrollo expusimos el puerto 9990 con `-bmanagement 0.0.0.0`

**Solución producción:**
```bash
# NO exponer el puerto 9990 al exterior
docker run -d --name wildfly \
  -p 8080:8080 \
  # NO incluir: -p 9990:9990
  wildfly:latest
```

Acceso solo mediante:
- Túnel SSH: `ssh -L 9990:localhost:9990 servidor`
- VPN corporativa

#### 2. Gestión de secretos

**Problema:** Contraseñas en texto plano o en comandos

**Solución:** Usar Docker Secrets o variables de entorno

```yaml
# docker-compose.yml
services:
  wildfly:
    environment:
      - ADMIN_PASSWORD_FILE=/run/secrets/admin_password
    secrets:
      - admin_password

secrets:
  admin_password:
    file: ./secrets/admin_password.txt
```

**Nunca hacer:**
- Guardar contraseñas en el repositorio Git
- Escribir contraseñas en Dockerfile o docker-compose.yml

#### 3. Registro y trazabilidad (Logs)

**Configuración de logging persistente:**

```yaml
volumes:
  - ./logs:/opt/jboss/wildfly/standalone/log
```

**Información a registrar:**
- Accesos a la API (quién, cuándo, qué)
- Errores y excepciones
- Intentos de autenticación fallidos

**Rotación de logs** para evitar llenar el disco:
```
<!-- En standalone.xml -->
<periodic-rotating-file-handler name="FILE">
    <suffix value=".yyyy-MM-dd"/>
    <file path="server.log" relative-to="jboss.server.log.dir"/>
</periodic-rotating-file-handler>
```

#### 4. Hardening del contenedor

**Ejecutar como usuario no-root:**
```dockerfile
USER jboss
```

**Sistema de archivos de solo lectura:**
```yaml
services:
  wildfly:
    read_only: true
    tmpfs:
      - /tmp
    volumes:
      - ./deployments:/opt/jboss/wildfly/standalone/deployments
      - ./data:/opt/jboss/wildfly/standalone/data
```

**Limitar recursos:**
```yaml
services:
  wildfly:
    deploy:
      resources:
        limits:
          cpus: '2'
          memory: 1G
```

### Resumen de seguridad

| Capa | Desarrollo | Producción |
|------|------------|------------|
| Red | Puertos 8080, 9990 expuestos | Solo 443 vía Nginx |
| Autenticación | Usuario admin básico | + Certificados SSL cliente |
| Secretos | En comandos | Docker Secrets/Vault |
| Logs | En contenedor (volátiles) | Volumen persistente + rotación |
| Contenedor | Usuario por defecto | non-root, read-only, recursos limitados |

---

## e) Componentes web del servidor de aplicaciones

### ¿Qué es el WAR?

**WAR (Web Application Archive)** es el formato estándar de empaquetado para aplicaciones web Java/Jakarta EE.

**Estructura del WAR `crud-file.war`:**

```
crud-file.war
├── WEB-INF/
│   ├── classes/
│   │   └── com/example/tasks/
│   │       ├── Task.class
│   │       ├── TaskResource.class
│   │       ├── TaskStore.class
│   │       ├── FileTaskStore.class
│   │       └── TasksApplication.class
│   └── web.xml (opcional en Jakarta EE)
├── index.html (interfaz web)
├── css/
└── js/
```

**Contenido:**
- Clases Java compiladas (`.class`)
- Recursos estáticos (HTML, CSS, JS)
- Descriptores de despliegue (web.xml, si existe)
- Dependencias (en WEB-INF/lib, aunque en nuestro caso están "provided")

### Contexto / Ruta base de la aplicación

El **contexto** es la parte de la URL que identifica a la aplicación dentro del servidor.

**¿Cómo se determina?**
- Por defecto: nombre del archivo WAR sin extensión
- `crud-file.war` → contexto `/crud-file`

**Personalización** (archivo `jboss-web.xml`):
```xml
<?xml version="1.0" encoding="UTF-8"?>
<jboss-web>
    <context-root>/api</context-root>
</jboss-web>
```

### Contenedor web: Undertow

**Undertow** es el servidor web embebido en WildFly. Se encarga de:

- Recibir conexiones HTTP/HTTPS
- Gestionar el ciclo de vida de las peticiones
- Enrutar peticiones al contexto correcto
- Servir contenido estático
- Gestionar sesiones HTTP

### Desglose de URL

**URL completa:** `http://localhost:8080/crud-file/api/tasks/1`

| Parte | Valor | Descripción |
|-------|-------|-------------|
| Protocolo | `http` | Protocolo de comunicación |
| Host | `localhost` | Servidor (en producción sería el dominio) |
| Puerto | `8080` | Puerto donde escucha Undertow |
| Contexto | `/crud-file` | Identifica la aplicación WAR |
| Application Path | `/api` | Definido en `@ApplicationPath("/api")` |
| Resource Path | `/tasks` | Definido en `@Path("/tasks")` |
| Path Param | `/1` | ID del recurso específico |

### Evidencias

**Aplicación funcionando en navegador:**

<img width="1954" height="713" alt="image" src="https://github.com/user-attachments/assets/f645a5db-3b82-4f58-bd83-25dcf2e04461" />



**Respuesta del endpoint REST:**

```bash
$ curl http://localhost:8080/crud-file/api/tasks
[{"createdAt":"2026-02-06T22:21:29.946750890Z","done":false,"id":1,"title":"Evidencias para la tarea de despliegue"}]
```

---

## f) Parámetros necesarios para el despliegue

### Parámetros del comando `docker run`

```bash
docker run -d --name wildfly -p 8080:8080 -p 9990:9990 \
  quay.io/wildfly/wildfly:latest \
  /opt/jboss/wildfly/bin/standalone.sh -b 0.0.0.0 -bmanagement 0.0.0.0
```

| Parámetro | Valor | Propósito | ¿Qué pasa si falta/está mal? |
|-----------|-------|-----------|------------------------------|
| `-d` | - | Ejecutar en segundo plano (detached) | El contenedor bloquea la terminal |
| `--name` | `wildfly` | Nombre del contenedor | Se asigna nombre aleatorio, difícil de gestionar |
| `-p 8080:8080` | host:container | Publicar puerto de aplicaciones | No se puede acceder a la app desde el host |
| `-p 9990:9990` | host:container | Publicar puerto de admin | No se puede acceder a la consola |
| `-b 0.0.0.0` | Bind address | WildFly escucha en todas las interfaces | Solo escucha en localhost (inaccesible desde fuera del contenedor) |
| `-bmanagement 0.0.0.0` | Management bind | Consola admin escucha en todas las interfaces | Error "Unable to redirect" al acceder a la consola |

### Parámetros de Gradle (build.gradle)

```groovy
plugins {
    id 'java'
    id 'war'
}
version = '1.0.0'
war {
    archiveFileName = 'crud-file.war'
}
dependencies {
    compileOnly 'jakarta.platform:jakarta.jakartaee-api:10.0.0'
}
```

| Parámetro | Valor | Propósito | ¿Qué pasa si falta/está mal? |
|-----------|-------|-----------|------------------------------|
| `id 'war'` | Plugin WAR | Habilita generación de WAR | Solo genera JAR, no desplegable en WildFly |
| `archiveFileName` | `crud-file.war` | Nombre del WAR (determina contexto) | Nombre largo con versión, URL difícil de recordar |
| `compileOnly` | Jakarta EE API | Dependencia provided | Si fuera `implementation`, conflictos con WildFly |

### Parámetros de despliegue

| Parámetro | Valor | Propósito |
|-----------|-------|-----------|
| Ruta de despliegue | `/opt/jboss/wildfly/standalone/deployments/` | Carpeta hot-deploy de WildFly |
| Nombre del WAR | `crud-file.war` | Determina el contexto `/crud-file` |

**Comando de despliegue:**
```bash
docker cp build\libs\crud-file.war wildfly:/opt/jboss/wildfly/standalone/deployments/
```

### Ejemplo de error por parámetro incorrecto

**Si olvido `-b 0.0.0.0`:**
```bash
docker run -d --name wildfly -p 8080:8080 quay.io/wildfly/wildfly:latest
```

**Resultado:** Al acceder a `http://localhost:8080` → "Connection refused"

**Motivo:** WildFly solo escucha en `127.0.0.1` dentro del contenedor, no en la interfaz de red del contenedor que Docker mapea.

---

## g) Pruebas de funcionamiento y rendimiento

### Pruebas funcionales

#### 1. Interfaz web

**URL:** `http://localhost:8080/crud-file/`

<img width="1957" height="707" alt="image" src="https://github.com/user-attachments/assets/8bcee4d3-96d2-45d3-9a83-382e5b07cbf7" />


Funcionalidades probadas:
- Crear tarea
- Listar tareas
- Cambiar estado (Pendiente/Completada)
- Eliminar tarea

#### 2. Pruebas con curl

**GET - Listar todas las tareas:**
```bash
curl http://localhost:8080/crud-file/api/tasks
```
**Respuesta:**
```json
[{"createdAt":"2026-02-06T22:21:29.946750890Z","done":false,"id":1,"title":"Evidencias para la tarea de despliegue"}]
```

**POST - Crear tarea:**
```bash
curl -X POST http://localhost:8080/crud-file/api/tasks \
  -H "Content-Type: application/json" \
  -d '{"title": "Tarea desde curl", "completed": false}'
```
**Respuesta:**
```json
{"createdAt":"2026-02-06T22:48:56.760514867Z","done":false,"id":2,"title":"Tarea desde curl"}
```

**GET - Obtener tarea por ID:**
```bash
curl http://localhost:8080/crud-file/api/tasks/1
```
**Respuesta:**
```json
{"createdAt":"2026-02-06T22:21:29.946750890Z","done":false,"id":1,"title":"Evidencias para la tarea de despliegue"}
```

**PUT - Actualizar tarea:**
```bash
curl -X PUT http://localhost:8080/crud-file/api/tasks/1 \
  -H "Content-Type: application/json" \
  -d '{"title": "Tarea actualizada", "completed": true}'
```
**Respuesta:**
```json
{"createdAt":"2026-02-06T22:21:29.946750890Z","done":false,"id":1,"title":"Tarea actualizada"}
```

**DELETE - Eliminar tarea:**
```bash
curl -X DELETE http://localhost:8080/crud-file/api/tasks/1
```
**Respuesta:** HTTP 204 No Content (éxito)

<img width="2440" height="298" alt="image" src="https://github.com/user-attachments/assets/21c85825-c1e3-4094-b40b-b90e6e229515" />


### Prueba de rendimiento con hey

**Herramienta:** hey (alternativa moderna a ApacheBench)

**Comando ejecutado:**
```bash
hey.exe -n 1000 -c 10 -H "Accept: application/json" -H "Content-Type: application/json" http://localhost:8080/crud-file/api/tasks
```

**Parámetros:**
- `-n 1000`: 1000 peticiones totales
- `-c 10`: 10 usuarios concurrentes

**Resultados:**

```
Summary:
  Total:        0.2013 secs
  Slowest:      0.0244 secs
  Fastest:      0.0005 secs
  Average:      0.0019 secs
  Requests/sec: 4966.5774

Response time histogram:
  0.001 [1]     |
  0.003 [871]   |■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■
  0.005 [85]    |■■■■
  0.008 [18]    |■
  ...

Latency distribution:
  10% in 0.0007 secs
  25% in 0.0010 secs
  50% in 0.0014 secs
  75% in 0.0021 secs
  90% in 0.0034 secs
  95% in 0.0049 secs
  99% in 0.0104 secs

Status code distribution:
  [200] 1000 responses
```

<img width="1963" height="901" alt="image" src="https://github.com/user-attachments/assets/6ff1693e-3a95-4c96-80f5-e2baf57fb73e" />


### Análisis de resultados

| Métrica | Valor | Interpretación |
|---------|-------|----------------|
| **Requests/sec** | 4966.58 | Excelente throughput (~5000 req/s) |
| **Average latency** | 1.9 ms | Respuestas muy rápidas |
| **99th percentile** | 10.4 ms | Incluso en peor caso, respuesta <11ms |
| **Status codes** | 100% HTTP 200 | Sin errores |

**Conclusión:** El despliegue es estable y de alto rendimiento. WildFly en Docker maneja eficientemente la carga con tiempos de respuesta muy bajos y sin errores.

---

## h) Documentación de administración y recomendaciones

### Mini-guía de administración del despliegue

Esta guía permite a cualquier miembro del equipo levantar, administrar y mantener el despliegue de la aplicación CRUD de Tareas.

---

### 1. Requisitos previos

- Docker Desktop instalado y corriendo
- Git instalado
- Puertos 80 y 443 disponibles (o 8080 para despliegue simple)

---

### 2. Clonar el repositorio

```bash
git clone https://github.com/revilofe/2526_DAW_u5.3_jakarta-wildfly-gradle-otra
cd 2526_DAW_u5.3_jakarta-wildfly-gradle-otra
```

---

### 3. Compilar la aplicación

```bash
# Windows
gradlew.bat clean build

# Linux/Mac
./gradlew clean build
```

**Resultado esperado:** `BUILD SUCCESSFUL`

El archivo WAR se genera en: `build/libs/crud-file.war`

---

### 4. Opción A: Despliegue simple (solo WildFly)

```bash
# Levantar WildFly
docker run -d --name wildfly -p 8080:8080 -p 9990:9990 \
  quay.io/wildfly/wildfly:latest \
  /opt/jboss/wildfly/bin/standalone.sh -b 0.0.0.0 -bmanagement 0.0.0.0

# Crear usuario de administración
docker exec -it wildfly /opt/jboss/wildfly/bin/add-user.sh

# Desplegar el WAR
docker cp build/libs/crud-file.war wildfly:/opt/jboss/wildfly/standalone/deployments/
```

**URLs de acceso:**
- Aplicación: http://localhost:8080/crud-file
- API: http://localhost:8080/crud-file/api/tasks
- Consola admin: http://localhost:9990

---

### 5. Opción B: Despliegue con Docker Compose (Nginx + WildFly)

```bash
cd docker
docker compose up -d
```

**URLs de acceso:**
- Aplicación: http://localhost
- API: http://localhost/api/tasks

---

### 6. Comprobar el estado

```bash
# Ver contenedores activos
docker compose ps

# Ver logs de WildFly
docker logs wildfly-app

# Ver logs de Nginx
docker logs nginx-proxy

# Probar la API
curl http://localhost/api/tasks
```

**Estado esperado:**
- `wildfly-app`: Up (healthy)
- `nginx-proxy`: Up

---

### 7. Desplegar una nueva versión

```bash
# 1. Compilar nueva versión
gradlew.bat clean build

# 2. Reiniciar WildFly para recargar el WAR
docker compose restart wildfly

# 3. Verificar despliegue
docker logs wildfly-app | findstr "Deployed"
```

---

### 8. Parar y eliminar el despliegue

```bash
# Parar contenedores (mantiene datos)
docker compose stop

# Eliminar contenedores y red
docker compose down

# Eliminar también volúmenes (CUIDADO: borra datos)
docker compose down -v
```

---

### 9. Recomendaciones para evitar errores comunes

| Error | Causa | Solución |
|-------|-------|----------|
| "Puerto 8080 en uso" | Otro servicio usa el puerto | `docker stop <contenedor>` o cambiar puerto |
| "Connection refused" | WildFly aún arrancando | Esperar 30-60 segundos |
| "Unable to redirect" en consola | Falta `-bmanagement 0.0.0.0` | Recrear contenedor con el parámetro |
| WAR no se despliega | Ruta incorrecta | Verificar ruta `/opt/jboss/wildfly/standalone/deployments/` |
| "Permission denied" en volúmenes | Permisos de usuario | No montar volúmenes en `/log` o `/data` directamente |
| Nginx "502 Bad Gateway" | WildFly no arrancado | Esperar o verificar logs de WildFly |

---

### 10. Comandos clave de referencia

```bash
# Levantar todo
docker compose up -d

# Ver estado
docker compose ps

# Ver logs en tiempo real
docker compose logs -f

# Reiniciar un servicio
docker compose restart wildfly

# Entrar en un contenedor
docker exec -it wildfly-app bash

# Probar API
curl http://localhost/api/tasks
curl -X POST http://localhost/api/tasks -H "Content-Type: application/json" -d '{"title":"Test"}'
```

---

## i) Virtualización, nube o contenedores en el despliegue

### Docker Compose: Nginx + WildFly + Aplicación REST

Se ha implementado un despliegue completo usando Docker Compose que incluye:

- **Nginx**: Servidor web como reverse proxy
- **WildFly**: Servidor de aplicaciones Jakarta EE
- **Aplicación**: CRUD de Tareas (crud-file.war)

### Estructura de archivos

```
docker/
├── docker-compose.yml
└── nginx/
    ├── nginx.conf
    └── ssl/
        ├── cert.pem
        └── key.pem
```

### docker-compose.yml

```yaml
services:
  # Servidor de aplicaciones WildFly
  wildfly:
    image: quay.io/wildfly/wildfly:latest
    container_name: wildfly-app
    command: /opt/jboss/wildfly/bin/standalone.sh -b 0.0.0.0 -bmanagement 0.0.0.0
    volumes:
      - ../build/libs/crud-file.war:/opt/jboss/wildfly/standalone/deployments/crud-file.war
    networks:
      - app-network
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:8080/crud-file/api/tasks"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 60s
    restart: unless-stopped

  # Servidor web Nginx (reverse proxy)
  nginx:
    image: nginx:alpine
    container_name: nginx-proxy
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./nginx/nginx.conf:/etc/nginx/nginx.conf:ro
      - ./nginx/ssl:/etc/nginx/ssl:ro
    depends_on:
      - wildfly
    networks:
      - app-network
    restart: unless-stopped

networks:
  app-network:
    driver: bridge
```

### nginx.conf

```nginx
events {
    worker_connections 1024;
}

http {
    include /etc/nginx/mime.types;
    default_type application/octet-stream;

    # Logs
    access_log /var/log/nginx/access.log;
    error_log /var/log/nginx/error.log;

    # Upstream hacia WildFly
    upstream wildfly {
        server wildfly:8080;
    }

    # Servidor HTTP
    server {
        listen 80;
        server_name localhost;

        # Health check para Docker
        location /health {
            return 200 'OK';
            add_header Content-Type text/plain;
        }

        # Proxy hacia la aplicación
        location / {
            proxy_pass http://wildfly/crud-file/;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
        }

        # Proxy hacia la API
        location /api/ {
            proxy_pass http://wildfly/crud-file/api/;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
        }
    }
}
```

### Características implementadas

| Característica | Implementación |
|----------------|----------------|
| **Reverse Proxy** | Nginx redirige `/` y `/api/` a WildFly |
| **Red interna** | `app-network` (bridge) para comunicación entre contenedores |
| **WildFly no expuesto** | Puerto 8080 solo accesible dentro de la red Docker |
| **Healthcheck** | Verificación automática del estado de WildFly |
| **Reinicio automático** | `restart: unless-stopped` en ambos servicios |
| **Certificados SSL** | Generados en `nginx/ssl/` (autofirmados para desarrollo) |
| **Volumen para WAR** | El WAR se monta directamente desde `build/libs/` |

### Comandos de gestión

```bash
# Levantar el stack
cd docker
docker compose up -d

# Ver estado
docker compose ps

# Ver logs
docker compose logs -f

# Parar
docker compose down
```

### Evidencias del despliegue

**docker compose ps:**

```
NAME          IMAGE                            SERVICE   STATUS                    PORTS
nginx-proxy   nginx:alpine                     nginx     Up 32 seconds             0.0.0.0:80->80/tcp, 0.0.0.0:443->443/tcp
wildfly-app   quay.io/wildfly/wildfly:latest   wildfly   Up 33 seconds (healthy)   8080/tcp
```

**Acceso a través de Nginx (http://localhost):**

<img width="2551" height="895" alt="image" src="https://github.com/user-attachments/assets/26980db1-1d9a-4c65-86ae-7fd19322c7fd" />


**API a través de Nginx (http://localhost/api/tasks):**

<img width="2559" height="167" alt="image" src="https://github.com/user-attachments/assets/aee4b041-1e6e-4d7d-a933-9b552346fef6" />



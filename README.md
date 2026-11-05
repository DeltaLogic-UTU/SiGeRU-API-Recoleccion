# SiGeRu — API de Recolección

**Sistema de Gestión de Residuos Urbanos | Delta-Logic — 2026**

La API de Recolección forma parte del backend de **SiGeRu**, un sistema web orientado a la administración y el seguimiento de los residuos urbanos.

Este componente está desarrollado con **PHP y Laravel** y se centra en los procesos relacionados con la recolección de residuos, los recorridos de camiones, los contenedores asignados y el registro de las actividades realizadas.

El objetivo es mantener actualizada la información operativa y facilitar su consulta desde las interfaces de Choferes, Cuadrillas y Administración.

La API se integra con [SiGeRu-Website](https://github.com/DeltaLogic-UTU/SiGeRu-Website) y con los demás servicios del proyecto.

## 1. Funcionalidades principales

El módulo de recolección contempla:

- Consulta de recorridos.
- Asignación de rutas a camiones.
- Consulta de rutas despachadas.
- Identificación del chofer asignado.
- Inicio de recolecciones.
- Registro de contenedores recolectados.
- Registro de contenedores que no pudieron recolectarse.
- Generación de incidencias.
- Seguimiento del progreso de los recorridos.
- Actualización de ubicaciones GPS.
- Finalización de recolecciones.
- Actualización de los datos operativos.
- Consulta del historial de recorridos.
- Integración con Gestión y Estadísticas.

Las operaciones trabajan con los datos almacenados en MySQL/MariaDB.

## 2. Tecnologías utilizadas

| Tecnología | Función |
|---|---|
| PHP | Lenguaje del backend |
| Laravel | Framework de desarrollo |
| Composer | Gestión de dependencias |
| MariaDB/MySQL | Base de datos |
| JSON | Intercambio de información |
| API REST | Comunicación entre componentes |
| JWT | Autenticación de solicitudes protegidas |
| Git y GitHub | Control de versiones |
| XAMPP | Entorno local de desarrollo |

La API utiliza consultas a la base de datos, validaciones de Laravel y transacciones para registrar operaciones relacionadas.

## 3. Organización del proyecto

La estructura general sigue la organización de Laravel:

```text
SiGeRu-API-Recoleccion/
├── app/
│   ├── Http/
│   │   └── Controllers/
│   │       └── RecoleccionController.php
│   ├── Models/
│   └── Providers/
│
├── bootstrap/
├── config/
├── database/
│   ├── migrations/
│   └── seeders/
│
├── public/
├── resources/
├── routes/
│   ├── api.php
│   └── web.php
│
├── storage/
├── tests/
├── .env.example
├── .gitignore
├── artisan
├── composer.json
├── composer.lock
└── README.md
```

Los demás controladores y archivos dependen de la versión incluida en el repositorio.

El controlador de recolecciones contiene las operaciones principales para consultar despachos, iniciar recorridos, registrar contenedores y finalizar actividades.

## 4. Gestión de rutas y despachos

SiGeRu permite organizar recorridos de recolección a partir de las rutas y circuitos registrados.

Los despachos pueden incluir:

- Camión asignado.
- Matrícula.
- Chofer.
- Ruta.
- Circuitos.
- Contenedores asignados.
- Geometría del recorrido.
- Distancia planificada.
- Fecha de envío.
- Estado del despacho.

### Validaciones

La lógica de despacho revisada comprueba condiciones como:

- Que el camión exista y esté activo.
- Que tenga un chofer asignado.
- Que no tenga un despacho incompatible.
- Que no exista una recolección activa para el mismo camión.
- Que los circuitos sean válidos.
- Que los contenedores pertenezcan a los circuitos indicados.

Estas comprobaciones buscan evitar asignaciones duplicadas o inconsistentes.

## 5. Espacio del Chofer

El frontend del Chofer presenta la información asociada con su vehículo y recorrido.

Entre los datos consultados se encuentran:

- Información del camión.
- Chofer asignado.
- Ruta despachada.
- Circuitos incluidos.
- Contenedores pendientes.
- Contenedores procesados.
- Recolección activa.
- Información geográfica.

El sistema evita asignar automáticamente un camión cualquiera cuando no existe una relación válida con el usuario.

Si el chofer no tiene un camión asignado, se devuelve una respuesta indicando esa situación.

## 6. Inicio del recorrido

Una vez que existe un despacho válido, el chofer puede iniciar la recolección.

El proceso contempla:

1. Identificar el camión.
2. Comprobar que esté habilitado.
3. Verificar el despacho.
4. Revisar que no exista otra recolección activa.
5. Registrar la fecha de inicio.
6. Crear el registro de recolección.
7. Actualizar el estado del despacho.
8. Actualizar el estado operativo del camión.

El estado de la recolección pasa a:

```text
En curso
```

Si el mismo recorrido ya fue iniciado, el sistema puede recuperar el registro activo en lugar de generar otro duplicado.

## 7. Registro de contenedores

Durante el recorrido se registra el resultado de cada contenedor asignado.

Se contemplan dos acciones principales:

### Contenedor levantado

Cuando se completa la recolección:

- Se identifica el contenedor.
- Se verifica que pertenezca a la ruta.
- Se registra su recolección.
- Se actualiza su última fecha de recolección.
- Se reinicia el contador de días sin recolección.
- Se actualiza el progreso del recorrido.
- Se conserva información en el historial.

### Contenedor no levantado

Cuando no es posible efectuar la recolección:

- Se identifica el contenedor.
- Se registra el motivo.
- Se conserva la observación.
- Se genera una incidencia.
- Se actualiza la información del despacho.

Esto permite que Administración conozca los problemas detectados durante el recorrido.

### Prevención de duplicados

La lógica revisada comprueba si un contenedor ya fue procesado en la recolección activa.

De esta manera se evita duplicar registros cuando se repite accidentalmente una operación.

## 8. Gestión de incidencias

La API contempla la generación de incidencias durante las tareas de recolección.

Una incidencia puede producirse, por ejemplo, cuando:

- Un contenedor está roto.
- No puede levantarse.
- Existe un obstáculo.
- Se presenta otro problema operativo.

Los datos asociados pueden incluir:

- Identificador del contenedor.
- Camión.
- Cuadrilla.
- Tipo de incidencia.
- Descripción.
- Fecha de reporte.
- Estado.

Las incidencias generadas se relacionan con los registros utilizados por la API de Gestión.

## 9. Seguimiento GPS

El sistema contempla el registro de posiciones geográficas de camiones.

Cada actualización de ubicación utiliza:

```text
latitud
longitud
```

Las coordenadas se validan antes de procesarse.

La ubicación puede utilizarse para:

- Mostrar la posición del camión.
- Representar el seguimiento en el mapa.
- Consultar información del recorrido.
- Mantener actualizados los datos geográficos.

La recepción de coordenadas GPS no debe interpretarse como una confirmación automática de recolección.

La disponibilidad del seguimiento depende de los datos transmitidos desde la interfaz del Chofer.

## 10. Finalización de recolecciones

Al finalizar un recorrido, el sistema registra el cierre de la actividad.

Entre las operaciones contempladas se encuentran:

- Verificar la recolección activa.
- Registrar la fecha de finalización.
- Actualizar su estado.
- Consolidar los contenedores procesados.
- Actualizar información del camión.
- Cerrar las asignaciones correspondientes.
- Registrar los datos necesarios para el historial.

Las recolecciones finalizadas pueden consultarse posteriormente desde los módulos de Gestión y Chofer.

## 11. Historial de recorridos

La API dispone de lógica para consultar las recolecciones anteriores.

Los datos históricos permiten conocer:

- Camión utilizado.
- Chofer relacionado.
- Ruta.
- Circuito.
- Fecha de inicio.
- Fecha de finalización.
- Estado.
- Contenedores recolectados.
- Contenedores asignados.
- Distancia registrada.
- Información operativa disponible.

El historial permite consultar las actividades realizadas sin perder los registros de recorridos anteriores.

## 12. Integración con otras APIs

SiGeRu está dividido en servicios que comparten información operativa.

### API de Usuarios

Se encarga de la autenticación, identificación y perfiles.

Dirección local:

```text
http://127.0.0.1:8000
```

### API de Gestión

Administra recursos y operaciones como:

- Camiones.
- Contenedores.
- Cuadrillas.
- Circuitos.
- Rutas.
- Reclamos.
- Incidencias.
- Estadísticas.

Dirección local:

```text
http://127.0.0.1:8001
```

### API de Recolección

Corresponde al componente dedicado a las operaciones de recolección.

Dirección local prevista:

```text
http://127.0.0.1:8002
```

**Nota de integración:** durante el desarrollo, parte de las operaciones de choferes y recolecciones también se incorporaron a la API de Gestión en el puerto `8001`.

Por tanto, la ubicación definitiva de cada endpoint debe comprobarse en los archivos de rutas de ambas APIs. No debe suponerse que una operación está publicada en `8002` únicamente porque su función corresponde al módulo de recolección.

## 13. Base de datos

La API utiliza la base de datos compartida:

```text
sigeru
```

La estructura se encuentra en:

[SiGeRu-DumpSQL](https://github.com/DeltaLogic-UTU/SiGeRu-DumpSQL)

### Tablas relacionadas

La lógica revisada utiliza información de tablas como:

| Tabla | Función |
|---|---|
| `camiones` | Información y estado de vehículos |
| `rutas` | Recorridos definidos |
| `circuitos` | Circuitos operativos |
| `contenedores` | Contenedores registrados |
| `ruta_despachos` | Despachos y progreso |
| `recolecciones` | Recolecciones realizadas |
| `recoleccion_contenedor` | Contenedores procesados |
| `camion_asignaciones` | Historial de asignaciones |
| `incidencias` | Problemas registrados |
| `historial_contenedores` | Cambios y operaciones sobre contenedores |

La estructura debe mantenerse compatible con las demás APIs.

## 14. Configuración del entorno

La API utiliza variables de entorno almacenadas en un archivo `.env`.

### Crear el archivo

En PowerShell:

```powershell
Copy-Item .env.example .env
```

### Configuración local

Ejemplo:

```dotenv
APP_NAME="SiGeRu"
APP_ENV=local
APP_DEBUG=true
APP_URL=http://127.0.0.1:8002

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=sigeru
DB_USERNAME=root
DB_PASSWORD=

SESSION_DRIVER=file
```

Estos valores corresponden al entorno de desarrollo con XAMPP y deben adaptarse a la instalación.

### Clave de Laravel

```powershell
php artisan key:generate
```

La clave se genera en el `.env` local y no debe publicarse en GitHub.

### Autenticación JWT

Si la API utiliza el mismo sistema de autenticación JWT de las demás APIs, deben configurarse las variables de firma y validación correspondientes.

Las claves deben ser compatibles con el método de autenticación que utilice la instalación.

## 15. Instalación local

### Requisitos

- PHP compatible con Laravel.
- Composer.
- MySQL/MariaDB.
- XAMPP o entorno equivalente.
- Git.

### Paso 1. Clonar el repositorio

```powershell
git clone https://github.com/DeltaLogic-UTU/SiGeRu-API-Recoleccion.git

cd SiGeRu-API-Recoleccion
```

### Paso 2. Instalar dependencias

```powershell
composer install
```

### Paso 3. Preparar el entorno

```powershell
Copy-Item .env.example .env

php artisan key:generate
```

Configurar la conexión a MySQL y las variables adicionales que necesite la aplicación.

### Paso 4. Preparar la base de datos

Iniciar MySQL desde XAMPP.

Crear la base si todavía no existe:

```sql
CREATE DATABASE IF NOT EXISTS sigeru
CHARACTER SET utf8mb4
COLLATE utf8mb4_unicode_ci;
```

Importar la estructura correspondiente desde `SiGeRu-DumpSQL`.

No reemplazar una base existente sin realizar previamente una copia de seguridad.

### Paso 5. Ejecutar Laravel

```powershell
php artisan serve --host=127.0.0.1 --port=8002
```

La API quedará disponible en:

```text
http://127.0.0.1:8002
```

## 16. Rutas de la API

Los endpoints deben comprobarse en los archivos de rutas del repositorio:

```text
routes/api.php
routes/web.php
```

Para consultar las rutas disponibles:

```powershell
php artisan route:list
```

Para consultar específicamente las rutas de API:

```powershell
php artisan route:list --path=api
```

El resultado permite identificar:

- Método HTTP.
- URL.
- Controlador.
- Middleware.

La existencia de métodos en `RecoleccionController.php` no garantiza que todos estén publicados como endpoints en esta API.

## 17. Integración con el frontend

La comunicación con las APIs se realiza desde JavaScript mediante `fetch`.

Ejemplo de configuración para la API de Recolección:

```javascript
const API_RECOLECCION = 'http://127.0.0.1:8002';
```

Las rutas realmente utilizadas deben coincidir con la configuración del frontend y de cada backend.

Las solicitudes protegidas mediante JWT incorporan:

```http
Authorization: Bearer TU_TOKEN_JWT
Accept: application/json
```

Las respuestas JSON permiten actualizar la información de los recorridos, el mapa y los contenedores.

## 18. Pruebas y validaciones

Entre los procesos que deben comprobarse se encuentran:

1. Conexión con MySQL.
2. Autenticación de las solicitudes.
3. Consulta de camiones.
4. Consulta de rutas asignadas.
5. Validación de despachos.
6. Inicio de recorridos.
7. Registro de contenedores recolectados.
8. Registro de incidencias.
9. Prevención de registros duplicados.
10. Actualización de coordenadas GPS.
11. Finalización de recorridos.
12. Consulta del historial.
13. Sincronización con la API de Gestión.
14. Actualización de los indicadores operativos.

Las pruebas deben verificar también los errores y las restricciones de acceso.

## 19. Seguridad y mantenimiento

Para mantener el sistema se recomienda:

- Validar los datos de entrada.
- Comprobar autenticación y autorización.
- Verificar que cada chofer pueda operar solamente sus recursos autorizados.
- Evitar despachos duplicados.
- Mantener transacciones para operaciones relacionadas.
- No publicar claves JWT.
- No subir archivos `.env`.
- Desactivar `APP_DEBUG` en producción.
- Proteger los datos de ubicación.
- Realizar respaldos periódicos.
- Mantener actualizadas las dependencias.

El proyecto requiere pruebas de seguridad e integración antes de utilizarse en producción.

## 20. Repositorios de SiGeRu

| Repositorio | Descripción |
|---|---|
| [SiGeRu-Website](https://github.com/DeltaLogic-UTU/SiGeRu-Website) | Frontend del sistema |
| [SiGeRu-API-Usuarios](https://github.com/DeltaLogic-UTU/SiGeRu-API-Usuarios) | Autenticación y usuarios |
| [SiGeRu-API-Gestion](https://github.com/DeltaLogic-UTU/SiGeRu-API-Gestion) | Gestión operativa |
| [SiGeRu-API-Recoleccion](https://github.com/DeltaLogic-UTU/SiGeRu-API-Recoleccion) | Recolecciones y recorridos |
| [SiGeRu-DumpSQL](https://github.com/DeltaLogic-UTU/SiGeRu-DumpSQL) | Base de datos |

---

## Proyecto académico 2026

**SiGeRu — Sistema de Gestión de Residuos Urbanos**

**Delta-Logic**

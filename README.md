# Sistema de Gestión Odontológica ULEAM

Sistema académico para apoyar la formación clínica de estudiantes de odontología y la supervisión de casos por docentes. El proyecto incluye un frontend web, una API REST y una base de datos PostgreSQL administrada con Prisma.

El repositorio está orientado a un entorno académico controlado. No debe utilizarse con pacientes reales hasta completar las medidas clínicas, operativas y de seguridad pendientes.

## Contenido

- [Objetivo](#objetivo)
- [Arquitectura](#arquitectura)
- [Roles de usuario](#roles-de-usuario)
- [Módulos principales](#módulos-principales)
- [Tecnologías](#tecnologías)
- [Estructura del repositorio](#estructura-del-repositorio)
- [Requisitos previos](#requisitos-previos)
- [Configuración inicial](#configuración-inicial)
- [Ejecutar el proyecto](#ejecutar-el-proyecto)
- [Pruebas](#pruebas)
- [Datos de demostración](#datos-de-demostración)
- [Documentación](#documentación)
- [Estado actual](#estado-actual)
- [Seguridad y producción](#seguridad-y-producción)
- [Flujo recomendado de desarrollo](#flujo-recomendado-de-desarrollo)

## Objetivo

El sistema busca cubrir el siguiente recorrido académico:

1. El administrador gestiona usuarios y catálogos.
2. El estudiante registra pacientes y crea casos clínicos.
3. El profesor asignado revisa los casos.
4. Los actores autorizados registran odontograma, tratamientos y prescripciones.
5. Se gestionan citas, observaciones y archivos dentro del contexto correspondiente.
6. Los resultados pueden consultarse desde el historial clínico visible para cada rol.

## Arquitectura

```mermaid
flowchart LR
    Navegador["Navegador"] --> Frontend["Frontend Vue 3"]
    Frontend --> API["API NestJS"]
    API --> Prisma["Prisma ORM"]
    Prisma --> PostgreSQL["PostgreSQL"]
    API --> Archivos["Almacenamiento local de archivos"]
```

- **Frontend:** aplicación Vue 3 con TypeScript, Pinia y Vue Router.
- **Backend:** API REST con NestJS, Passport/JWT, validación de DTO y Prisma.
- **Persistencia:** PostgreSQL.
- **Archivos:** almacenamiento local dentro del servidor backend.
- **Documentación funcional:** análisis separado en `documentacion/analisis_sistema`.

## Roles de usuario

| Rol | Responsabilidad principal |
| --- | --- |
| Administrador | Gestiona usuarios, especialidades, catálogos y configuración administrativa. |
| Profesor | Revisa casos asignados, supervisa estudiantes y registra observaciones clínicas. |
| Estudiante | Registra pacientes, crea casos y agrega registros clínicos permitidos. |

Secretario aparece como rol en el modelo y requiere una definición funcional más completa antes de integrarlo plenamente.

## Módulos principales

### Gestión de identidad

- Usuarios y perfiles.
- Roles y permisos.
- Especialidades.
- Datos geográficos de Ecuador.

### Gestión clínica

- Pacientes e historial.
- Casos clínicos.
- Estados de casos y observaciones.
- Odontograma 2D/3D.
- Tratamientos y prescripciones.
- Citas y agenda.
- Encuestas y preguntas clínicas.
- CIE-10 y procedimientos.
- Archivos asociados a casos y entidades.

### Módulos existentes con alcance parcial

Los módulos pueden tener interfaz, API o persistencia parcial. La clasificación detallada se encuentra en [Estado de módulos](documentacion/analisis_sistema/07_estado_de_modulos.md).

## Tecnologías

### Frontend

- Vue 3
- TypeScript
- Pinia
- Vue Router
- Axios
- Vite
- Three.js para visualización odontológica

### Backend

- NestJS 11
- Passport y JWT
- Prisma
- PostgreSQL
- class-validator y class-transformer
- bcrypt
- Multer
- ExcelJS
- Helmet y Nest Throttler

## Estructura del repositorio

```text
Practicas-Odontologia/
├── backendv2/                 API NestJS y base de datos Prisma
│   ├── prisma/                Esquema y migraciones
│   ├── scripts/               Seeds, bootstrap y datos demo
│   ├── src/                   Controladores, servicios, guards y módulos
│   ├── test/                  Pruebas HTTP/E2E
│   └── uploads/               Archivos locales de ejecución
├── Frontendv2/                Aplicación web Vue
│   └── src/
│       ├── assets/            Estilos e imágenes
│       ├── components/        Componentes reutilizables
│       ├── composables/       Lógica reutilizable
│       ├── Config/            API y configuración
│       ├── router/            Rutas y guards
│       ├── services/          Servicios HTTP
│       ├── stores/            Estado Pinia
│       ├── types/             Tipos compartidos
│       └── views/             Vistas por rol
├── documentacion/             Análisis funcional y técnico
├── Resources/                 Recursos, referencias e investigación
├── referencias-odontograma3d/  Referencias independientes del visor 3D
└── README.md                  Esta guía general
```

## Requisitos previos

- Node.js 20 LTS recomendado.
- npm.
- PostgreSQL.
- Git.
- Navegador moderno.

Para ejecutar pruebas y migraciones también se necesita acceso a una base PostgreSQL local o desechable.

## Configuración inicial

### 1. Clonar el repositorio

```powershell
git clone https://github.com/melanieperez26/Practicas-Odontologia.git
cd Practicas-Odontologia
```

### 2. Configurar PostgreSQL

Crear una base de datos y una cuenta PostgreSQL específicas para desarrollo.

### 3. Configurar el backend

Copiar el ejemplo de variables:

```powershell
Copy-Item backendv2\.env.example backendv2\.env
```

Editar `backendv2/.env` y reemplazar los valores de ejemplo.

Variables principales:

```env
DATABASE_URL="postgresql://usuario:password@localhost:5432/nombre_base"
JWT_SECRET="secreto_largo_de_al_menos_32_caracteres"
PORT=3000
CORS_ORIGINS="http://localhost:5173"
```

No registrar secretos reales en Git. `.env` debe permanecer excluido por `.gitignore`.

### 4. Configurar el frontend

Crear `Frontendv2/.env` si hace falta:

```env
VITE_API_URL=http://localhost:3000
```

## Ejecutar el proyecto

### Backend

```powershell
cd backendv2
npm install
npx prisma generate
npx prisma migrate deploy
npm run start:dev
```

El backend usa por defecto el puerto `3000`.

### Frontend

En otra terminal:

```powershell
cd Frontendv2
npm install
npm run dev
```

El frontend usa por defecto Vite en `http://localhost:5173`.

### Preparar el primer administrador

Después de inicializar el backend y cargar los roles base, pueden configurarse las variables `BOOTSTRAP_ADMIN_*` y ejecutar:

```powershell
cd backendv2
npm run bootstrap:admin
```

El script evita crear nuevos administradores si ya existe uno.

### Preparar datos demo

```powershell
cd backendv2
$env:DEMO_PASSWORD="ClaveDemoTemporalSegura"
npm run demo:data
```

Las cuentas demo son solo datos sintéticos locales. No usar contraseñas conocidas con datos reales.

## Pruebas

### Backend

```powershell
cd backendv2
npm run build
npm test -- --runInBand
npm run test:e2e -- --runInBand
npm audit --omit=dev
```

Las pruebas incluyen:

- Servicios unitarios.
- Guards y roles.
- Autorización HTTP aislada.
- Casos clínicos y acceso cruzado.
- Usuarios y pacientes.
- Citas y odontograma.
- Conexión real con PostgreSQL durante las suites E2E configuradas.

### Frontend

```powershell
cd Frontendv2
npm install
npm run build
npm run lint
```

Los resultados actuales deben volver a verificarse después de cambios en dependencias o reglas de autorización.

## Datos de demostración

El script `backendv2/scripts/demo-data.ts` crea o actualiza cuentas sintéticas:

- `demo.admin@odontologia.local`
- `demo.profesor@odontologia.local`
- `demo.estudiante@odontologia.local`

La contraseña se recibe mediante `DEMO_PASSWORD` y no se registra en el repositorio.

## Documentación

### Documentación general

- [Descripción general](documentacion/analisis_sistema/01_descripcion_general.md)
- [Requisitos funcionales](documentacion/analisis_sistema/02_requisitos_funcionales.md)
- [Requisitos no funcionales](documentacion/analisis_sistema/03_requisitos_no_funcionales.md)
- [Reglas de negocio](documentacion/analisis_sistema/04_reglas_de_negocio.md)
- [Flujos de trabajo](documentacion/analisis_sistema/05_flujos_de_trabajo.md)
- [Matriz de permisos](documentacion/analisis_sistema/06_matriz_permisos.md)
- [Estado de módulos](documentacion/analisis_sistema/07_estado_de_modulos.md)
- [Pendientes y mejoras](documentacion/analisis_sistema/08_pendientes_y_mejoras.md)
- [Plan de trabajo](documentacion/analisis_sistema/09_plan_de_trabajo.md)
- [Pruebas y calidad](documentacion/analisis_sistema/10_pruebas_y_calidad.md)

### Documentación por componente

- [Guía del backend](backendv2/README.md)
- [Política de seguridad](backendv2/SECURITY.md)
- [Registro de entrega backend](backendv2/ENTREGA.md)
- [Guía del frontend](Frontendv2/README.md)

## Estado actual

El proyecto cuenta con una base funcional integrada de autenticación, usuarios, pacientes, casos clínicos, odontograma, tratamientos, prescripciones, citas y archivos.

Estado resumido:

- **Implementado:** autenticación básica y varias operaciones por rol.
- **Parcial:** pacientes, casos, permisos, citas, odontograma, tratamientos, prescripciones y archivos.
- **No implementado:** disponibilidad clínica, hallazgos independientes, solicitudes, auditoría persistente y módulos completos de clínicas/horarios.
- **Pendiente:** recurrir a validaciones reales de concurrencia, rollback, auditoría, recuperación y operación clínica.

## Seguridad y producción

Antes de desplegar se debe verificar:

- Rotación de `JWT_SECRET` y contraseña PostgreSQL.
- HTTPS y secretos distintos por entorno.
- CORS restringido a dominios reales.
- Protección de rutas por propiedad y permiso.
- Recuperación de contraseña, revocación y expiración centralizada.
- Validación de archivos por firma y política de almacenamiento.
- Backups cifrados y restauración comprobada.
- Registro de auditoría persistente.
- Consentimiento informado y política de tratamiento de datos clínicos.
- Eliminación del acceso público a catálogos y mutaciones no administrativas.
- Resolución de las vulnerabilidades de runtime reportadas por npm.

El proyecto es válido como prototipo académico controlado, no como sistema clínico certificado.

## Flujo recomendado de desarrollo

1. Crear una rama desde `main`.
2. Implementar un cambio acotado.
3. Añadir o actualizar pruebas.
4. Ejecutar build y pruebas.
5. Revisar el diff y archivos sensibles.
6. Crear un Pull Request.
7. Esperar revisión técnica.
8. Integrar solo después de resolver conflictos y resultados fallidos.

Ejemplo:

```powershell
git switch main
git pull origin main
git switch -c feat/nueva-funcionalidad
```

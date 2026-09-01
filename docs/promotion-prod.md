# Promoción a producción

## Precondiciones

Antes de promover:

- La entrega está integrada en `develop`.
- QA está aprobado.
- Existe una rama `release/*`.
- UAT tiene aprobación funcional documentada.
- Se conoce el número de versión y el responsable de la ventana.
- Existe respaldo o procedimiento de recuperación aplicable.
- Se comunicó la ventana de cambio y el plan de validación.

## Procedimiento

### 1. Preparar la release

1. Crear `release/YYYY.MM.patch` desde `develop`.
2. Actualizar versión, notas de release y cualquier configuración necesaria.
3. Abrir PR de `release/*` hacia `main`.
4. Obtener las aprobaciones requeridas.

### 2. Aprobar y etiquetar

1. Completar el PR hacia `main`.
2. Crear un tag anotado, por ejemplo `v2026.09.0`.
3. Registrar en las notas:
   - funcionalidades incluidas;
   - correcciones;
   - cambios de configuración;
   - migraciones o tareas manuales;
   - riesgos conocidos;
   - plan de rollback.

### 3. Desplegar manualmente

#### Power Platform

- Importar la solución administrada en PROD.
- Verificar variables de entorno y referencias de conexión.
- Activar flujos y procesos que correspondan.
- Validar usuarios, seguridad y conexiones.
- Ejecutar smoke tests funcionales.

#### Finance & Operations

- Construir el deployable package aprobado.
- Seguir el procedimiento de despliegue autorizado del entorno.
- Ejecutar las tareas de base de datos o sincronización requeridas.
- Validar batch jobs, integraciones y seguridad.
- Ejecutar smoke tests técnicos y funcionales.

### 4. Cerrar la release

1. Registrar resultado, hora y responsable.
2. Documentar incidencias y desviaciones.
3. Abrir PR de `main` hacia `develop` para sincronizar cambios.
4. Cerrar la rama `release/*` cuando no sea necesaria.

## Rollback mínimo

Si falla la validación:

1. Detener la promoción y comunicar el incidente.
2. Identificar si el rollback es por código, solución, paquete o configuración.
3. Restaurar la última versión aprobada siguiendo el procedimiento de la plataforma.
4. Deshabilitar temporalmente componentes problemáticos si es seguro hacerlo.
5. Registrar el incidente y abrir un bugfix/hotfix.
6. No borrar evidencia ni sobrescribir el tag de la versión fallida.

## Criterios de éxito

- La versión esperada está instalada.
- Smoke tests completados.
- Integraciones críticas operativas.
- Sin errores críticos en monitoreo inicial.
- El dueño funcional confirma la operación.

# Procedimiento de Pull Requests

## Antes de abrir el PR

El autor debe:

- Confirmar que la rama parte de la base correcta.
- Mantener el cambio enfocado en una sola finalidad.
- Actualizar documentación cuando cambie el procedimiento o configuración.
- Ejecutar las pruebas disponibles.
- Adjuntar evidencia de pruebas: capturas, resultados o enlaces.
- Verificar que no existan secretos, contraseñas, tokens ni datos reales.
- Indicar cualquier paso manual requerido en el ambiente destino.

## Flujo

1. Crear la rama temporal.
2. Hacer commits pequeños y descriptivos.
3. Abrir el PR hacia `develop`, salvo `release/*` y `hotfix/*`.
4. Asignar al menos un revisor técnico.
5. Para cambios funcionales, solicitar validación del dueño del proceso.
6. Resolver comentarios y corregir los checks.
7. Obtener aprobación.
8. Completar con **Squash and merge**.
9. Eliminar la rama temporal.

## Criterios mínimos de aprobación

- El objetivo y el alcance están claros.
- El cambio fue probado en DEV.
- No rompe funcionalidades existentes conocidas.
- La solución está exportada o versionada de forma consistente.
- Los pasos de despliegue y configuración están documentados.
- Existe un plan de reversión si el riesgo es medio o alto.

## Revisión por tipo de cambio

- **Power Platform:** revisar solución, componentes incluidos, dependencias, variables de entorno y conexiones.
- **Finance & Operations:** revisar paquetes/modelos, dependencias, base de datos, compilación y pasos de sincronización.
- **Seguridad:** revisar permisos, roles, conexiones y exposición de datos.

## Título recomendado

`[PP] Descripción breve` o `[F&O] Descripción breve`

Ejemplos:

- `[PP] Add approval flow for purchase requests`
- `[F&O] Fix validation in vendor invoice posting`

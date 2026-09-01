# Reglas específicas por plataforma

## Power Platform

- Trabajar con **Solutions**; evitar cambios aislados no exportados.
- Usar soluciones no administradas en DEV.
- Promover soluciones administradas hacia QA, UAT y PROD.
- Mantener separadas las configuraciones mediante variables de entorno.
- Usar referencias de conexión en lugar de valores embebidos.
- No incluir datos de negocio reales en archivos del repositorio.
- Documentar dependencias entre soluciones y conectores.
- Mantener un responsable de la solución y un dueño funcional.

## Finance & Operations

- Versionar el código X++ y artefactos permitidos por la estrategia del proyecto.
- Validar compilación, sincronización y dependencias antes de promover.
- Generar el paquete desplegable desde el commit/tag aprobado.
- Documentar cambios de base de datos, labels, security artifacts e integraciones.
- Coordinar despliegues con los responsables de batch jobs e interfaces.
- No almacenar secretos ni configuraciones específicas de ambiente en el código.

## Configuración por ambiente

La configuración específica de DEV, QA, UAT y PROD debe mantenerse fuera del código cuando sea posible. Debe existir un registro seguro y controlado de:

- URLs y endpoints;
- referencias de conexión;
- variables de entorno;
- usuarios técnicos;
- parámetros de batch;
- permisos y roles;
- tareas manuales de instalación.

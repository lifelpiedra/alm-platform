# ALM MVP — Power Platform y Finance & Operations

Guía mínima viable para gestionar cambios desde desarrollo hasta producción en GitHub o Azure DevOps.

## Alcance

- Provisionamiento de ambientes **manual**.
- Control de código fuente y revisión por Pull Requests.
- Separación de cambios por tipo de solución.
- Promoción controlada hacia producción.
- Automatización futura, fuera del alcance inicial.

## Ambientes

`DEV → QA → UAT → PROD`

| Ambiente | Propósito | Despliegue |
|---|---|---|
| DEV | Desarrollo e integración | Manual |
| QA | Pruebas técnicas e integración | Manual, desde `develop` |
| UAT | Validación funcional del negocio | Manual, desde `release/*` |
| PROD | Operación productiva | Manual, desde `main` |

## Flujo resumido

1. Crear una rama `feature/*` o `bugfix/*` desde `develop`.
2. Implementar y probar en DEV.
3. Abrir Pull Request hacia `develop`.
4. Tras la aprobación, integrar y promover a QA.
5. Para una entrega, crear `release/*` desde `develop`.
6. Validar en UAT.
7. Abrir Pull Request de `release/*` hacia `main`.
8. Crear un tag de versión y promover manualmente a PROD.
9. Sincronizar `main` de vuelta a `develop`.

## Estructura

- `docs/branching.md`: estrategia de ramas.
- `docs/pull-requests.md`: procedimiento de Pull Requests.
- `docs/promotion-prod.md`: promoción a producción y rollback.
- `docs/platform-specific.md`: reglas mínimas por plataforma.
- `.github/pull_request_template.md`: plantilla de PR.

## Principios del MVP

- `main` debe representar lo que está aprobado para producción.
- No se hacen cambios directos en `main` ni `develop`.
- Todo cambio debe tener Pull Request y evidencia de pruebas.
- Las credenciales, secretos y datos sensibles nunca se guardan en Git.
- La versión desplegada debe poder identificarse mediante un tag.

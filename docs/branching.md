# Estrategia de branching

## Ramas permanentes

### `main`

Representa el estado aprobado para producción. Solo recibe cambios mediante Pull Request desde una rama `release/*` o `hotfix/*`.

### `develop`

Rama de integración. Contiene el trabajo aprobado para la siguiente entrega y es la fuente para QA.

## Ramas temporales

| Tipo | Se crea desde | Se integra a | Ejemplo |
|---|---|---|---|
| `feature/*` | `develop` | `develop` | `feature/customer-approval |
| `bugfix/*` | `develop` | `develop` | `bugfix/fix-invoice-validation` |
| `release/*` | `develop` | `main` y `develop` | `release/2026.09.0` |
| `hotfix/*` | `main` | `main` y `develop` | `hotfix/2026.09.1` |

Usar nombres cortos, descriptivos y en minúsculas. No incluir credenciales, nombres de clientes o información sensible.

## Reglas

1. Proteger `main` y `develop`.
2. Prohibir pushes directos a ambas ramas.
3. Requerir al menos una aprobación para cambios normales.
4. Requerir dos aprobaciones para producción cuando el equipo lo permita.
5. Resolver conversaciones y checks antes de completar el PR.
6. Preferir **Squash and merge** para mantener un historial limpio.
7. Eliminar ramas temporales después del merge.
8. Etiquetar las versiones productivas con el formato `vYYYY.MM.patch`, por ejemplo `v2026.09.0`.

## Hotfix

1. Crear `hotfix/*` desde el tag o commit actualmente productivo de `main`.
2. Corregir y probar con prioridad.
3. Abrir PR hacia `main`.
4. Tras desplegar, abrir PR de `main` hacia `develop` para conservar la corrección.

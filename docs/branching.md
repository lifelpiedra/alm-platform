# Estrategia de branching

## Objetivo

El branching organiza el trabajo y evita que cambios incompletos lleguen directamente a producción. Para este MVP usaremos dos ramas permanentes y ramas temporales para cada cambio o entrega.

## Árbol general de ramas

```text
                              ┌──────────────────────────────┐
                              │            main              │
                              │  Código aprobado en PROD    │
                              └──────────────┬───────────────┘
                                             │
                         hotfix/* ──────────┘
                         │                   │
                         └──── PR ──────────┴──> main
                                             │
                                             └── PR de sincronización ──> develop

 develop
    │
    ├── feature/* ── PR ──> develop
    │
    ├── bugfix/*  ── PR ──> develop
    │
    └── release/* ── PR ──> main ──> PROD
                         │
                         └── PR de sincronización ──> develop
```

La secuencia normal es:

```text
feature/* o bugfix/*
        │
        └── Pull Request
                │
                ▼
             develop
                │
                └── Crear release/*
                        │
                        └── Pull Request
                                │
                                ▼
                              main
                                │
                                └── Tag + despliegue manual
                                      │
                                      ▼
                                     PROD
```

## Ramas permanentes

### `main`

Representa el estado aprobado para producción. Solo recibe cambios mediante Pull Request desde una rama `release/*` o `hotfix/*`.

Cada cambio integrado en `main` debe poder relacionarse con una versión desplegable. Después de completar una release se crea un tag, por ejemplo `v2026.09.0`.

### `develop`

Rama de integración. Contiene el trabajo aprobado para la siguiente entrega y es la fuente para QA.

Los cambios de `feature/*` y `bugfix/*` llegan primero a `develop`. La rama debe mantenerse en un estado razonablemente estable; no se debe usar como área de trabajo personal.

## Ramas temporales

| Tipo | Se crea desde | Se integra a | Propósito | Ejemplo |
|---|---|---|---|---|
| `feature/*` | `develop` | `develop` | Nueva funcionalidad | `feature/customer-approval` |
| `bugfix/*` | `develop` | `develop` | Corrección encontrada antes de producción | `bugfix/fix-invoice-validation` |
| `release/*` | `develop` | `main` y después `develop` | Preparar una entrega | `release/2026.09.0` |
| `hotfix/*` | `main` | `main` y después `develop` | Corrección urgente en producción | `hotfix/2026.09.1` |

Usar nombres cortos, descriptivos y en minúsculas. No incluir credenciales, nombres de clientes o información sensible.

## Cómo crear las ramas inicialmente

Como el repositorio migrado no creó automáticamente las ramas, deben crearse una sola vez. La rama `main` ya existe. Crear `develop` a partir de `main`:

```bash
git clone https://dev.azure.com/{organization}/{project}/_git/{repo}
cd {repo}

git fetch origin

git switch main
git pull origin main

git switch -c develop
git push -u origin develop
```

A partir de ese momento, las ramas temporales se crean desde la rama indicada en la tabla anterior.

## Reglas

1. Proteger `main` y `develop`.
2. Prohibir pushes directos a ambas ramas.
3. Requerir al menos una aprobación para cambios normales.
4. Requerir dos aprobaciones para producción cuando el equipo lo permita.
5. Resolver conversaciones y checks antes de completar el PR.
6. Preferir **Squash and merge** para mantener un historial limpio.
7. Eliminar ramas temporales después del merge.
8. Etiquetar las versiones productivas con el formato `vYYYY.MM.patch`, por ejemplo `v2026.09.0`.
9. No reutilizar una rama temporal después de completar su PR.
10. Toda rama de trabajo debe tener un objetivo identificable, idealmente asociado a un work item de Azure Boards.

## Flujo normal: nueva funcionalidad (`feature/*`)

Una funcionalidad nueva sigue este recorrido:

```text
1. develop
      │
      └── crear feature/customer-approval
              │
2.          desarrollar y probar en DEV
              │
3.          PR: feature/customer-approval → develop
              │
4.          revisión y aprobación
              │
5.          merge a develop
              │
6.          validar en QA
              │
7.          crear release/2026.09.0 desde develop
              │
8.          validar en UAT
              │
9.          PR: release/2026.09.0 → main
              │
10.         merge a main, crear tag v2026.09.0
              │
11.         promoción manual a PROD
              │
12.         PR: main → develop
```

### Ejemplo práctico de una feature

Supongamos que se necesita agregar una aprobación para solicitudes de compra en Power Platform.

```bash
# Partir de develop actualizado
git switch develop
git pull origin develop

# Crear la rama de trabajo
git switch -c feature/customer-approval

# Realizar cambios, exportar/versionar la solución y probar en DEV
# ... trabajo local ...

git add .
git commit -m "Add approval flow for purchase requests"
git push -u origin feature/customer-approval
```

Después:

1. Crear un PR de `feature/customer-approval` hacia `develop`.
2. Adjuntar evidencia de pruebas en DEV.
3. Obtener la aprobación del revisor.
4. Completar el PR.
5. Desplegar o importar manualmente a QA.
6. Cuando el conjunto de cambios esté listo, crear `release/2026.09.0` desde `develop`.
7. Validar en UAT y promover la release a `main`.

## Flujo de corrección previa a producción (`bugfix/*`)

Un `bugfix/*` se usa para un problema detectado en DEV, QA o UAT que todavía no está en producción.

```text
develop
   │
   └── bugfix/fix-invoice-validation
           │
           └── corregir y probar
                   │
                   └── PR → develop
                           │
                           └── incluir en la próxima release
                                   │
                                   └── release/* → main → PROD
```

### Ejemplo práctico de un bugfix

Se detecta en QA que la validación de una factura de proveedor permite un valor incorrecto:

```bash
git switch develop
git pull origin develop
git switch -c bugfix/fix-invoice-validation

# Corregir el problema y probarlo en DEV

git add .
git commit -m "Fix vendor invoice validation"
git push -u origin bugfix/fix-invoice-validation
```

Luego:

1. Crear el PR hacia `develop`.
2. Indicar el defecto corregido y adjuntar la evidencia de la prueba.
3. Completar el PR después de la revisión.
4. Validar la corrección en QA.
5. Incluir el bugfix en la siguiente `release/*`.
6. Promover la release a UAT y posteriormente a `main` y PROD.

## Preparación de una release (`release/*`)

Una `release/*` congela el alcance de una entrega. Después de crearla, solo deben entrar correcciones necesarias para esa entrega; las nuevas funcionalidades deben esperar a otra release.

```bash
git switch develop
git pull origin develop
git switch -c release/2026.09.0
git push -u origin release/2026.09.0
```

Flujo de la release:

1. Crear `release/2026.09.0` desde `develop`.
2. Promover manualmente la release a UAT.
3. Ejecutar pruebas funcionales y de regresión.
4. Corregir problemas directamente en `release/2026.09.0` solo si son necesarios para la entrega.
5. Abrir PR de `release/2026.09.0` hacia `main`.
6. Completar el PR después de la aprobación.
7. Crear el tag `v2026.09.0` sobre `main`.
8. Promover manualmente a PROD.
9. Abrir un PR de `main` hacia `develop` para sincronizar cualquier cambio realizado durante la release.

## Flujo urgente de producción (`hotfix/*`)

Un `hotfix/*` se usa únicamente cuando el problema ya está en producción y no puede esperar a la siguiente release.

```text
main / PROD
      │
      └── hotfix/2026.09.1
              │
              ├── corregir y probar
              │
              └── PR → main → tag → PROD
                              │
                              └── PR main → develop
```

### Ejemplo práctico de un hotfix

Se detecta que un flujo crítico en producción está fallando y requiere una corrección inmediata:

```bash
# Partir de main, que representa la versión productiva
git switch main
git pull origin main

git switch -c hotfix/2026.09.1

# Aplicar únicamente la corrección urgente y probarla

git add .
git commit -m "Fix production approval notification"
git push -u origin hotfix/2026.09.1
```

Luego:

1. Crear el PR de `hotfix/2026.09.1` hacia `main`.
2. Solicitar la revisión prioritaria y adjuntar evidencia de la prueba.
3. Completar el PR cuando estén satisfechas las políticas.
4. Crear el tag `v2026.09.1` sobre `main`.
5. Promover manualmente la corrección a PROD.
6. Abrir un PR de `main` hacia `develop` para que la corrección no se pierda en la siguiente release.
7. Eliminar la rama `hotfix/2026.09.1` después de completar el proceso.

## Regla clave para decidir qué rama usar

- **Nueva funcionalidad:** `feature/*` desde `develop`.
- **Defecto encontrado antes de PROD:** `bugfix/*` desde `develop`.
- **Preparar una entrega:** `release/*` desde `develop`.
- **Defecto urgente en PROD:** `hotfix/*` desde `main`.

## Sincronización posterior a una release o hotfix

Cuando un cambio llega a `main`, debe regresar a `develop`. Esto evita que una corrección aplicada durante una release o hotfix desaparezca de la línea de desarrollo.

```bash
# Alternativa mediante PR, recomendada para mantener trazabilidad
Origen: main
Destino: develop

# No hacer push directo a develop si la rama está protegida.
```

El PR `main → develop` debe revisarse y completarse incluso si parece que no hay diferencias. Si Azure DevOps indica que no hay cambios, basta con dejar constancia de que la rama ya está sincronizada.

# Migración de ALM MVP a Azure DevOps

## Requisitos previos

- Acceso a una organización de Azure DevOps (crear en https://dev.azure.com si no existe).
- Permiso para crear proyectos en la organización.
- Git instalado localmente.
- Clone del repositorio `alm-platform` en tu máquina.

## Paso 1: Crear el proyecto en Azure DevOps

### 1.1 Acceder a la organización

1. Ve a https://dev.azure.com
2. Selecciona tu organización (o crea una nueva).

### 1.2 Crear proyecto nuevo

1. Haz clic en **New project**.
2. Completa:
   - **Project name:** `alm-platform` (o el nombre deseado).
   - **Description:** `Application Lifecycle Management para Power Platform y Finance & Operations`.
   - **Visibility:** Privado (recomendado para datos sensibles).
   - **Version control:** Git.
   - **Work item process:** Scrum o Agile (según tu preferencia).
3. Haz clic en **Create**.

### 1.3 Copiar la URL del repositorio

1. Dentro del proyecto, ve a **Repos** → **Files**.
2. Copia la URL del repositorio (aparece en la esquina superior derecha o en **Clone**).
   - Formato: `https://dev.azure.com/{organization}/{project}/_git/{repo}`

## Paso 2: Clonar desde GitHub y empujar a Azure DevOps

### 2.1 Preparar la sincronización local

En tu terminal:

```bash
# Navega a una carpeta temporal o de trabajo
cd /ruta/de/trabajo

# Clona el repositorio de GitHub con --mirror
git clone --mirror https://github.com/lifelpiedra/alm-platform.git

# Accede a la carpeta espejo
cd alm-platform.git
```

### 2.2 Empujar a Azure DevOps

Aún en la carpeta espejo:

```bash
# Reemplaza {organization} y {project} con tus valores
git push --mirror https://dev.azure.com/{organization}/{project}/_git/alm-platform
```

Si se solicita autenticación:

- **Usuario:** Tu correo de Azure DevOps.
- **Contraseña:** Genera un **Personal Access Token (PAT)**:
  1. En Azure DevOps, haz clic en tu perfil (arriba a la derecha).
  2. Selecciona **Personal access tokens**.
  3. Haz clic en **New Token**.
  4. Completa:
     - **Name:** `alm-migration`
     - **Scopes:** Selecciona `Code (Read & Write)`
     - **Expiration:** Elige el período (ej. 1 año).
  5. Copia el token y úsalo como contraseña.

### 2.3 Verificar la migración

1. Accede a tu proyecto en Azure DevOps.
2. Ve a **Repos** → **Files**.
3. Verifica que todas las ramas estén presentes (`main`, `develop`, etc.).
4. Confirma que el historial de commits sea íntegro.

## Paso 3: Configurar protecciones de rama

### 3.1 Acceder a políticas de rama

1. En tu proyecto, ve a **Repos** → **Branches**.
2. Haz clic en el ícono de tres puntos `...` junto a la rama `main`.
3. Selecciona **Branch policies**.

### 3.2 Configurar `main`

1. **Require a minimum number of reviewers:** Marca la casilla.
   - Mínimo recomendado: **2**.
   - Marca **Reject pull requests from creators** si lo deseas (evita auto-aprobación).

2. **Prohibit the most recent pusher from approving their own changes:** Marca.

3. **Allow completion on behalf of an absent reviewer:** Marca (solo para administradores).

4. **Block completion when there are active comments:** Marca.

5. **Check for comments resolution:** Marca **All comments**.

6. **Build validation:** Opcional (solo si usarás pipelines automáticas después).

7. **Automatically complete PRs:** Desmarca (control manual).

8. Haz clic en **Save**.

### 3.3 Configurar `develop`

Repite el procedimiento anterior para `develop`, pero con **1-2 revisores** según el equipo.

### 3.4 Prohibir pushes directos (opcional pero recomendado)

1. En **Branch policies**, bajo **Require a minimum number of reviewers**, marca:
   - **Make pushes that edit metadata forbidden**.
   - Esto fuerza el uso de Pull Requests.

## Paso 4: Configurar convenciones de nomenclatura

### 4.1 Acceder a repositorio settings

1. Ve a **Repos** → **Settings**.
2. Desplázate hasta **Repository settings**.

### 4.2 Configurar restricciones (opcional)

1. En **Repository settings**, busca **Limits**.
2. Puedes establecer límites de tamaño de archivo (ej. 100 MB).

## Paso 5: Configurar plantilla de Pull Request

### 5.1 Verificar que la plantilla se importó

1. Ve a **Repos** → **Pull requests**.
2. Haz clic en **New pull request**.
3. Deberías ver la plantilla de `.github/pull_request_template.md` automáticamente.

Si no aparece:

1. Ve a **Repos** → **Settings**.
2. En **Options**, busca **Pull Request settings**.
3. Confirma que la plantilla esté activa.

## Paso 6: Configurar permisos de grupo

### 6.1 Definir quiénes pueden aprobar

1. Ve a **Repos** → **Settings**.
2. Busca **Permissions** o **Group permissions**.
3. Asigna roles:
   - **Readers:** Solo lectura.
   - **Contributors:** Crear y completar PRs.
   - **Build Administrators:** Gestionar pipelines (si aplica).
   - **Project Administrators:** Toda la gestión.

## Paso 7: Limpiar y documentar

### 7.1 Limpiar el repositorio espejo local

```bash
cd ..
rm -rf alm-platform.git
```

### 7.2 Crear README local con instrucciones

Crea un documento interno en tu proyecto de Azure DevOps:

1. Ve a **Wikis** (o **Pages** si usas GitHub Pages).
2. Crea una página llamada **Setup ALM**.
3. Documenta:
   - URL del repositorio.
   - Ramas y convenciones.
   - Cómo clonar localmente.
   - Contacto para permisos.

### 7.3 Opcional: Configurar Repos en Azure Boards

Si usas Azure Boards para gestionar el trabajo:

1. Ve a **Boards** → **Sprints**.
2. Vincula PRs a work items:
   - Al crear un PR, menciona `AB#123` (donde 123 es el ID del work item).
   - Azure DevOps los vinculará automáticamente.

## Paso 8: Verificación final

### Checklist de migración completada

- [ ] Proyecto creado en Azure DevOps.
- [ ] Todas las ramas presentes (`main`, `develop`, `feature/*`, etc.).
- [ ] Historial de commits completo.
- [ ] Protecciones configuradas en `main` y `develop`.
- [ ] Plantilla de PR funcionando.
- [ ] Permisos asignados al equipo.
- [ ] Documentación migrada y accesible.
- [ ] Equipo notificado del nuevo repositorio.

## Paso 9: Redirigir el flujo de trabajo

### 9.1 Comunicar el cambio

1. Notifica al equipo que el repositorio ahora está en Azure DevOps.
2. Proporción la URL del proyecto.
3. Comparte instrucciones para clonar:

```bash
git clone https://dev.azure.com/{organization}/{project}/_git/alm-platform
cd alm-platform
```

### 9.2 Configurar credenciales (recomendado)

En Windows:

1. **Credential Manager**:
   - Control Panel → **Credential Manager** → **Windows Credentials** → **Add a generic credential**.
   - **Internet or network address:** `https://dev.azure.com`
   - **User name:** Tu correo de Azure AD.
   - **Password:** Tu PAT.

En macOS/Linux:

```bash
git config --global credential.helper osxkeychain  # macOS
# o
git config --global credential.helper store  # Linux (menos seguro, considera usar gpg)
```

Luego:

```bash
git clone https://dev.azure.com/{organization}/{project}/_git/alm-platform
# Se te pedirá usuario y PAT la primera vez; se guardarán.
```

## Notas finales

- **Backups:** Azure DevOps mantiene copias de seguridad automáticas.
- **Histórico:** GitHub sigue teniendo el repositorio original si necesitas referencia.
- **Sincronización:** Si necesitas mantener sincronizado GitHub y Azure DevOps, considera un script de espejo bidireccional (fuera del alcance de este MVP).
- **Automatización futura:** Azure DevOps Pipelines permite automatizar compilación, pruebas y despliegue cuando estén listos.

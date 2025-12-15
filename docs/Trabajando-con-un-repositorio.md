# Trabajando con un Repositorio - Git Flow

## Objetivo
Guía completa para seleccionar un repositorio, clonarlo, trabajar sobre él, hacer cambios y crear Pull Requests siguiendo la metodología **Git Flow**.

---

## ⚠️ INSTRUCCIONES CRÍTICAS PARA LLMs/IA

### Detección del Sistema Operativo
**ANTES de ejecutar cualquier comando, DEBES identificar el sistema operativo del usuario:**

1. **Windows con PowerShell**: 
   - Usar comandos nativos de PowerShell
   - Rutas con `\` o usar rutas universales
   - Comandos Git funcionan igual

2. **Linux/Mac/Git Bash**: 
   - Usar comandos de terminal Unix
   - Rutas con `/`
   - Comandos Git funcionan igual

### Proceso Automático Recomendado

**Ejecutar en este orden:**

1. **Listar repositorios** disponibles
2. **Clonar repositorio** seleccionado
3. **Configurar Git Flow** en el repositorio
4. **Crear branch** para nueva funcionalidad
5. **Hacer cambios** y commits
6. **Push** a GitHub
7. **Crear Pull Request** via GitHub CLI o API

**Ejecutar todo de forma fluida, pidiendo confirmación solo en decisiones críticas.**

---

## Requisitos Previos

### 1. Verificar Git Instalado

**Windows y Linux:**
```bash
git --version
```
**Resultado esperado**: `git version 2.x.x` o superior

### 2. Configurar Git (Si no está configurado)

**Windows y Linux:**
```bash
# Configurar nombre
git config --global user.name "Tu Nombre"

# Configurar email
git config --global user.email "tu@email.com"

# Verificar configuración
git config --list
```

### 3. Instalar Git Flow (Opcional pero Recomendado)

**Windows (con Git Bash o Chocolatey):**
```powershell
# Con Chocolatey
choco install gitflow-avh

# O descargar desde: https://github.com/nvie/gitflow/wiki/Windows
```

**Linux (Ubuntu/Debian):**
```bash
sudo apt-get install git-flow
```

**Mac:**
```bash
brew install git-flow-avh
```

**Verificar instalación:**
```bash
git flow version
```

### 4. Autenticación en GitHub

**Opción A: GitHub CLI (Recomendado):**
```bash
gh auth login
```

**Opción B: SSH Key:**
```bash
# Generar clave SSH
ssh-keygen -t ed25519 -C "tu@email.com"

# Copiar clave pública
cat ~/.ssh/id_ed25519.pub

# Agregar en: https://github.com/settings/keys
```

---

## Parte 1: Seleccionar y Clonar un Repositorio

### Paso 1: Listar tus Repositorios

**Usando GitHub CLI:**
```bash
gh repo list --limit 100
```

**Usando API con PowerShell (Windows):**
```powershell
$repos = Invoke-RestMethod -Uri "https://api.github.com/users/TU_USUARIO/repos?per_page=100" -Headers @{Accept="application/vnd.github.v3+json"}
$repos | Select-Object name, description, html_url | Format-Table -AutoSize
```

**Usando API con curl (Linux/Mac):**
```bash
curl -H "Accept: application/vnd.github.v3+json" https://api.github.com/users/TU_USUARIO/repos?per_page=100 | jq -r '.[] | "\(.name) - \(.description)"'
```

### Paso 2: Clonar el Repositorio

**Opción A: HTTPS (Recomendado para principiantes):**
```bash
git clone https://github.com/USUARIO/REPOSITORIO.git
cd REPOSITORIO
```

**Opción B: SSH (Recomendado para uso frecuente):**
```bash
git clone git@github.com:USUARIO/REPOSITORIO.git
cd REPOSITORIO
```

**Opción C: Usando GitHub CLI:**
```bash
gh repo clone USUARIO/REPOSITORIO
cd REPOSITORIO
```

### Paso 3: Verificar el Repositorio Clonado

```bash
# Ver información del repositorio
git remote -v

# Ver branches disponibles
git branch -a

# Ver estado actual
git status
```

---

## Parte 2: Configurar Git Flow

### ¿Qué es Git Flow?

Git Flow es una metodología de branching que define una estructura de ramas:

- **main/master**: Código en producción
- **develop**: Rama de desarrollo principal
- **feature/**: Nuevas funcionalidades
- **release/**: Preparación de releases
- **hotfix/**: Correcciones urgentes en producción

### Paso 1: Inicializar Git Flow

**Si Git Flow está instalado:**
```bash
git flow init
```

Responder a las preguntas (o usar valores por defecto):
- Branch name for production releases: `main` (o `master`)
- Branch name for "next release" development: `develop`
- Feature branches prefix: `feature/`
- Release branches prefix: `release/`
- Hotfix branches prefix: `hotfix/`
- Support branches prefix: `support/`
- Version tag prefix: `v`

**Si Git Flow NO está instalado (Método Manual):**
```bash
# Crear rama develop si no existe
git checkout -b develop

# Subir develop a GitHub
git push -u origin develop

# Volver a main
git checkout main
```

### Paso 2: Verificar Configuración

```bash
# Ver todas las ramas
git branch -a

# Deberías ver:
# * main (o master)
#   develop
```

---

## Parte 3: Trabajar en una Nueva Funcionalidad

### Flujo Completo con Git Flow

#### Paso 1: Crear Feature Branch

**Con Git Flow:**
```bash
# Crear y cambiar a nueva feature
git flow feature start nombre-de-la-funcionalidad

# Esto crea: feature/nombre-de-la-funcionalidad
```

**Sin Git Flow (Manual):**
```bash
# Asegurarse de estar en develop
git checkout develop

# Actualizar develop
git pull origin develop

# Crear nueva rama feature
git checkout -b feature/nombre-de-la-funcionalidad
```

#### Paso 2: Hacer Cambios en el Código

**Ejemplo: Crear/Modificar archivos**

**Windows PowerShell:**
```powershell
# Crear un nuevo archivo
New-Item -Path "nuevo-archivo.txt" -ItemType File -Value "Contenido del archivo"

# Editar archivo (abrirá en notepad)
notepad nuevo-archivo.txt

# O usar VS Code
code .
```

**Linux/Mac:**
```bash
# Crear un nuevo archivo
echo "Contenido del archivo" > nuevo-archivo.txt

# Editar archivo
nano nuevo-archivo.txt
# o
vim nuevo-archivo.txt
# o
code .
```

#### Paso 3: Ver Cambios Realizados

```bash
# Ver archivos modificados
git status

# Ver diferencias específicas
git diff

# Ver diferencias de un archivo específico
git diff nombre-archivo.txt
```

#### Paso 4: Agregar Cambios al Staging

```bash
# Agregar un archivo específico
git add nombre-archivo.txt

# Agregar todos los archivos modificados
git add .

# Agregar archivos por extensión
git add *.js

# Ver qué está en staging
git status
```

#### Paso 5: Hacer Commit

```bash
# Commit con mensaje descriptivo
git commit -m "feat: agregar nueva funcionalidad X"

# Commit con mensaje detallado
git commit -m "feat: agregar nueva funcionalidad X

- Implementa característica Y
- Agrega validación Z
- Actualiza documentación"
```

**Convenciones de Mensajes de Commit (Conventional Commits):**
- `feat:` - Nueva funcionalidad
- `fix:` - Corrección de bug
- `docs:` - Cambios en documentación
- `style:` - Cambios de formato (no afectan el código)
- `refactor:` - Refactorización de código
- `test:` - Agregar o modificar tests
- `chore:` - Tareas de mantenimiento

#### Paso 6: Push de la Feature Branch

```bash
# Primera vez (crear rama en GitHub)
git push -u origin feature/nombre-de-la-funcionalidad

# Siguientes veces
git push
```

---

## Parte 4: Crear Pull Request

### Opción A: Usando GitHub CLI (Recomendado)

```bash
# Crear PR desde la rama actual
gh pr create --base develop --title "Título del PR" --body "Descripción detallada del PR"

# Crear PR interactivo (te preguntará los detalles)
gh pr create

# Crear PR con template
gh pr create --base develop --title "feat: nueva funcionalidad" --body "
## Descripción
Breve descripción de los cambios

## Cambios realizados
- Cambio 1
- Cambio 2
- Cambio 3

## Testing
- [ ] Probado localmente
- [ ] Tests pasando
"
```

### Opción B: Usando la API de GitHub

**Windows PowerShell:**
```powershell
$token = "TU_TOKEN_AQUI"
$headers = @{
    Authorization = "token $token"
    Accept = "application/vnd.github.v3+json"
}

$body = @{
    title = "feat: nueva funcionalidad"
    body = "Descripción detallada del PR"
    head = "feature/nombre-de-la-funcionalidad"
    base = "develop"
} | ConvertTo-Json

Invoke-RestMethod -Uri "https://api.github.com/repos/USUARIO/REPOSITORIO/pulls" -Method Post -Headers $headers -Body $body
```

**Linux/Mac:**
```bash
curl -X POST \
  -H "Authorization: token TU_TOKEN" \
  -H "Accept: application/vnd.github.v3+json" \
  https://api.github.com/repos/USUARIO/REPOSITORIO/pulls \
  -d '{
    "title": "feat: nueva funcionalidad",
    "body": "Descripción detallada del PR",
    "head": "feature/nombre-de-la-funcionalidad",
    "base": "develop"
  }'
```

### Opción C: Usando la Interfaz Web de GitHub

1. Ir a: `https://github.com/USUARIO/REPOSITORIO`
2. GitHub detectará tu nueva rama y mostrará un botón "Compare & pull request"
3. Click en el botón
4. Completar:
   - **Base branch**: `develop`
   - **Compare branch**: `feature/nombre-de-la-funcionalidad`
   - **Título**: Descripción corta
   - **Descripción**: Detalles del PR
5. Click en "Create pull request"

---

## Parte 5: Finalizar Feature (Después de Aprobar PR)

### Con Git Flow

```bash
# Finalizar feature (merge a develop)
git flow feature finish nombre-de-la-funcionalidad

# Esto hace:
# 1. Merge de feature a develop
# 2. Elimina la rama feature local
# 3. Te deja en develop

# Push de develop actualizado
git push origin develop

# Eliminar rama remota
git push origin --delete feature/nombre-de-la-funcionalidad
```

### Sin Git Flow (Manual)

```bash
# Cambiar a develop
git checkout develop

# Actualizar develop
git pull origin develop

# Merge de feature
git merge feature/nombre-de-la-funcionalidad

# Push a GitHub
git push origin develop

# Eliminar rama local
git branch -d feature/nombre-de-la-funcionalidad

# Eliminar rama remota
git push origin --delete feature/nombre-de-la-funcionalidad
```

---

## Parte 6: Crear un Release

### Con Git Flow

```bash
# Iniciar release (desde develop)
git flow release start 1.0.0

# Hacer ajustes finales si es necesario
# Actualizar versiones, changelog, etc.

# Finalizar release
git flow release finish 1.0.0

# Esto hace:
# 1. Merge a main
# 2. Tag con la versión
# 3. Merge de vuelta a develop
# 4. Elimina rama release

# Push de todo
git push origin main
git push origin develop
git push origin --tags
```

### Sin Git Flow (Manual)

```bash
# Crear rama release desde develop
git checkout develop
git pull origin develop
git checkout -b release/1.0.0

# Hacer ajustes finales
# ... commits si es necesario ...

# Merge a main
git checkout main
git merge release/1.0.0

# Crear tag
git tag -a v1.0.0 -m "Release version 1.0.0"

# Merge de vuelta a develop
git checkout develop
git merge release/1.0.0

# Push de todo
git push origin main
git push origin develop
git push origin --tags

# Eliminar rama release
git branch -d release/1.0.0
```

---

## Parte 7: Hotfix (Corrección Urgente en Producción)

### Con Git Flow

```bash
# Iniciar hotfix desde main
git flow hotfix start 1.0.1

# Hacer corrección
# ... editar archivos ...
git add .
git commit -m "fix: corregir bug crítico"

# Finalizar hotfix
git flow hotfix finish 1.0.1

# Push de todo
git push origin main
git push origin develop
git push origin --tags
```

### Sin Git Flow (Manual)

```bash
# Crear rama hotfix desde main
git checkout main
git pull origin main
git checkout -b hotfix/1.0.1

# Hacer corrección
# ... editar archivos ...
git add .
git commit -m "fix: corregir bug crítico"

# Merge a main
git checkout main
git merge hotfix/1.0.1
git tag -a v1.0.1 -m "Hotfix version 1.0.1"

# Merge a develop
git checkout develop
git merge hotfix/1.0.1

# Push de todo
git push origin main
git push origin develop
git push origin --tags

# Eliminar rama hotfix
git branch -d hotfix/1.0.1
```

---

## Comandos Útiles de Git

### Ver Historial

```bash
# Ver log completo
git log

# Ver log resumido
git log --oneline

# Ver log con gráfico
git log --graph --oneline --all

# Ver log de un archivo específico
git log -- nombre-archivo.txt
```

### Deshacer Cambios

```bash
# Deshacer cambios en archivo (antes de add)
git checkout -- nombre-archivo.txt

# Deshacer add (quitar de staging)
git reset HEAD nombre-archivo.txt

# Deshacer último commit (mantener cambios)
git reset --soft HEAD~1

# Deshacer último commit (eliminar cambios)
git reset --hard HEAD~1
```

### Trabajar con Ramas

```bash
# Ver todas las ramas
git branch -a

# Crear nueva rama
git branch nombre-rama

# Cambiar de rama
git checkout nombre-rama

# Crear y cambiar de rama
git checkout -b nombre-rama

# Eliminar rama local
git branch -d nombre-rama

# Eliminar rama remota
git push origin --delete nombre-rama

# Renombrar rama actual
git branch -m nuevo-nombre
```

### Sincronizar con Remoto

```bash
# Actualizar referencias remotas
git fetch

# Actualizar rama actual
git pull

# Actualizar y hacer rebase
git pull --rebase

# Ver ramas remotas
git branch -r

# Ver todos los remotos
git remote -v
```

### Stash (Guardar Cambios Temporalmente)

```bash
# Guardar cambios sin commit
git stash

# Ver lista de stashes
git stash list

# Aplicar último stash
git stash apply

# Aplicar y eliminar último stash
git stash pop

# Eliminar todos los stashes
git stash clear
```

---

## Flujo de Trabajo Completo - Ejemplo Práctico

### Escenario: Agregar nueva funcionalidad de login

**Paso 1: Preparación**
```bash
# Clonar repositorio
gh repo clone USUARIO/mi-proyecto
cd mi-proyecto

# Inicializar Git Flow
git flow init -d  # -d usa valores por defecto
```

**Paso 2: Crear Feature**
```bash
# Crear feature branch
git flow feature start login-system

# Verificar rama actual
git branch
# * feature/login-system
```

**Paso 3: Desarrollar**
```bash
# Crear archivos
echo "class Login { }" > login.js
echo "# Login System" > login.md

# Ver cambios
git status

# Agregar archivos
git add .

# Commit
git commit -m "feat: implementar sistema de login

- Agregar clase Login
- Agregar documentación
- Implementar validación de usuarios"
```

**Paso 4: Push y PR**
```bash
# Push de feature
git push -u origin feature/login-system

# Crear PR
gh pr create --base develop --title "feat: sistema de login" --body "
## Descripción
Implementación del sistema de login con validación de usuarios

## Cambios
- Nueva clase Login
- Validación de credenciales
- Documentación actualizada

## Testing
- [x] Probado localmente
- [x] Tests unitarios pasando
"
```

**Paso 5: Después de Aprobar PR**
```bash
# Finalizar feature
git flow feature finish login-system

# Push de develop
git push origin develop
```

**Paso 6: Crear Release**
```bash
# Iniciar release
git flow release start 1.0.0

# Actualizar versión en package.json, etc.
# ... ediciones ...

# Commit de versión
git commit -am "chore: bump version to 1.0.0"

# Finalizar release
git flow release finish 1.0.0

# Push de todo
git push origin main
git push origin develop
git push origin --tags
```

---

## Mejores Prácticas

### 1. Commits

✅ **Hacer:**
- Commits pequeños y frecuentes
- Mensajes descriptivos
- Usar conventional commits
- Un commit = un cambio lógico

❌ **Evitar:**
- Commits gigantes con muchos cambios
- Mensajes vagos ("fix", "update", "changes")
- Mezclar múltiples funcionalidades en un commit

### 2. Branches

✅ **Hacer:**
- Nombres descriptivos (`feature/user-authentication`)
- Una branch por funcionalidad
- Mantener branches actualizadas con develop
- Eliminar branches después de merge

❌ **Evitar:**
- Nombres genéricos (`test`, `new-branch`)
- Trabajar directamente en main/develop
- Branches de larga duración sin merge

### 3. Pull Requests

✅ **Hacer:**
- Descripción clara y detallada
- Incluir contexto y screenshots si aplica
- Solicitar revisores apropiados
- Responder a comentarios

❌ **Evitar:**
- PRs gigantes (>500 líneas)
- Descripción vacía o "WIP"
- Ignorar comentarios de revisión

### 4. Git Flow

✅ **Hacer:**
- Seguir la estructura de ramas
- Feature → Develop → Release → Main
- Usar tags para versiones
- Documentar releases

❌ **Evitar:**
- Saltarse pasos del flujo
- Merge directo a main
- Olvidar crear tags

---

## Solución de Problemas Comunes

### Error: "fatal: not a git repository"
```bash
# Solución: Inicializar git
git init
```

### Error: "Permission denied (publickey)"
```bash
# Solución: Configurar SSH key
ssh-keygen -t ed25519 -C "tu@email.com"
# Agregar key en GitHub: https://github.com/settings/keys
```

### Error: "Your branch is behind"
```bash
# Solución: Actualizar rama
git pull origin nombre-rama
```

### Error: "Merge conflict"
```bash
# Solución: Resolver conflictos manualmente
# 1. Abrir archivos en conflicto
# 2. Buscar marcadores: <<<<<<<, =======, >>>>>>>
# 3. Editar y resolver
# 4. Agregar archivos resueltos
git add archivo-resuelto.txt
# 5. Continuar merge
git commit
```

### Error: "fatal: refusing to merge unrelated histories"
```bash
# Solución: Permitir merge de historias no relacionadas
git pull origin main --allow-unrelated-histories
```

---

## Recursos Adicionales

### Documentación Oficial
- **Git**: https://git-scm.com/doc
- **Git Flow**: https://nvie.com/posts/a-successful-git-branching-model/
- **GitHub CLI**: https://cli.github.com/manual/
- **Conventional Commits**: https://www.conventionalcommits.org/

### Herramientas Útiles
- **GitKraken**: Cliente visual de Git
- **SourceTree**: Cliente visual de Git
- **VS Code**: Editor con integración Git
- **GitHub Desktop**: Cliente oficial de GitHub

### Cheat Sheets
- **Git**: https://education.github.com/git-cheat-sheet-education.pdf
- **Git Flow**: https://danielkummer.github.io/git-flow-cheatsheet/

---

## Resumen de Comandos por Fase

| Fase | Comandos Principales |
|------|---------------------|
| **Clonar** | `gh repo clone USER/REPO` o `git clone URL` |
| **Iniciar Git Flow** | `git flow init` |
| **Nueva Feature** | `git flow feature start NOMBRE` |
| **Hacer Cambios** | `git add .` → `git commit -m "mensaje"` |
| **Push** | `git push -u origin feature/NOMBRE` |
| **Crear PR** | `gh pr create` |
| **Finalizar Feature** | `git flow feature finish NOMBRE` |
| **Release** | `git flow release start X.Y.Z` → `git flow release finish X.Y.Z` |
| **Hotfix** | `git flow hotfix start X.Y.Z` → `git flow hotfix finish X.Y.Z` |

---

## Diagrama de Git Flow

```
main (producción)
  |
  |---- hotfix/1.0.1 (corrección urgente)
  |       |
  |<------|  (merge y tag v1.0.1)
  |
  |<---- release/1.0.0 (preparar release)
  |         |
  |         |<---- develop (desarrollo)
  |                  |
  |                  |<---- feature/login (nueva funcionalidad)
  |                  |<---- feature/dashboard (nueva funcionalidad)
  |                  |
  |<-----------------|  (merge release)
  |
  v
```

---

## Notas Finales

1. **Git Flow es una guía, no una regla estricta**: Adapta el flujo a las necesidades de tu proyecto.

2. **Comunicación es clave**: Usa PRs para discutir cambios y mantener al equipo informado.

3. **Automatización**: Considera usar GitHub Actions para CI/CD.

4. **Protección de ramas**: Configura branch protection rules en GitHub para main y develop.

5. **Code reviews**: Siempre solicita revisión de código antes de mergear.

6. **Documentación**: Mantén README.md y CHANGELOG.md actualizados.

7. **Versionado semántico**: Usa MAJOR.MINOR.PATCH (ej: 1.0.0)
   - MAJOR: Cambios incompatibles
   - MINOR: Nueva funcionalidad compatible
   - PATCH: Correcciones de bugs

---

**¡Listo para trabajar con Git Flow!** 🚀

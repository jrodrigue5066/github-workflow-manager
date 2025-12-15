# Crear Repositorio en GitHub

## 🎯 Objetivo
Guía completa y optimizada para crear un nuevo repositorio en GitHub usando GitHub CLI, con Git Flow configurado automáticamente.

---

## 🚀 Método Recomendado: GitHub CLI (Más Rápido y Eficiente)

### ¿Por qué GitHub CLI?
- ✅ **Más rápido**: Crea el repositorio desde la terminal
- ✅ **Automatizado**: Configura todo en un solo flujo
- ✅ **Menos errores**: No necesitas crear manualmente en el navegador
- ✅ **Autenticación segura**: Usa OAuth en lugar de tokens

---

## 📦 Paso 1: Instalación de GitHub CLI

### Verificar si ya está instalado

```bash
gh --version
```

Si ves la versión (ej: `gh version 2.83.2`), **salta al Paso 2**.

### Instalación según tu sistema operativo

#### Windows (PowerShell)

```powershell
# Opción 1: Con winget (recomendado)
winget install --id GitHub.cli

# Opción 2: Con Chocolatey
choco install gh

# Opción 3: Con Scoop
scoop install gh
```

**⚠️ Importante para Windows**: Después de instalar, usa la ruta completa la primera vez:

```powershell
C:\Progra~1\GitHub` CLI\gh.exe --version
```

O reinicia la terminal para que reconozca el comando `gh`.

#### macOS

```bash
# Con Homebrew
brew install gh
```

#### Linux

**Ubuntu/Debian:**
```bash
type -p curl >/dev/null || (sudo apt update && sudo apt install curl -y)
curl -fsSL https://cli.github.com/packages/githubcli-archive-keyring.gpg | sudo dd of=/usr/share/keyrings/githubcli-archive-keyring.gpg \
&& sudo chmod go+r /usr/share/keyrings/githubcli-archive-keyring.gpg \
&& echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" | sudo tee /etc/apt/sources.list.d/github-cli.list > /dev/null \
&& sudo apt update \
&& sudo apt install gh -y
```

**Fedora/CentOS/RHEL:**
```bash
sudo dnf install gh
```

**Arch Linux:**
```bash
sudo pacman -S github-cli
```

---

## 🔐 Paso 2: Autenticación con GitHub

### Iniciar autenticación

```bash
gh auth login
```

### Flujo de autenticación paso a paso

El comando te hará varias preguntas. Aquí está el flujo completo:

#### 1. ¿Dónde usas GitHub?
```
? Where do you use GitHub?
> GitHub.com
  Other
```
**Selecciona**: `GitHub.com` (presiona Enter)

#### 2. ¿Qué protocolo prefieres?
```
? What is your preferred protocol for Git operations on this host?
> HTTPS
  SSH
```
**Selecciona**: `HTTPS` (presiona Enter)

#### 3. ¿Autenticar Git con tus credenciales de GitHub?
```
? Authenticate Git with your GitHub credentials? (Y/n)
```
**Escribe**: `Y` (presiona Enter)

#### 4. ¿Cómo quieres autenticarte?
```
? How would you like to authenticate GitHub CLI?
> Login with a web browser
  Paste an authentication token
```
**Selecciona**: `Login with a web browser` (presiona Enter)

#### 5. Código de dispositivo
```
! First copy your one-time code: XXXX-XXXX
Press Enter to open https://github.com/login/device in your browser...
```

**Pasos**:
1. **Copia el código** (ej: `E482-0CC4`)
2. **Presiona Enter** para abrir el navegador
3. **Inicia sesión en GitHub** (si no lo has hecho)
   - Usa tu usuario y contraseña
   - Si tienes 2FA, ingresa tu código de 6 dígitos del authenticator
4. **Pega el código de dispositivo** en la página que se abrió
5. **Haz clic en "Continue"**
6. **Haz clic en "Authorize GitHub CLI"**

#### 6. Confirmación
```
✓ Authentication complete.
✓ Configured git protocol
✓ Logged in as tu-usuario
```

### ⚠️ Diferencia importante: Código de Dispositivo vs Código 2FA

- **Código de Dispositivo** (`XXXX-XXXX`): 
  - Es el código que GitHub CLI te muestra
  - Lo ingresas en https://github.com/login/device
  - Tiene 8 caracteres con un guión (ej: `E482-0CC4`)
  
- **Código 2FA** (6 dígitos):
  - Es el código de tu aplicación authenticator (Google Authenticator, Authy, etc.)
  - Lo usas al iniciar sesión en GitHub
  - Tiene 6 dígitos (ej: `123456`)

### Verificar autenticación

```bash
gh auth status
```

Deberías ver:
```
✓ Logged in to github.com as tu-usuario
✓ Git operations for github.com configured to use https protocol.
✓ Token: gho_************************************
```

---

## 📁 Paso 3: Crear Repositorio Completo

### Opción A: Flujo Completo Automatizado (Recomendado)

Este script crea todo de una vez:

**Windows PowerShell:**
```powershell
# Variables (MODIFICAR ESTOS VALORES)
$repoName = "mi-proyecto"
$description = "Descripción del proyecto"
$visibility = "public"  # o "private"

# Crear directorio y entrar
mkdir $repoName
cd $repoName

# Inicializar Git
git init
git branch -M main

# Crear archivos básicos
@"
# $repoName

$description

## Instalación

``````bash
git clone https://github.com/tu-usuario/$repoName.git
cd $repoName
``````

## Uso

Documenta cómo usar tu proyecto aquí.

## Licencia

MIT
"@ | Out-File README.md -Encoding UTF8

# Crear .gitignore completo
@"
# Archivos de sistema
.DS_Store
Thumbs.db

# IDEs
.vscode/
.idea/
*.swp
*.swo

# Logs
*.log

# Archivos temporales
*.tmp
*.temp

# Node modules
node_modules/

# Python
__pycache__/
*.py[cod]
*.pyo
*.pyd
.Python
env/
venv/

# Tokens y credenciales
.env
.env.*
!.env.example
*.token
*.key
*.pem
credentials.json
secrets.json
config.local.*
auth.json
.secrets/

# GitHub CLI tokens
gh_token
.gh_token

# Git credentials
.git-credentials

# SSH keys
id_rsa
id_rsa.pub
*.ppk

# Configuración personal
.user.ini
personal.config
local.settings.json
"@ | Out-File .gitignore -Encoding UTF8

# Commit inicial
git add .
git commit -m "chore: initial commit"

# Crear rama develop
git checkout -b develop
git checkout main

# Crear repositorio en GitHub
gh repo create $repoName --$visibility --description "$description" --source=. --remote=origin

# Push de ambas ramas
git push -u origin main
git push -u origin develop

# Crear tag inicial
git tag -a v1.0.0 -m "Initial release"
git push origin --tags

Write-Host "`n✅ Repositorio creado exitosamente!`n"
Write-Host "🔗 URL: https://github.com/$(gh api user --jq .login)/$repoName"
Write-Host "🌿 Ramas: main, develop"
Write-Host "🏷️  Tag: v1.0.0"
```

**Linux/Mac:**
```bash
# Variables (MODIFICAR ESTOS VALORES)
REPO_NAME="mi-proyecto"
DESCRIPTION="Descripción del proyecto"
VISIBILITY="public"  # o "private"

# Crear directorio y entrar
mkdir $REPO_NAME
cd $REPO_NAME

# Inicializar Git
git init
git branch -M main

# Crear README
cat > README.md << EOF
# $REPO_NAME

$DESCRIPTION

## Instalación

\`\`\`bash
git clone https://github.com/tu-usuario/$REPO_NAME.git
cd $REPO_NAME
\`\`\`

## Uso

Documenta cómo usar tu proyecto aquí.

## Licencia

MIT
EOF

# Crear .gitignore completo
cat > .gitignore << 'EOF'
# Archivos de sistema
.DS_Store
Thumbs.db

# IDEs
.vscode/
.idea/
*.swp
*.swo

# Logs
*.log

# Archivos temporales
*.tmp
*.temp

# Node modules
node_modules/

# Python
__pycache__/
*.py[cod]
*.pyo
*.pyd
.Python
env/
venv/

# Tokens y credenciales
.env
.env.*
!.env.example
*.token
*.key
*.pem
credentials.json
secrets.json
config.local.*
auth.json
.secrets/

# GitHub CLI tokens
gh_token
.gh_token

# Git credentials
.git-credentials

# SSH keys
id_rsa
id_rsa.pub
*.ppk

# Configuración personal
.user.ini
personal.config
local.settings.json
EOF

# Commit inicial
git add .
git commit -m "chore: initial commit"

# Crear rama develop
git checkout -b develop
git checkout main

# Crear repositorio en GitHub
gh repo create $REPO_NAME --$VISIBILITY --description "$DESCRIPTION" --source=. --remote=origin

# Push de ambas ramas
git push -u origin main
git push -u origin develop

# Crear tag inicial
git tag -a v1.0.0 -m "Initial release"
git push origin --tags

echo ""
echo "✅ Repositorio creado exitosamente!"
echo "🔗 URL: https://github.com/$(gh api user --jq .login)/$REPO_NAME"
echo "🌿 Ramas: main, develop"
echo "🏷️  Tag: v1.0.0"
```

### Opción B: Paso a Paso Manual

Si prefieres hacerlo paso a paso:

```bash
# 1. Crear directorio
mkdir mi-proyecto
cd mi-proyecto

# 2. Inicializar Git
git init
git branch -M main

# 3. Crear archivos (README.md, .gitignore, etc.)
echo "# Mi Proyecto" > README.md

# 4. Commit inicial
git add .
git commit -m "chore: initial commit"

# 5. Crear rama develop
git checkout -b develop
git checkout main

# 6. Crear repo en GitHub
gh repo create mi-proyecto --public --description "Mi proyecto"

# 7. Conectar y push
git remote add origin https://github.com/tu-usuario/mi-proyecto.git
git push -u origin main
git push -u origin develop
```

---

## 🔧 Configuración Post-Creación

### Cambiar rama por defecto a develop

```bash
gh repo edit --default-branch develop
```

O manualmente:
1. Ve a: `https://github.com/tu-usuario/tu-repo/settings`
2. En **Default branch**, selecciona `develop`
3. Haz clic en **Update**

### Verificar configuración

```bash
# Ver repositorio
gh repo view

# Ver ramas
git branch -a

# Ver remotes
git remote -v

# Ver estado
git status
```

---

## 🌿 Flujo de Trabajo con Git Flow

### Crear una nueva feature

```bash
# 1. Asegurarse de estar en develop actualizado
git checkout develop
git pull origin develop

# 2. Crear rama feature
git checkout -b feature/nombre-funcionalidad

# 3. Desarrollar
# ... hacer cambios ...
git add .
git commit -m "feat: descripción de la funcionalidad"

# 4. Push y crear PR
git push -u origin feature/nombre-funcionalidad

# 5. Crear Pull Request
gh pr create --base develop --head feature/nombre-funcionalidad --title "Nueva funcionalidad" --body "Descripción detallada"

# 6. Después de aprobar y mergear el PR
git checkout develop
git pull origin develop
git branch -d feature/nombre-funcionalidad
```

### Crear un release

```bash
# 1. Desde develop, crear rama release
git checkout develop
git pull origin develop
git checkout -b release/1.1.0

# 2. Ajustes finales (versiones, changelog, etc.)
# ... hacer cambios ...
git commit -am "chore: bump version to 1.1.0"

# 3. Merge a main
git checkout main
git merge release/1.1.0
git tag -a v1.1.0 -m "Release 1.1.0"

# 4. Merge de vuelta a develop
git checkout develop
git merge release/1.1.0

# 5. Push todo
git push origin main develop --tags

# 6. Eliminar rama release
git branch -d release/1.1.0
```

---

## ❌ Solución de Problemas Comunes

### 1. GitHub CLI no reconocido después de instalación (Windows)

**Síntoma:**
```
gh : El término 'gh' no se reconoce como nombre de un cmdlet...
```

**Soluciones:**

**Opción A: Usar ruta completa**
```powershell
C:\Progra~1\GitHub` CLI\gh.exe --version
```

**Opción B: Reiniciar terminal**
Cierra y abre una nueva ventana de PowerShell.

**Opción C: Agregar al PATH manualmente**
```powershell
$env:Path += ";C:\Program Files\GitHub CLI"
```

### 2. Error: "repository not found" al hacer push

**Causa**: El repositorio no existe en GitHub.

**Solución**: Crear el repositorio primero
```bash
gh repo create nombre-repo --public
```

### 3. Error: "gh: command not found" (Linux/Mac)

**Causa**: GitHub CLI no instalado.

**Solución**: Instalar según tu sistema (ver Paso 1).

### 4. Error: "failed to authenticate"

**Causa**: No estás autenticado o el token expiró.

**Solución**: Volver a autenticar
```bash
gh auth login
```

### 5. Confusión entre código de dispositivo y código 2FA

**Recuerda**:
- **Código de dispositivo** (8 caracteres con guión): Lo da GitHub CLI, lo ingresas en https://github.com/login/device
- **Código 2FA** (6 dígitos): Lo da tu app authenticator, lo usas al iniciar sesión en GitHub

### 6. Error: "Permission denied (publickey)"

**Causa**: Intentando usar SSH sin configurar.

**Solución**: Usar HTTPS (ya configurado si seguiste el Paso 2).

### 7. Error: "remote origin already exists"

**Solución**:
```bash
git remote remove origin
git remote add origin https://github.com/usuario/repo.git
```

### 8. Verificar si GitHub CLI está autenticado

```bash
gh auth status
```

Si no está autenticado, ejecuta:
```bash
gh auth login
```

---

## 📋 Checklist Rápido

### Antes de empezar
- [ ] GitHub CLI instalado (`gh --version`)
- [ ] Autenticado en GitHub (`gh auth status`)
- [ ] Decidir nombre del repositorio
- [ ] Decidir visibilidad (public/private)

### Creación
- [ ] Crear directorio del proyecto
- [ ] Inicializar Git (`git init`)
- [ ] Crear README.md
- [ ] Crear .gitignore (con protección de credenciales)
- [ ] Commit inicial
- [ ] Crear rama develop
- [ ] Crear repositorio en GitHub (`gh repo create`)
- [ ] Push de main y develop
- [ ] Crear tag v1.0.0
- [ ] Configurar develop como rama por defecto

### Verificación
- [ ] Repositorio visible en GitHub
- [ ] README.md se muestra correctamente
- [ ] Ambas ramas existen (main y develop)
- [ ] Tag v1.0.0 visible
- [ ] .gitignore protege datos sensibles

---

## 🔒 Seguridad: Protección de Datos Sensibles

### ⚠️ IMPORTANTE: Nunca subas a GitHub

- ❌ Contraseñas
- ❌ Tokens de API
- ❌ Claves privadas (SSH, PEM, etc.)
- ❌ Archivos `.env` con credenciales
- ❌ Configuraciones personales con datos sensibles

### ✅ Usa .gitignore

El `.gitignore` incluido en los scripts protege automáticamente:

```gitignore
# Tokens y credenciales
.env
.env.*
!.env.example
*.token
*.key
*.pem
credentials.json
secrets.json
config.local.*
auth.json
.secrets/

# GitHub CLI tokens
gh_token
.gh_token

# Git credentials
.git-credentials

# SSH keys
id_rsa
id_rsa.pub
*.ppk

# Configuración personal
.user.ini
personal.config
local.settings.json
```

### 📝 Buena práctica: Usar archivos de ejemplo

En lugar de subir `.env` con credenciales, crea `.env.example`:

```bash
# .env.example (SÍ se sube a GitHub)
DATABASE_URL=postgresql://usuario:password@localhost:5432/dbname
API_KEY=tu_api_key_aqui
SECRET_TOKEN=tu_token_aqui
```

```bash
# .env (NO se sube a GitHub - está en .gitignore)
DATABASE_URL=postgresql://admin:mipassword123@localhost:5432/produccion
API_KEY=sk_live_51H...
SECRET_TOKEN=ghp_abc123...
```

---

## 📚 Métodos Alternativos

### Método Manual (Sin GitHub CLI)

Si no puedes o no quieres instalar GitHub CLI:

1. **Crear en GitHub Web**: https://github.com/new
2. **Clonar localmente**:
   ```bash
   git clone https://github.com/usuario/repo.git
   cd repo
   ```
3. **Configurar Git Flow**:
   ```bash
   git checkout -b develop
   git push -u origin develop
   ```

### Método con API de GitHub

Para automatización avanzada, usa la API:

```bash
# Crear repositorio
curl -X POST \
  -H "Authorization: Bearer TU_TOKEN" \
  -H "Accept: application/vnd.github.v3+json" \
  https://api.github.com/user/repos \
  -d '{"name":"mi-repo","description":"Descripción","private":false}'
```

---

## 🎓 Mejores Prácticas

### Nombres de Repositorio
✅ **Usar**: `mi-proyecto`, `api-rest`, `web-app`  
❌ **Evitar**: `Mi Proyecto`, `API_REST`, `proyecto123`

### Mensajes de Commit
✅ **Usar**: `feat: add login`, `fix: resolve bug`, `docs: update README`  
❌ **Evitar**: `update`, `changes`, `fix`

### Estructura de Ramas
✅ **Usar**: `main` (producción), `develop` (desarrollo), `feature/nombre`  
❌ **Evitar**: `master`, trabajar directamente en `main`

---

## 📖 Recursos Adicionales

- **GitHub CLI Docs**: https://cli.github.com/manual/
- **Git Flow**: https://nvie.com/posts/a-successful-git-branching-model/
- **GitHub Docs**: https://docs.github.com/
- **Conventional Commits**: https://www.conventionalcommits.org/

---

**Última actualización**: Diciembre 2025  
**Versión**: 2.0 - Optimizada con GitHub CLI

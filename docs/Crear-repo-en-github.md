# Crear Repositorio en GitHub

## Objetivo
Guía simplificada para crear un nuevo repositorio en GitHub y configurarlo con Git Flow, compatible con Windows y Linux.

---

## ⚠️ INSTRUCCIONES PARA LLMs/IA

### Detección del Sistema Operativo
1. **Windows PowerShell**: Usar comandos nativos de PowerShell
2. **Linux/Mac Bash**: Usar comandos de terminal Unix

### Proceso Automatizado
1. Crear repositorio en GitHub (manual o API)
2. Inicializar Git localmente
3. Configurar Git Flow (main + develop)
4. Crear estructura básica
5. Commit inicial y push

**Ejecutar pasos secuencialmente, pidiendo confirmación solo para decisiones críticas.**

---

## Método 1: Creación Manual (Recomendado - Más Simple)

### Paso 1: Crear en GitHub Web

1. Ir a: **https://github.com/new**
2. Completar:
   - **Repository name**: nombre-del-proyecto
   - **Description**: Descripción breve
   - **Visibility**: Public o Private
   - **NO marcar**: "Initialize this repository with a README"
3. Click **Create repository**

### Paso 2: Inicializar Localmente

**Windows PowerShell:**
```powershell
# Crear carpeta del proyecto
mkdir nombre-del-proyecto
cd nombre-del-proyecto

# Inicializar Git
git init

# Crear README básico
@"
# Nombre del Proyecto

Descripción del proyecto.

## Instalación

``````bash
git clone https://github.com/USUARIO/nombre-del-proyecto.git
``````
"@ | Out-File -FilePath "README.md" -Encoding UTF8

# Crear .gitignore
@"
# Sistema
.DS_Store
Thumbs.db

# IDEs
.vscode/
.idea/

# Logs
*.log

# Credenciales
.env
*.token
"@ | Out-File -FilePath ".gitignore" -Encoding UTF8
```

**Linux/Mac:**
```bash
# Crear carpeta del proyecto
mkdir nombre-del-proyecto
cd nombre-del-proyecto

# Inicializar Git
git init

# Crear README básico
cat > README.md << 'EOF'
# Nombre del Proyecto

Descripción del proyecto.

## Instalación

```bash
git clone https://github.com/USUARIO/nombre-del-proyecto.git
```
EOF

# Crear .gitignore
cat > .gitignore << 'EOF'
# Sistema
.DS_Store
Thumbs.db

# IDEs
.vscode/
.idea/

# Logs
*.log

# Credenciales
.env
*.token
EOF
```

### Paso 3: Configurar Git Flow

**Ambos sistemas (Windows y Linux):**
```bash
# Renombrar rama a main (si es necesario)
git branch -M main

# Hacer commit inicial
git add .
git commit -m "chore: initial commit"

# Crear rama develop
git checkout -b develop

# Volver a main
git checkout main
```

### Paso 4: Conectar con GitHub

```bash
# Agregar remote (reemplazar USUARIO y REPO)
git remote add origin https://github.com/USUARIO/nombre-del-proyecto.git

# Verificar remote
git remote -v

# Push de main
git push -u origin main

# Push de develop
git push -u origin develop
```

### Paso 5: Configurar Rama por Defecto

1. Ir a: `https://github.com/USUARIO/nombre-del-proyecto/settings`
2. En **Default branch**, cambiar a `develop`
3. Confirmar el cambio

---

## Método 2: Con GitHub CLI (Si está instalado)

### Verificar GitHub CLI

```bash
gh --version
```

Si no está instalado: https://cli.github.com/

### Crear Repositorio Completo

**Ambos sistemas:**
```bash
# Autenticarse (solo primera vez)
gh auth login

# Crear repositorio y clonar
gh repo create nombre-del-proyecto --public --clone

# Entrar al directorio
cd nombre-del-proyecto

# Crear archivos básicos
echo "# Nombre del Proyecto" > README.md
echo ".DS_Store" > .gitignore
echo "Thumbs.db" >> .gitignore

# Configurar Git Flow
git add .
git commit -m "chore: initial commit"
git branch develop
git push -u origin main
git push -u origin develop
```

---

## Método 3: Con API de GitHub (Avanzado)

### Requisitos
- Token de acceso personal de GitHub
- Obtener en: https://github.com/settings/tokens

### Crear Repositorio via API

**Windows PowerShell:**
```powershell
$token = "TU_TOKEN_AQUI"
$headers = @{
    Authorization = "Bearer $token"
    Accept = "application/vnd.github.v3+json"
}

$body = @{
    name = "nombre-del-proyecto"
    description = "Descripción del proyecto"
    private = $false
    auto_init = $false
} | ConvertTo-Json

$repo = Invoke-RestMethod -Uri "https://api.github.com/user/repos" -Method Post -Headers $headers -Body $body -ContentType "application/json"

Write-Host "✅ Repositorio creado: $($repo.html_url)"
```

**Linux/Mac:**
```bash
TOKEN="TU_TOKEN_AQUI"

curl -X POST \
  -H "Authorization: Bearer $TOKEN" \
  -H "Accept: application/vnd.github.v3+json" \
  https://api.github.com/user/repos \
  -d '{
    "name": "nombre-del-proyecto",
    "description": "Descripción del proyecto",
    "private": false,
    "auto_init": false
  }'
```

Luego seguir **Paso 2, 3 y 4** del Método 1.

---

## Estructura Recomendada Git Flow

```
Repositorio
├── main (producción)
│   └── Tag: v1.0.0, v1.1.0, etc.
│
└── develop (desarrollo)
    ├── feature/nueva-funcionalidad
    ├── feature/otra-funcionalidad
    └── release/1.1.0
```

### Flujo de Trabajo

```bash
# 1. Crear feature desde develop
git checkout develop
git checkout -b feature/nombre-feature

# 2. Desarrollar
# ... hacer cambios ...
git add .
git commit -m "feat: descripción"

# 3. Push y crear PR
git push -u origin feature/nombre-feature
# Crear PR en GitHub: develop ← feature/nombre-feature

# 4. Después de aprobar PR, mergear y eliminar
git checkout develop
git pull origin develop
git branch -d feature/nombre-feature
```

---

## Comandos Útiles

### Verificar Estado
```bash
# Ver ramas
git branch -a

# Ver remote
git remote -v

# Ver último commit
git log -1

# Ver estado
git status
```

### Sincronizar
```bash
# Actualizar desde GitHub
git pull origin develop

# Subir cambios
git push origin develop
```

### Gestión de Ramas
```bash
# Crear rama
git checkout -b nombre-rama

# Cambiar de rama
git checkout nombre-rama

# Eliminar rama local
git branch -d nombre-rama

# Eliminar rama remota
git push origin --delete nombre-rama
```

---

## Checklist de Creación

### Antes de Empezar
- [ ] Tener cuenta de GitHub
- [ ] Git instalado y configurado
- [ ] Decidir nombre del repositorio
- [ ] Decidir visibilidad (público/privado)

### Creación
- [ ] Crear repositorio en GitHub
- [ ] Inicializar Git localmente
- [ ] Crear README.md
- [ ] Crear .gitignore
- [ ] Commit inicial
- [ ] Crear rama develop
- [ ] Conectar con GitHub (remote)
- [ ] Push de main y develop
- [ ] Configurar develop como rama por defecto

### Verificación
- [ ] Repositorio visible en GitHub
- [ ] README.md se muestra correctamente
- [ ] Ambas ramas (main y develop) existen
- [ ] Rama por defecto es develop

---

## Solución de Problemas Comunes

### Error: "gh: command not found"
**Causa**: GitHub CLI no instalado  
**Solución**: Usar Método 1 (manual) o instalar desde https://cli.github.com/

### Error: "Permission denied (publickey)"
**Causa**: SSH no configurado  
**Solución**: Usar HTTPS en lugar de SSH
```bash
# HTTPS (recomendado)
git remote add origin https://github.com/USUARIO/REPO.git

# En lugar de SSH
# git remote add origin git@github.com:USUARIO/REPO.git
```

### Error: "remote origin already exists"
**Solución**: Eliminar y volver a agregar
```bash
git remote remove origin
git remote add origin https://github.com/USUARIO/REPO.git
```

### Error: "failed to push some refs"
**Solución**: Pull primero, luego push
```bash
git pull origin main --allow-unrelated-histories
git push origin main
```

---

## Plantilla Rápida (Copy-Paste)

### Para Windows PowerShell

```powershell
# Variables (MODIFICAR ESTOS VALORES)
$repoName = "mi-proyecto"
$userName = "tu-usuario"
$description = "Descripción del proyecto"

# Crear estructura
mkdir $repoName
cd $repoName
git init

# Crear archivos
@"
# $repoName

$description
"@ | Out-File README.md -Encoding UTF8

".DS_Store`nThumbs.db`n.vscode/`n.idea/`n*.log`n.env" | Out-File .gitignore -Encoding UTF8

# Git Flow
git add .
git commit -m "chore: initial commit"
git branch -M main
git checkout -b develop
git checkout main

# Conectar (primero crear repo en GitHub)
git remote add origin "https://github.com/$userName/$repoName.git"
git push -u origin main
git push -u origin develop

Write-Host "`n✅ Repositorio creado y configurado!`n"
Write-Host "URL: https://github.com/$userName/$repoName"
```

### Para Linux/Mac

```bash
# Variables (MODIFICAR ESTOS VALORES)
REPO_NAME="mi-proyecto"
USER_NAME="tu-usuario"
DESCRIPTION="Descripción del proyecto"

# Crear estructura
mkdir $REPO_NAME
cd $REPO_NAME
git init

# Crear archivos
cat > README.md << EOF
# $REPO_NAME

$DESCRIPTION
EOF

cat > .gitignore << EOF
.DS_Store
Thumbs.db
.vscode/
.idea/
*.log
.env
EOF

# Git Flow
git add .
git commit -m "chore: initial commit"
git branch -M main
git checkout -b develop
git checkout main

# Conectar (primero crear repo en GitHub)
git remote add origin "https://github.com/$USER_NAME/$REPO_NAME.git"
git push -u origin main
git push -u origin develop

echo ""
echo "✅ Repositorio creado y configurado!"
echo "URL: https://github.com/$USER_NAME/$REPO_NAME"
```

---

## Mejores Prácticas

### Nombres de Repositorio
✅ **Usar**:
- Minúsculas
- Guiones para separar palabras
- Nombres descriptivos
- Ejemplos: `mi-proyecto`, `api-rest`, `web-app`

❌ **Evitar**:
- Espacios
- Caracteres especiales
- Nombres muy largos
- Ejemplos: `Mi Proyecto`, `API_REST`, `proyecto-super-mega-ultra-largo`

### Commits Iniciales
✅ **Usar**: `chore: initial commit` o `chore: initial project structure`  
❌ **Evitar**: `first commit`, `init`, `start`

### Estructura de Ramas
✅ **Usar**:
- `main` para producción
- `develop` para desarrollo
- `feature/nombre` para features

❌ **Evitar**:
- `master` (obsoleto, usar `main`)
- Trabajar directamente en `main`
- Nombres genéricos como `test`, `new`

---

## Resumen de Métodos

| Método | Complejidad | Requiere | Velocidad | Recomendado |
|--------|-------------|----------|-----------|-------------|
| **Manual** | Baja | Solo navegador | Media | ✅ Sí |
| **GitHub CLI** | Media | gh instalado | Rápida | Si está instalado |
| **API** | Alta | Token de acceso | Rápida | Para automatización |

---

## Recursos Adicionales

- **GitHub Docs**: https://docs.github.com/
- **Git Flow**: https://nvie.com/posts/a-successful-git-branching-model/
- **GitHub CLI**: https://cli.github.com/
- **Git Cheat Sheet**: https://education.github.com/git-cheat-sheet-education.pdf

---

**Última actualización**: Diciembre 2025

# Listado de Pull Requests en GitHub

## Objetivo
Conectarse a un repositorio de GitHub y obtener un listado completo de todos los Pull Requests (PRs) de un repositorio específico o de todos los repositorios del usuario.

---

## ⚠️ INSTRUCCIONES CRÍTICAS PARA LLMs/IA

### Detección del Sistema Operativo
**ANTES de ejecutar cualquier comando, DEBES identificar el sistema operativo del usuario:**

1. **Windows con PowerShell**: 
   - Usar comandos `Invoke-RestMethod`, `Invoke-WebRequest`
   - NO usar `curl` directamente (es un alias que NO funciona igual)
   - Usar `Out-File` en lugar de `>`

2. **Linux/Mac/Git Bash**: 
   - Usar comandos `curl`
   - Usar redirección `>` para archivos

### Reglas de Ejecución para IA

#### Para Listar Pull Requests de un Repositorio:

**SI el usuario está en Windows PowerShell, USAR:**
```powershell
# Listar PRs de un repositorio específico
Invoke-RestMethod -Uri "https://api.github.com/repos/USUARIO/REPOSITORIO/pulls?state=all&per_page=100" -Headers @{Accept="application/vnd.github.v3+json"}
```

**SI el usuario está en Linux/Mac/Bash, USAR:**
```bash
curl -H "Accept: application/vnd.github.v3+json" https://api.github.com/repos/USUARIO/REPOSITORIO/pulls?state=all&per_page=100
```

#### Para Guardar en Archivo (Windows PowerShell):
```powershell
# Paso 1: Obtener datos de PRs
$prs = Invoke-RestMethod -Uri "https://api.github.com/repos/USUARIO/REPOSITORIO/pulls?state=all&per_page=100" -Headers @{Accept="application/vnd.github.v3+json"}

# Paso 2: Guardar JSON
$prs | ConvertTo-Json -Depth 10 | Out-File -FilePath "pull-requests.json" -Encoding UTF8

# Paso 3: Crear Markdown formateado
$output = "# Pull Requests - USUARIO/REPOSITORIO`n`n"
$output += "**Total de PRs:** $($prs.Count)`n"
$output += "**Fecha de consulta:** $(Get-Date -Format 'yyyy-MM-dd HH:mm:ss')`n`n---`n`n"
foreach ($pr in $prs) {
    $output += "## PR #$($pr.number): $($pr.title)`n`n"
    $output += "- **Estado:** $($pr.state)`n"
    $output += "- **Autor:** $($pr.user.login)`n"
    $output += "- **Creado:** $($pr.created_at)`n"
    $output += "- **Actualizado:** $($pr.updated_at)`n"
    if ($pr.merged_at) {
        $output += "- **Mergeado:** $($pr.merged_at)`n"
    }
    $output += "- **Base:** $($pr.base.ref) ← **Head:** $($pr.head.ref)`n"
    $output += "- **URL:** $($pr.html_url)`n"
    $output += "- **Comentarios:** $($pr.comments)`n"
    $output += "- **Commits:** $($pr.commits)`n"
    $output += "- **Archivos cambiados:** $($pr.changed_files)`n"
    $output += "- **Adiciones:** +$($pr.additions) / **Eliminaciones:** -$($pr.deletions)`n`n"
    if ($pr.body) {
        $output += "**Descripción:**`n$($pr.body)`n`n"
    }
    $output += "---`n`n"
}
$output | Out-File -FilePath "pull-requests-USUARIO-REPOSITORIO.md" -Encoding UTF8
```

#### Para Guardar en Archivo (Linux/Mac/Bash):
```bash
# Paso 1: Obtener datos de PRs
curl -H "Accept: application/vnd.github.v3+json" \
  https://api.github.com/repos/USUARIO/REPOSITORIO/pulls?state=all&per_page=100 > pull-requests.json

# Paso 2: Procesar con jq (si está instalado)
cat pull-requests.json | jq -r '.[] | "## PR #\(.number): \(.title)\n- Estado: \(.state)\n- Autor: \(.user.login)\n- URL: \(.html_url)\n"' > pull-requests.md
```

### Proceso Automático Recomendado (Sin Pedir Permiso Múltiples Veces)

**Ejecutar en este orden sin pausas:**

1. **Detectar SO** (automático según el entorno)
2. **Obtener datos de GitHub** usando el comando correcto del SO
3. **Guardar JSON** para procesamiento
4. **Generar Markdown** formateado y legible
5. **Mostrar resumen** al usuario con tabla

**NO pedir permiso entre cada paso. Ejecutar todo de una vez.**

---

## Requisitos Previos

### 1. Verificar Git Instalado
```bash
git --version
```
**Resultado esperado**: Debe mostrar la versión de Git instalada (ej: `git version 2.x.x`)

### 2. Verificar Autenticación en GitHub
```bash
git config --global user.name
git config --global user.email
```
**Resultado esperado**: Debe mostrar el nombre de usuario y email configurados.

---

## Método 1: Usar GitHub CLI (Recomendado)

### Paso 1: Instalar GitHub CLI
Si no está instalado, descargar desde: https://cli.github.com/

Verificar instalación:
```bash
gh --version
```

### Paso 2: Autenticarse en GitHub
```bash
gh auth login
```
Seguir las instrucciones interactivas:
- Seleccionar: `GitHub.com`
- Seleccionar protocolo: `HTTPS` o `SSH`
- Autenticarse mediante navegador o token

### Paso 3: Listar Pull Requests

**Listar PRs de un repositorio específico:**
```bash
gh pr list --repo USUARIO/REPOSITORIO --limit 100
```

**Listar PRs del repositorio actual (si estás dentro del directorio):**
```bash
gh pr list --limit 100
```

**Listar PRs con estado específico:**
```bash
# Solo PRs abiertos
gh pr list --state open --limit 100

# Solo PRs cerrados
gh pr list --state closed --limit 100

# Solo PRs mergeados
gh pr list --state merged --limit 100

# Todos los PRs
gh pr list --state all --limit 100
```

### Paso 4: Exportar Listado a Archivo

**Formato tabla (texto):**
```bash
gh pr list --repo USUARIO/REPOSITORIO --limit 100 > pull-requests.txt
```

**Formato JSON detallado:**
```bash
gh pr list --repo USUARIO/REPOSITORIO --json number,title,state,author,createdAt,updatedAt,mergedAt,url,body,comments,commits --limit 100 > pull-requests.json
```

**Ver detalles de un PR específico:**
```bash
gh pr view NUMERO_PR --repo USUARIO/REPOSITORIO
```

---

## Método 2: Usar API de GitHub (PowerShell/cURL)

### Paso 1: Identificar tu Sistema Operativo
- **Windows PowerShell**: Usar comandos `Invoke-RestMethod`
- **Linux/Mac/Git Bash**: Usar comandos `curl`

### Paso 2: Listar Pull Requests Públicos (Sin Token)

**Para Windows PowerShell (RECOMENDADO PARA WINDOWS):**
```powershell
# Listar todos los PRs de un repositorio
$prs = Invoke-RestMethod -Uri "https://api.github.com/repos/USUARIO/REPOSITORIO/pulls?state=all&per_page=100" -Headers @{Accept="application/vnd.github.v3+json"}

# Ver resumen en tabla
$prs | Select-Object number, title, state, @{Name="author";Expression={$_.user.login}}, created_at | Format-Table -AutoSize
```

**Para Linux/Mac/Git Bash:**
```bash
curl -H "Accept: application/vnd.github.v3+json" \
  https://api.github.com/repos/USUARIO/REPOSITORIO/pulls?state=all&per_page=100
```

### Paso 3: Filtrar por Estado

**Para Windows PowerShell:**
```powershell
# Solo PRs abiertos
$prs = Invoke-RestMethod -Uri "https://api.github.com/repos/USUARIO/REPOSITORIO/pulls?state=open&per_page=100" -Headers @{Accept="application/vnd.github.v3+json"}

# Solo PRs cerrados
$prs = Invoke-RestMethod -Uri "https://api.github.com/repos/USUARIO/REPOSITORIO/pulls?state=closed&per_page=100" -Headers @{Accept="application/vnd.github.v3+json"}

# Todos los PRs
$prs = Invoke-RestMethod -Uri "https://api.github.com/repos/USUARIO/REPOSITORIO/pulls?state=all&per_page=100" -Headers @{Accept="application/vnd.github.v3+json"}
```

**Para Linux/Mac/Git Bash:**
```bash
# Solo PRs abiertos
curl -H "Accept: application/vnd.github.v3+json" \
  "https://api.github.com/repos/USUARIO/REPOSITORIO/pulls?state=open&per_page=100"

# Solo PRs cerrados
curl -H "Accept: application/vnd.github.v3+json" \
  "https://api.github.com/repos/USUARIO/REPOSITORIO/pulls?state=closed&per_page=100"

# Todos los PRs
curl -H "Accept: application/vnd.github.v3+json" \
  "https://api.github.com/repos/USUARIO/REPOSITORIO/pulls?state=all&per_page=100"
```

### Paso 4: Guardar Resultado en Archivo

**Para Windows PowerShell:**
```powershell
# Obtener PRs
$prs = Invoke-RestMethod -Uri "https://api.github.com/repos/USUARIO/REPOSITORIO/pulls?state=all&per_page=100" -Headers @{Accept="application/vnd.github.v3+json"}

# Guardar JSON completo
$prs | ConvertTo-Json -Depth 10 | Out-File -FilePath "pull-requests.json" -Encoding UTF8

# Crear Markdown formateado
$output = "# Pull Requests - USUARIO/REPOSITORIO`n`n"
$output += "**Total de PRs:** $($prs.Count)`n"
$output += "**Fecha:** $(Get-Date -Format 'yyyy-MM-dd HH:mm:ss')`n`n"

# Agrupar por estado
$abiertos = ($prs | Where-Object {$_.state -eq "open"}).Count
$cerrados = ($prs | Where-Object {$_.state -eq "closed"}).Count
$output += "- **Abiertos:** $abiertos`n"
$output += "- **Cerrados:** $cerrados`n`n---`n`n"

foreach ($pr in $prs) {
    $output += "## PR #$($pr.number): $($pr.title)`n`n"
    $output += "- **Estado:** $($pr.state)`n"
    $output += "- **Autor:** $($pr.user.login)`n"
    $output += "- **Creado:** $($pr.created_at)`n"
    $output += "- **URL:** $($pr.html_url)`n"
    $output += "- **Base:** $($pr.base.ref) ← **Head:** $($pr.head.ref)`n`n"
    $output += "---`n`n"
}
$output | Out-File -FilePath "pull-requests-USUARIO-REPOSITORIO.md" -Encoding UTF8
```

**Para Linux/Mac/Git Bash:**
```bash
# Guardar JSON
curl -H "Accept: application/vnd.github.v3+json" \
  https://api.github.com/repos/USUARIO/REPOSITORIO/pulls?state=all&per_page=100 > pull-requests.json

# Procesar con jq (si está instalado)
echo "# Pull Requests - USUARIO/REPOSITORIO" > pull-requests.md
echo "" >> pull-requests.md
cat pull-requests.json | jq -r '.[] | "## PR #\(.number): \(.title)\n- Estado: \(.state)\n- Autor: \(.user.login)\n- URL: \(.html_url)\n---\n"' >> pull-requests.md
```

### Paso 5: Listar PRs con Autenticación (Para Repositorios Privados)

**Obtener Token de Acceso Personal:**
1. Ir a: https://github.com/settings/tokens
2. Click en "Generate new token" → "Generate new token (classic)"
3. Seleccionar permisos: `repo` (acceso completo a repositorios)
4. Generar y copiar el token

**Para Windows PowerShell:**
```powershell
$token = "TU_TOKEN_AQUI"
$headers = @{
    Authorization = "token $token"
    Accept = "application/vnd.github.v3+json"
}
$prs = Invoke-RestMethod -Uri "https://api.github.com/repos/USUARIO/REPOSITORIO/pulls?state=all&per_page=100" -Headers $headers
```

**Para Linux/Mac/Git Bash:**
```bash
curl -H "Authorization: token TU_TOKEN" \
     -H "Accept: application/vnd.github.v3+json" \
     https://api.github.com/repos/USUARIO/REPOSITORIO/pulls?state=all&per_page=100
```

---

## Método 3: Listar PRs de Múltiples Repositorios

### Opción A: Usando GitHub CLI

**Script para Windows PowerShell:**
```powershell
# Obtener lista de repositorios
$repos = gh repo list USUARIO --limit 100 --json name --jq '.[].name'

# Crear archivo de salida
$output = "# Pull Requests de Todos los Repositorios - USUARIO`n`n"
$output += "**Fecha:** $(Get-Date -Format 'yyyy-MM-dd HH:mm:ss')`n`n---`n`n"

foreach ($repo in $repos) {
    Write-Host "Procesando: $repo"
    $prs = gh pr list --repo "USUARIO/$repo" --state all --limit 100 --json number,title,state,author,url 2>$null
    
    if ($prs) {
        $prsObj = $prs | ConvertFrom-Json
        if ($prsObj.Count -gt 0) {
            $output += "## Repositorio: $repo`n`n"
            $output += "**Total PRs:** $($prsObj.Count)`n`n"
            
            foreach ($pr in $prsObj) {
                $output += "- **PR #$($pr.number)**: $($pr.title) - Estado: $($pr.state) - [Ver]($($pr.url))`n"
            }
            $output += "`n---`n`n"
        }
    }
}

$output | Out-File -FilePath "todos-los-pull-requests.md" -Encoding UTF8
Write-Host "✅ Archivo generado: todos-los-pull-requests.md"
```

**Script para Linux/Mac/Bash:**
```bash
#!/bin/bash

echo "# Pull Requests de Todos los Repositorios - USUARIO" > todos-los-pull-requests.md
echo "" >> todos-los-pull-requests.md
echo "**Fecha:** $(date '+%Y-%m-%d %H:%M:%S')" >> todos-los-pull-requests.md
echo "" >> todos-los-pull-requests.md
echo "---" >> todos-los-pull-requests.md
echo "" >> todos-los-pull-requests.md

# Obtener lista de repositorios
repos=$(gh repo list USUARIO --limit 100 --json name --jq '.[].name')

for repo in $repos; do
    echo "Procesando: $repo"
    prs=$(gh pr list --repo "USUARIO/$repo" --state all --limit 100 --json number,title,state,url 2>/dev/null)
    
    if [ ! -z "$prs" ] && [ "$prs" != "[]" ]; then
        echo "## Repositorio: $repo" >> todos-los-pull-requests.md
        echo "" >> todos-los-pull-requests.md
        echo "$prs" | jq -r '.[] | "- **PR #\(.number)**: \(.title) - Estado: \(.state) - [Ver](\(.url))"' >> todos-los-pull-requests.md
        echo "" >> todos-los-pull-requests.md
        echo "---" >> todos-los-pull-requests.md
        echo "" >> todos-los-pull-requests.md
    fi
done

echo "✅ Archivo generado: todos-los-pull-requests.md"
```

---

## Formato de Salida Esperado

### Información Básica por Pull Request
- **Número del PR**
- **Título**
- **Estado** (open, closed, merged)
- **Autor**
- **Fecha de creación**
- **Fecha de actualización**
- **Fecha de merge** (si aplica)
- **Branch base y head**
- **URL del PR**
- **Número de comentarios**
- **Número de commits**
- **Archivos cambiados**
- **Líneas añadidas/eliminadas**
- **Descripción/Body**

### Ejemplo de Salida JSON
```json
[
  {
    "number": 42,
    "title": "Add new feature",
    "state": "open",
    "user": {
      "login": "username"
    },
    "created_at": "2025-12-01T10:00:00Z",
    "updated_at": "2025-12-15T09:30:00Z",
    "merged_at": null,
    "base": {
      "ref": "main"
    },
    "head": {
      "ref": "feature-branch"
    },
    "html_url": "https://github.com/user/repo/pull/42",
    "comments": 5,
    "commits": 3,
    "changed_files": 7,
    "additions": 150,
    "deletions": 30
  }
]
```

---

## Comandos Útiles Adicionales

### Filtrar PRs por Autor

**GitHub CLI:**
```bash
gh pr list --author NOMBRE_USUARIO --limit 100
```

**API con PowerShell:**
```powershell
$prs = Invoke-RestMethod -Uri "https://api.github.com/repos/USUARIO/REPOSITORIO/pulls?state=all&per_page=100" -Headers @{Accept="application/vnd.github.v3+json"}
$prs | Where-Object {$_.user.login -eq "NOMBRE_USUARIO"}
```

### Filtrar PRs por Label

**GitHub CLI:**
```bash
gh pr list --label "bug" --limit 100
gh pr list --label "enhancement" --limit 100
```

### Ordenar PRs

**GitHub CLI:**
```bash
# Por fecha de creación (más recientes primero)
gh pr list --json number,title,createdAt --jq 'sort_by(.createdAt) | reverse'

# Por fecha de actualización
gh pr list --json number,title,updatedAt --jq 'sort_by(.updatedAt) | reverse'
```

**PowerShell:**
```powershell
# Ordenar por fecha de creación
$prs | Sort-Object created_at -Descending

# Ordenar por número de comentarios
$prs | Sort-Object comments -Descending
```

### Contar PRs por Estado

**PowerShell:**
```powershell
$prs = Invoke-RestMethod -Uri "https://api.github.com/repos/USUARIO/REPOSITORIO/pulls?state=all&per_page=100" -Headers @{Accept="application/vnd.github.v3+json"}

Write-Host "Abiertos: $(($prs | Where-Object {$_.state -eq 'open'}).Count)"
Write-Host "Cerrados: $(($prs | Where-Object {$_.state -eq 'closed'}).Count)"
Write-Host "Total: $($prs.Count)"
```

**Linux/Mac:**
```bash
# Con GitHub CLI
echo "Abiertos: $(gh pr list --state open --limit 1000 | wc -l)"
echo "Cerrados: $(gh pr list --state closed --limit 1000 | wc -l)"
```

---

## Solución de Problemas

### Error: "gh: command not found"
**Solución**: Instalar GitHub CLI desde https://cli.github.com/

### Error: "authentication required"
**Solución**: Ejecutar `gh auth login` y completar el proceso de autenticación

### Error: "API rate limit exceeded"
**Solución**: 
- Autenticarse con token de acceso personal
- Esperar 1 hora para que se restablezca el límite
- Usar autenticación para aumentar el límite de 60 a 5000 peticiones/hora

### Error: "Not Found" o "404"
**Solución**: 
- Verificar que el nombre del repositorio sea correcto
- Verificar que tengas permisos para acceder al repositorio
- Para repositorios privados, usar autenticación con token

### Límite de 100 PRs
**Solución**: Aumentar el parámetro `per_page` o usar paginación:
```powershell
# PowerShell - Obtener hasta 300 PRs con paginación
$allPrs = @()
for ($page = 1; $page -le 3; $page++) {
    $prs = Invoke-RestMethod -Uri "https://api.github.com/repos/USUARIO/REPOSITORIO/pulls?state=all&per_page=100&page=$page" -Headers @{Accept="application/vnd.github.v3+json"}
    $allPrs += $prs
    if ($prs.Count -lt 100) { break }
}
```

---

## Resumen de Comandos Principales

| Acción | Comando (GitHub CLI) | Comando (API - PowerShell) |
|--------|---------------------|---------------------------|
| Listar PRs abiertos | `gh pr list --state open` | `Invoke-RestMethod -Uri "...pulls?state=open..."` |
| Listar todos los PRs | `gh pr list --state all` | `Invoke-RestMethod -Uri "...pulls?state=all..."` |
| Exportar a JSON | `gh pr list --json ... > prs.json` | `$prs \| ConvertTo-Json > prs.json` |
| Ver detalles de PR | `gh pr view NUMERO` | `Invoke-RestMethod -Uri ".../pulls/NUMERO"` |
| Filtrar por autor | `gh pr list --author USER` | `$prs \| Where-Object {$_.user.login -eq "USER"}` |
| Verificar autenticación | `gh auth status` | N/A |

---

## Notas Importantes

1. **Límites de API**: GitHub tiene límites de peticiones (rate limits). Con autenticación: 5000/hora, sin autenticación: 60/hora.

2. **Repositorios Privados**: Requieren autenticación y permisos adecuados.

3. **Paginación**: La API de GitHub devuelve máximo 100 items por página. Para más resultados, usar paginación.

4. **Estados de PR**: 
   - `open`: PR abierto
   - `closed`: PR cerrado (sin mergear)
   - Para PRs mergeados, verificar el campo `merged_at` (no null = mergeado)

5. **Seguridad**: Nunca compartas tu token de acceso personal. Guárdalo de forma segura.

6. **Formato de Datos**: JSON es el formato más útil para procesamiento automatizado por LLMs o scripts.

7. **Draft PRs**: Los PRs en estado "draft" también aparecen con `state: open`, pero tienen el campo `draft: true`.

---

## Ejemplos Prácticos

### Ejemplo 1: Obtener PRs de un repositorio específico

**Windows PowerShell:**
```powershell
$prs = Invoke-RestMethod -Uri "https://api.github.com/repos/facebook/react/pulls?state=all&per_page=10" -Headers @{Accept="application/vnd.github.v3+json"}
$prs | Select-Object number, title, state, @{Name="author";Expression={$_.user.login}} | Format-Table -AutoSize
```

**Linux/Mac:**
```bash
gh pr list --repo facebook/react --state all --limit 10
```

### Ejemplo 2: Buscar PRs de un autor específico

**Windows PowerShell:**
```powershell
$prs = Invoke-RestMethod -Uri "https://api.github.com/repos/USUARIO/REPOSITORIO/pulls?state=all&per_page=100" -Headers @{Accept="application/vnd.github.v3+json"}
$misPrs = $prs | Where-Object {$_.user.login -eq "MI_USUARIO"}
Write-Host "Encontrados: $($misPrs.Count) PRs"
$misPrs | Select-Object number, title, state | Format-Table -AutoSize
```

### Ejemplo 3: Generar reporte de PRs del último mes

**Windows PowerShell:**
```powershell
$prs = Invoke-RestMethod -Uri "https://api.github.com/repos/USUARIO/REPOSITORIO/pulls?state=all&per_page=100" -Headers @{Accept="application/vnd.github.v3+json"}
$fechaLimite = (Get-Date).AddDays(-30)
$prsRecientes = $prs | Where-Object {[DateTime]$_.created_at -gt $fechaLimite}

$output = "# PRs del Último Mes`n`n"
$output += "**Total:** $($prsRecientes.Count)`n`n"
foreach ($pr in $prsRecientes) {
    $output += "- PR #$($pr.number): $($pr.title) - $($pr.state)`n"
}
$output | Out-File -FilePath "prs-ultimo-mes.md" -Encoding UTF8
```

---

## Recursos Adicionales

- **Documentación API de GitHub**: https://docs.github.com/en/rest/pulls/pulls
- **GitHub CLI Manual**: https://cli.github.com/manual/gh_pr_list
- **Tokens de Acceso**: https://github.com/settings/tokens
- **Rate Limits**: https://docs.github.com/en/rest/overview/resources-in-the-rest-api#rate-limiting

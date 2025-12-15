# Listado de Proyectos en GitHub

## Objetivo
Conectarse a un repositorio de GitHub y obtener un listado completo de todos los proyectos (repositorios) del usuario.

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

#### Para Listar Repositorios Públicos:

**SI el usuario está en Windows PowerShell, USAR:**
```powershell
Invoke-RestMethod -Uri "https://api.github.com/users/NOMBRE_USUARIO/repos?per_page=100" -Headers @{Accept="application/vnd.github.v3+json"}
```

**SI el usuario está en Linux/Mac/Bash, USAR:**
```bash
curl -H "Accept: application/vnd.github.v3+json" https://api.github.com/users/NOMBRE_USUARIO/repos?per_page=100
```

#### Para Guardar en Archivo (Windows PowerShell):
```powershell
# Paso 1: Obtener datos
$repos = Invoke-RestMethod -Uri "https://api.github.com/users/NOMBRE_USUARIO/repos?per_page=100" -Headers @{Accept="application/vnd.github.v3+json"}

# Paso 2: Guardar JSON
$repos | ConvertTo-Json -Depth 10 | Out-File -FilePath "repositorios.json" -Encoding UTF8

# Paso 3: Crear Markdown formateado
$output = "# Listado de Repositorios - NOMBRE_USUARIO`n`n"
$output += "**Total de repositorios:** $($repos.Count)`n"
$output += "**Fecha de consulta:** $(Get-Date -Format 'yyyy-MM-dd HH:mm:ss')`n`n---`n`n"
foreach ($repo in $repos) {
    $output += "## $($repo.name)`n`n"
    $output += "- **Descripción:** $($repo.description)`n"
    $output += "- **URL:** $($repo.html_url)`n"
    $output += "- **Lenguaje:** $($repo.language)`n"
    $output += "- **Visibilidad:** $($repo.visibility)`n"
    $output += "- **Estrellas:** $($repo.stargazers_count)`n"
    $output += "- **Forks:** $($repo.forks_count)`n"
    $output += "- **Última actualización:** $($repo.updated_at)`n"
    $output += "- **URL de clonación (HTTPS):** $($repo.clone_url)`n"
    $output += "- **URL de clonación (SSH):** $($repo.ssh_url)`n`n---`n`n"
}
$output | Out-File -FilePath "listado-repositorios-NOMBRE_USUARIO.md" -Encoding UTF8
```

### Proceso Automático Recomendado (Sin Pedir Permiso 4 Veces)

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
Ejecutar el siguiente comando para verificar que Git está instalado:
```bash
git --version
```
**Resultado esperado**: Debe mostrar la versión de Git instalada (ej: `git version 2.x.x`)

### 2. Verificar Autenticación en GitHub
Ejecutar el siguiente comando para verificar la configuración de usuario:
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
Ejecutar el comando de autenticación:
```bash
gh auth login
```
Seguir las instrucciones interactivas:
- Seleccionar: `GitHub.com`
- Seleccionar protocolo: `HTTPS` o `SSH`
- Autenticarse mediante navegador o token

### Paso 3: Listar Todos los Repositorios
Para listar repositorios del usuario autenticado:
```bash
gh repo list --limit 100
```

Para listar repositorios de un usuario específico:
```bash
gh repo list NOMBRE_USUARIO --limit 100
```

### Paso 4: Exportar Listado a Archivo
Para guardar el listado en un archivo de texto:
```bash
gh repo list --limit 100 > listado-repositorios.txt
```

Para obtener información detallada en formato JSON:
```bash
gh repo list --json name,description,url,updatedAt,isPrivate --limit 100 > repositorios.json
```

---

## Método 2: Usar API de GitHub (PowerShell/cURL)

### Paso 1: Identificar tu Sistema Operativo
- **Windows PowerShell**: Usar comandos `Invoke-RestMethod`
- **Linux/Mac/Git Bash**: Usar comandos `curl`

### Paso 2: Listar Repositorios Públicos (Sin Token)

**Para Windows PowerShell (RECOMENDADO PARA WINDOWS):**
```powershell
# Listar repositorios de un usuario específico
Invoke-RestMethod -Uri "https://api.github.com/users/NOMBRE_USUARIO/repos?per_page=100" -Headers @{Accept="application/vnd.github.v3+json"}
```

**Para Linux/Mac/Git Bash:**
```bash
curl -H "Accept: application/vnd.github.v3+json" https://api.github.com/users/NOMBRE_USUARIO/repos?per_page=100
```

### Paso 3: Guardar Resultado en Archivo

**Para Windows PowerShell:**
```powershell
# Guardar en formato JSON
$repos = Invoke-RestMethod -Uri "https://api.github.com/users/NOMBRE_USUARIO/repos?per_page=100" -Headers @{Accept="application/vnd.github.v3+json"}
$repos | ConvertTo-Json -Depth 10 | Out-File -FilePath "repositorios.json" -Encoding UTF8

# Guardar en formato Markdown con información detallada
$output = "# Listado de Repositorios - NOMBRE_USUARIO`n`n"
$output += "**Total:** $($repos.Count)`n`n"
foreach ($repo in $repos) {
    $output += "## $($repo.name)`n"
    $output += "- **Descripción:** $($repo.description)`n"
    $output += "- **URL:** $($repo.html_url)`n"
    $output += "- **Lenguaje:** $($repo.language)`n"
    $output += "- **Clonación HTTPS:** $($repo.clone_url)`n`n"
}
$output | Out-File -FilePath "listado-repositorios.md" -Encoding UTF8
```

**Para Linux/Mac/Git Bash:**
```bash
curl -H "Accept: application/vnd.github.v3+json" https://api.github.com/users/NOMBRE_USUARIO/repos?per_page=100 > repositorios.json
```

### Paso 4: Listar Repositorios Privados (Con Token)

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
Invoke-RestMethod -Uri "https://api.github.com/user/repos?per_page=100" -Headers $headers
```

**Para Linux/Mac/Git Bash:**
```bash
curl -H "Authorization: token TU_TOKEN" -H "Accept: application/vnd.github.v3+json" https://api.github.com/user/repos?per_page=100
```

---

## Método 3: Usar Git para Clonar Todos los Repositorios

### Paso 1: Obtener Lista de URLs
Usar GitHub CLI para obtener solo las URLs:
```bash
gh repo list --json url --jq '.[].url' > urls-repositorios.txt
```

### Paso 2: Crear Script de Clonación
Crear un archivo `clonar-todos.sh` (Linux/Mac) o `clonar-todos.bat` (Windows):

**Para Linux/Mac:**
```bash
#!/bin/bash
while read repo; do
  git clone "$repo"
done < urls-repositorios.txt
```

**Para Windows (PowerShell):**
```powershell
Get-Content urls-repositorios.txt | ForEach-Object {
  git clone $_
}
```

### Paso 3: Ejecutar Script
**Linux/Mac:**
```bash
chmod +x clonar-todos.sh
./clonar-todos.sh
```

**Windows (PowerShell):**
```powershell
.\clonar-todos.bat
```

---

## Formato de Salida Esperado

### Información Básica por Repositorio
- **Nombre del repositorio**
- **Descripción**
- **URL de clonación**
- **Fecha de última actualización**
- **Visibilidad** (público/privado)
- **Lenguaje principal**

### Ejemplo de Salida JSON
```json
[
  {
    "name": "proyecto-ejemplo",
    "description": "Descripción del proyecto",
    "url": "https://github.com/usuario/proyecto-ejemplo",
    "updatedAt": "2025-12-15T10:00:00Z",
    "isPrivate": false,
    "primaryLanguage": "JavaScript"
  }
]
```

---

## Comandos Útiles Adicionales

### Filtrar por Lenguaje
```bash
gh repo list --language javascript --limit 100
```

### Filtrar por Visibilidad
```bash
gh repo list --public --limit 100
gh repo list --private --limit 100
```

### Ordenar por Fecha de Actualización
```bash
gh repo list --json name,updatedAt --jq 'sort_by(.updatedAt) | reverse'
```

### Contar Total de Repositorios

**PowerShell:**
```powershell
(gh repo list --limit 1000).Count
```

**Linux/Mac:**
```bash
gh repo list --limit 1000 | wc -l
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

### Límite de 100 Repositorios
**Solución**: Aumentar el parámetro `--limit`:
```bash
gh repo list --limit 1000
```

---

## Resumen de Comandos Principales

| Acción | Comando |
|--------|---------|
| Listar repositorios | `gh repo list --limit 100` |
| Exportar a JSON | `gh repo list --json name,url --limit 100 > repos.json` |
| Listar de otro usuario | `gh repo list USUARIO --limit 100` |
| Clonar todos | Usar script con URLs extraídas |
| Verificar autenticación | `gh auth status` |

---

## Notas Importantes

1. **Límites de API**: GitHub tiene límites de peticiones (rate limits). Con autenticación: 5000/hora, sin autenticación: 60/hora.

2. **Repositorios Privados**: Requieren autenticación y permisos adecuados.

3. **Paginación**: Si tienes más de 100 repositorios, ajusta el parámetro `--limit` o implementa paginación.

4. **Seguridad**: Nunca compartas tu token de acceso personal. Guárdalo de forma segura.

5. **Formato de Datos**: JSON es el formato más útil para procesamiento automatizado por LLMs o scripts.

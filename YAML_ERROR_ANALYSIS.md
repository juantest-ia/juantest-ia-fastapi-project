# Análisis del Error de Sintaxis YAML en Workflow de GitHub Actions

## Resumen Ejecutivo

Se identificó y corrigió un error de **indentación YAML** en un workflow de GitHub Actions. El error ocurría en la línea 18 del archivo `.github/workflows/deploy.yml`, donde la propiedad `run:` no tenía la indentación correcta.

---

## 1. ¿Dónde está exactamente el error de sintaxis?

El error está en **la línea 18**, en el paso "Build project":

```yaml
      - name: Build project
      run: npm run build  ← AQUÍ ESTÁ EL ERROR
```

### Localización Específica
- **Archivo**: `.github/workflows/deploy.yml`
- **Línea**: 18
- **Paso**: Build project
- **Propiedad afectada**: `run`

---

## 2. ¿Por qué es un error YAML?

El error es de **indentación incorrecta**. En YAML, la indentación no es solo una convención estética; es **sintácticamente significativa** y determina la estructura jerárquica de los datos.

### Análisis Detallado

En el archivo problemático:
- `- name: Build project` está indentado con **6 espacios** (nivel del array de steps)
- `run: npm run build` está indentado con **6 espacios** también, pero debería estar con **8 espacios**

Cuando `run` tiene la misma indentación que `name`, YAML interpreta que son dos elementos separados del array, no una pareja clave-valor del mismo objeto.

### Impacto

```yaml
# INTERPRETACIÓN INCORRECTA:
- name: Build project
- run: npm run build
# YAML ve dos pasos diferentes, no un paso con dos propiedades
```

---

## 3. ¿Cuál es la regla de indentación que se viola?

### Regla General de Indentación en YAML

```
- Primera línea del objeto (con -): N espacios
  Propiedades anidadas del objeto: N + 2 espacios
    Propiedades más anidadas: N + 4 espacios
```

### Aplicado al Workflow de GitHub Actions

```yaml
steps:                                    ← 0 espacios (raíz)
  - name: Install dependencies            ← 2 + 4 = 6 espacios (elemento del array)
    run: npm install                      ← 6 + 2 = 8 espacios (propiedad del objeto) ✓

  - name: Build project                   ← 6 espacios (elemento del array)
    run: npm run build                    ← 6 + 2 = 8 espacios (DEBE SER AQUÍ) ❌ TENÍA 6
```

### Jerarquía Correcta

```
jobs:                          (raíz)
  build:                       (2 espacios)
    runs-on:                   (4 espacios)
    steps:                     (4 espacios)
      - uses:                  (6 espacios - primer elemento del array)
        with:                  (8 espacios - propiedad del objeto)
      - name:                  (6 espacios - elemento del array)
        run:                   (8 espacios - propiedad del objeto)
```

---

## 4. Archivo YAML Completo y Corregido

```yaml
name: Deploy Pipeline

on:
  push:
    branches: [main]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Install dependencies
        run: npm install
      - name: Build project
        run: npm run build
      - name: Deploy
        run: npm run deploy
```

---

## Comparación: Antes vs. Después

### ❌ INCORRECTO (Antes)

```yaml
      - name: Build project
      run: npm run build    ← 6 espacios (INCORRECTO)
```

### ✅ CORRECTO (Después)

```yaml
      - name: Build project
        run: npm run build  ← 8 espacios (CORRECTO)
```

---

## Tabla Resumen del Error

| Aspecto | Detalle |
|---------|---------|
| **Archivo** | `.github/workflows/deploy.yml` |
| **Línea del error** | 18 |
| **Tipo de error** | Indentación de mapeo YAML |
| **Problema específico** | `run:` tiene 6 espacios en lugar de 8 |
| **Solución** | Agregar 2 espacios más antes de `run:` |
| **Causa raíz** | Los identificadores `name:` y `run:` quedaban al mismo nivel, rompiendo la estructura clave-valor |
| **Severidad** | CRÍTICA (invalida todo el workflow) |
| **Impacto** | El workflow no se ejecuta hasta corregir la indentación |

---

## Errores Comunes de Indentación en GitHub Actions

### 1. Propiedades de Step mal indentadas

```yaml
# ❌ INCORRECTO
  - name: My Step
  run: echo "hello"

# ✅ CORRECTO
  - name: My Step
    run: echo "hello"
```

### 2. Anidamiento en `with`

```yaml
# ❌ INCORRECTO
  - uses: actions/setup-node@v3
  with:
    node-version: 18

# ✅ CORRECTO
  - uses: actions/setup-node@v3
    with:
      node-version: 18
```

### 3. Arrays en `branches`

```yaml
# ❌ INCORRECTO
on:
  push:
  branches: [main]

# ✅ CORRECTO
on:
  push:
    branches: [main]
```

---

## Cómo Evitar Errores de Indentación

### 1. **Usa un Editor con Validación YAML**
   - VSCode con extensión "YAML"
   - JetBrains IDEs (IntelliJ, WebStorm)
   - Sublime Text con plugins YAML

### 2. **Configura EditorConfig**

```ini
[*.yml]
indent_style = space
indent_size = 2
```

### 3. **Valida Localmente**

```bash
# Instalar yamllint
pip install yamllint

# Validar archivo
yamllint .github/workflows/deploy.yml
```

### 4. **Usa GitHub Web Editor**
- GitHub proporciona vista previa de validación en el editor web

### 5. **Regla Nemotécnica**
- **Cada línea con `-` = nivel de array**
- **Propiedades bajo `-` = 2 espacios más de indentación**

---

## Referencias

- [GitHub Actions Workflow Syntax](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions)
- [YAML Specification](https://yaml.org/spec/1.2/spec.html)
- [yamllint Documentation](https://yamllint.readthedocs.io/)

---

## Conclusión

Este error es un ejemplo clásico de cómo la **indentación en YAML es crítica**. La diferencia entre 6 y 8 espacios invalidó completamente el workflow. La solución es simple: **asegurar que cada propiedad dentro de un objeto tiene exactamente 2 espacios más de indentación que el guion (`-`) que marca el inicio del objeto en el array**.

**Documento generado**: 2026-06-03
**Autor**: GitHub Copilot
**Repositorio**: juantest-ia/juantest-ia-fastapi-project

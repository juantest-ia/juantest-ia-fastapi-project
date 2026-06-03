# Guía Rápida de Indentación YAML en GitHub Actions

## El Error y La Solución

### ❌ INCORRECTO
```yaml
      - name: Build project
      run: npm run build    # 6 espacios (MALO)
```

### ✅ CORRECTO
```yaml
      - name: Build project
        run: npm run build  # 8 espacios (CORRECTO)
```

## Regla de Oro

**Cada propiedad dentro de un objeto del array debe tener exactamente 2 espacios más de indentación que el guion (`-`)**

```
jobs:                        (raíz)
  build:                     (2 espacios)
    runs-on: ubuntu-latest   (4 espacios)
    steps:                   (4 espacios)
      - uses: actions/...    (6 espacios + -)
        with:                (8 espacios - propiedades)
      - name: Install        (6 espacios + -)
        run: npm install     (8 espacios - propiedades)
```

## Validar Localmente

```bash
# Instalar yamllint
pip install yamllint

# Validar el archivo
yamllint deploy.yml
```

## Referencias

- [GitHub Actions Workflow Syntax](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions)
- [YAML Official Specification](https://yaml.org/spec/1.2/spec.html)

---

**Generado por**: GitHub Copilot  
**Fecha**: 2026-06-03  
**Repositorio**: juantest-ia/juantest-ia-fastapi-project

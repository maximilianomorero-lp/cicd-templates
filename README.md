# cicd-templates

Templates reutilizables de GitHub Actions para CI/CD.

## Workflows disponibles

| Workflow | Trigger | Descripción |
|----------|---------|-------------|
| `ci.yml` | `workflow_call` | Build de imagen de test y ejecución de tests |
| `build.yml` | `workflow_call` | Build y push de imagen Docker a ECR |
| `deploy.yml` | `workflow_call` | Deploy a ECS con notificación Slack |
| `claude-pr-review.yml` | `workflow_call` | Revisión automática de PRs con Claude AI |

---

## Claude PR Review

Analiza automáticamente el diff de cada Pull Request con Claude y deja un comentario con sugerencias de mejora.

### Configuración en tu repo

**1. Agrega el secret `ANTHROPIC_API_KEY` en tu repositorio**

Settings → Secrets and variables → Actions → New repository secret

**2. Crea el archivo `.github/workflows/pr-review.yml` en tu repo:**

```yaml
name: PR Review

on:
  pull_request:
    types: [opened, synchronize, reopened]

jobs:
  claude-review:
    uses: TU_ORG/cicd-templates/.github/workflows/claude-pr-review.yml@main
    with:
      pr_number: ${{ github.event.pull_request.number }}
      base_ref: ${{ github.base_ref }}
      language: 'es'          # 'es' para español, 'en' para inglés
    secrets:
      ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
```

Reemplaza `TU_ORG` con el nombre de tu organización o usuario de GitHub.

### Inputs

| Input | Requerido | Default | Descripción |
|-------|-----------|---------|-------------|
| `pr_number` | ✅ | — | Número del Pull Request |
| `base_ref` | ✅ | — | Rama base del PR (ej: `main`, `develop`) |
| `language` | ❌ | `es` | Idioma de la revisión: `es` o `en` |

### Secrets

| Secret | Requerido | Descripción |
|--------|-----------|-------------|
| `ANTHROPIC_API_KEY` | ✅ | API key de Anthropic |

### Permisos requeridos

El workflow necesita permiso para escribir en PRs. Si tu organización tiene permisos restrictivos, agrega esto en el workflow del repo llamador:

```yaml
permissions:
  pull-requests: write
  contents: read
```

### Qué revisa Claude

- 🐛 Bugs potenciales o errores lógicos
- 🔒 Problemas de seguridad
- ⚡ Problemas de rendimiento
- 📐 Calidad del código y mejores prácticas
- 📖 Legibilidad y mantenibilidad
- 🧪 Cobertura de casos extremos

---

## CI

Ejecuta tests dentro de un contenedor Docker.

```yaml
jobs:
  ci:
    uses: TU_ORG/cicd-templates/.github/workflows/ci.yml@main
    with:
      service_name: my-service
```

---

## Build

Build y push de imagen Docker a Amazon ECR.

```yaml
jobs:
  build:
    uses: TU_ORG/cicd-templates/.github/workflows/build.yml@main
    with:
      service_name: my-service
    secrets:
      AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
      AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
      AWS_ACCOUNT_ID: ${{ secrets.AWS_ACCOUNT_ID }}
```

---

## Deploy

Deploy a Amazon ECS con notificación a Slack.

```yaml
jobs:
  deploy:
    uses: TU_ORG/cicd-templates/.github/workflows/deploy.yml@main
    with:
      service_name: my-service
      environment: stage
      image_tag: main-a1b2c3d
    secrets:
      AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
      AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
      SLACK_WEBHOOK_URL: ${{ secrets.SLACK_WEBHOOK_URL }}
```

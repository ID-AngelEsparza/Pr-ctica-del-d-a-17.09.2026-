# Practica DevSecOps con OpenTofu y GitHub Actions

Este repositorio simula infraestructura ligera con OpenTofu usando los proveedores `random` y `local`. La practica permite demostrar trabajo colaborativo por roles, deteccion de una vulnerabilidad IaC, correccion mediante Pull Request y automatizacion con GitHub Actions usando secretos.

## Recursos simulados

- `random_pet.project_name`: genera un nombre aleatorio para el proyecto.
- `random_password.api_token`: genera un token sensible.
- `local_sensitive_file.secret_summary`: crea un archivo local sensible dentro de `generated/`.

## Flujo de trabajo sugerido

### Rol A: commit inicial vulnerable

1. Crear el repositorio publico en GitHub.
2. Agregar al Rol B como colaborador con permisos de escritura.
3. Copiar el contenido de `vulnerable-main.tf.example` a `main.tf`.
4. Crear el primer commit y subirlo a `main`.

Comandos sugeridos:

```bash
git init
git add main.tf .gitignore README.md
git commit -m "feat: add initial OpenTofu configuration"
git branch -M main
git remote add origin https://github.com/USUARIO/NOMBRE_REPO.git
git push -u origin main
```

### Rol B: auditoria y Pull Request

1. Crear una rama independiente.
2. Ejecutar analisis estatico con Checkov incluyendo escaneo de secretos.
3. Capturar la vulnerabilidad en `main.tf` donde `db_password` tiene un valor por defecto en texto plano.
4. Reemplazar `main.tf` por la version segura incluida actualmente en este repositorio, sin `default` para `db_password`.
5. Subir la rama y abrir un Pull Request hacia `main`.

Comandos sugeridos:

```bash
git checkout -b fix/remove-plaintext-secret
checkov -d . --framework terraform,secrets
git add main.tf
git commit -m "fix: remove plaintext secret from variable"
git push -u origin fix/remove-plaintext-secret
```

### Equipo: secretos y pipeline

1. Ir a `Settings > Secrets and variables > Actions > New repository secret`.
2. Crear el secreto `TF_VAR_DB_PASSWORD` con un valor de prueba, por ejemplo `ClaveSegura-2026!`.
3. Verificar que el workflow `OpenTofu Validate and Plan` se ejecute correctamente.

## Ejecucion local

Instalar OpenTofu y ejecutar:

```bash
tofu fmt -check
tofu init
tofu validate
tofu plan -var="db_password=ClaveLocalSegura-2026!"
```

## Analisis estatico local

Instalar Checkov:

```bash
pip install checkov
```

Ejecutar:

```bash
checkov -d . --framework terraform,secrets
```

## Evidencias para entregar

- Enlace del repositorio de GitHub.
- Captura del historial de commits, ramas y Pull Request.
- Captura de Checkov detectando el secreto en la version vulnerable.
- Captura del secreto `TF_VAR_DB_PASSWORD` configurado en GitHub Actions.
- Captura del workflow ejecutado en verde.

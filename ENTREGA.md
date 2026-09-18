# Documento de Entrega

## 1. Repositorio

- URL del repositorio: `PEGAR_URL_AQUI`
- Integrantes:
  - Rol A, Arquitecto IaC: `NOMBRE_AQUI`
  - Rol B, Ingeniero DevSecOps: `NOMBRE_AQUI`

## 2. Trabajo colaborativo

- Rama principal: `main`
- Rama de correccion: `fix/remove-plaintext-secret`
- Pull Request: `PEGAR_URL_DEL_PR_AQUI`

## 3. Descripcion del proyecto IaC

El proyecto usa OpenTofu para simular recursos ligeros mediante los proveedores `random` y `local`. Se genera un nombre aleatorio de proyecto, un token sensible y un archivo local marcado como sensible.

## 4. Vulnerabilidad inicial

La version inicial contenia una variable sensible con valor por defecto en texto plano:

```hcl
variable "db_password" {
  description = "Contrasena sensible usada por la infraestructura simulada."
  type        = string
  sensitive   = true
  default     = "ghp_fakeTokenForClassActivity1234567890abcdef"
}
```

Evidencia: insertar captura de Checkov detectando la vulnerabilidad con el comando `checkov -d . --framework terraform,secrets`.

## 5. Correccion aplicada

Se removio el valor `default` de la variable sensible y ahora el valor se recibe de forma segura mediante `TF_VAR_db_password`.

```hcl
variable "db_password" {
  description = "Contrasena sensible consumida desde GitHub Actions como TF_VAR_db_password."
  type        = string
  sensitive   = true
}
```

## 6. Secretos en GitHub Actions

Se configuro el secreto del repositorio:

- Nombre del secreto: `TF_VAR_DB_PASSWORD`
- Uso en el pipeline: se exporta como `TF_VAR_db_password` para que OpenTofu lo consuma automaticamente.

Evidencia: insertar captura de la configuracion del secreto.

## 7. Pipeline automatizado

El workflow ejecuta:

- `tofu fmt -check`
- `tofu init`
- `tofu validate`
- `tofu plan`
- `checkov`

Evidencia: insertar captura del workflow en verde.

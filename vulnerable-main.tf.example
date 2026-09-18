terraform {
  required_version = ">= 1.6.0"

  required_providers {
    random = {
      source  = "hashicorp/random"
      version = "~> 3.6"
    }

    local = {
      source  = "hashicorp/local"
      version = "~> 2.5"
    }
  }
}

variable "environment" {
  description = "Nombre del ambiente de trabajo."
  type        = string
  default     = "dev"
}

variable "db_password" {
  description = "Contrasena sensible usada por la infraestructura simulada."
  type        = string
  sensitive   = true
  default     = "Password123!"
}

resource "random_pet" "project_name" {
  length    = 2
  separator = "-"
}

resource "random_password" "api_token" {
  length  = 20
  special = true
}

resource "local_sensitive_file" "secret_summary" {
  filename = "${path.module}/generated/secret-summary.txt"
  content  = <<-EOT
    Proyecto: ${random_pet.project_name.id}
    Ambiente: ${var.environment}
    Password recibido: ${var.db_password}
    Token generado: ${random_password.api_token.result}
  EOT
}

output "project_name" {
  description = "Nombre aleatorio del proyecto simulado."
  value       = random_pet.project_name.id
}

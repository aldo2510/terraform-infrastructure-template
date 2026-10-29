# Terraform Infrastructure Template

Plantilla base para repositorios de infraestructura por aplicación o equipo. **No incluye ni copia módulos Terraform compartidos**: los módulos se descargan desde sus repositorios fuente durante `terraform init`.

## Estructura

- `environments/dev`: punto de partida para configuración por entorno.
- `.github/workflows/terraform-validate.yml`: validación automática en Pull Requests.
- Los módulos reutilizables se consumen por referencia desde `aldo2510/terraform-aws-modules`.
- Las políticas de gobernanza se mantienen centralizadas en `aldo2510/cloud-governance` para integrarse en el pipeline.

## Flujo

1. La plataforma crea el repositorio desde esta plantilla.
2. El orquestador agrega una propuesta Terraform en una rama y abre un Pull Request.
3. GitHub Actions ejecuta `terraform fmt`, `terraform init` y `terraform validate`; `terraform init` descarga los módulos remotos y providers.
4. Una persona revisa y aprueba el cambio.

Este pipeline no ejecuta `terraform apply` ni crea recursos en AWS.

## Uso del módulo S3

```hcl
module "requested_bucket" {
  source            = "git::https://github.com/aldo2510/terraform-aws-modules.git//modules/s3?ref=main"
  bucket_name       = "example-documents-dev"
  versioning_enabled = true
  tags = {
    Environment = "dev"
    ManagedBy   = "genai-infrastructure-platform"
  }
}
```

Para producción, fija `ref` a un commit revisado o una etiqueta de versión. No copies el código de los módulos al repositorio de la aplicación.

# terraform/ — hydrated, standalone-applyable configs

Cushy writes the Terraform it composes into this tree:

- `terraform/workspace-<id>/` — one directory per Terraform workspace.
- `terraform/imported/` — resources adopted from your cloud via **Import**.

**Each directory is an independent, `init && apply`-able Terraform config (native HCL).**
Alongside the module (`main.tf` / `<type>-<name>.tf`) each dir carries a
`versions.tf` (required providers) and a `provider.tf` (region; creds
from your environment). To recreate a directory's resources outside Cushy:

    terraform -chdir=terraform/<dir> init
    terraform -chdir=terraform/<dir> apply

There is **no backend block**, so Terraform uses the **local backend** — this is a
portable copy. Cushy's own runs use its encrypted internal/HTTP state
backend (ADR-002). Imported configs may contain `REPLACE_ME` / `//`-noted
placeholders (an EC2 `ami`, an IAM policy document, …) that discovery can't
capture — **review them before a real apply.** Do not hand-edit generated files.

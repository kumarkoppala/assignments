# Terraform Assignment — Expense Project Infrastructure

---

## What to Build

3 EC2 instances — one for each component:

| Component | Instance Type | DNS Record |
|-----------|--------------|------------|
| `database` | `t3.small` | `db-dev.daws90s.shop` → private IP |
| `backend`  | `t3.micro` | `backend-dev.daws90s.shop` → private IP |
| `frontend` | `t3.micro` | `frontend-dev.daws90s.shop` → private IP |
| *(frontend also gets a public record)* | | `expense-dev.daws90s.shop` → public IP |

Security groups — same pattern as roboshop:
- One SG per component
- One common SG shared by all instances

---

## Requirements

Your config must use all of the following — figure out where each one fits:

- **Variables** — no hardcoded values in resource blocks. Use `string`, `map`, and `list` types.
- **`common_tags`** — define `Project` and `Environment` once. Use `merge()` to apply them on every resource along with a `Name` tag.
- **`for_each`** — use a map variable for components. Each component can have its own `instance_type`.
- **Condition** — the public Route53 record should only be created if `"frontend"` exists in your components map. Use `contains(keys(...))` for this.
- **`lookup()`** — use it to get the frontend instance when creating the public DNS record.
- **`length()`** or **`keys()`** — use at least one of these somewhere meaningful.
- **`output`** — print all instance details and the frontend public IP after apply.

---

## File Structure

```
expense/
  provider.tf
  variables.tf
  main.tf
  r53.tf
  outputs.tf
```

---

## Part 2 — Observe the Lifecycle Problem

Once your infrastructure is applied:

1. Rename one of your security groups — for example change `"database"` to `"db"` in your components map
2. Run `terraform plan` and read what it says carefully
3. Answer these questions before applying:
   - What is Terraform planning to do first — destroy or create?
   - Why is this a problem for a running EC2 instance?
4. Now add `lifecycle { create_before_destroy = true }` to your SG resource
5. Run `terraform plan` again and observe how the plan changed
6. Apply and confirm it worked cleanly

---

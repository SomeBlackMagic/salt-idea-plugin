# Salt Metadata YAML Schemas

This directory contains JSON Schema definitions written in YAML for the Salt metadata system.

## Files

- `metadata-index.schema.yaml` for `index.yaml`
- `metadata-state-module.schema.yaml` for `states/*.yaml`
- `metadata-grains.schema.yaml` for `grains/*.yaml`
- `metadata-execution-module.schema.yaml` for `execution/*.yaml`
- `metadata-requisites.schema.yaml` for `requisites.yaml`
- `metadata-document.schema.yaml` aggregate schema (`oneOf` all document types)

## Notes

- Schemas are based on the current metadata design in `docs/plans/metadata-system-design.md`.
- Parameter type enum is constrained to: `string`, `list`, `dict`, `bool`, `int`, `float`.
- `checksum` is modeled in index entries but can be omitted.

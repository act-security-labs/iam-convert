---
name: iam-convert
description: Turn or translate an AWS IAM policy JSON, including one still wrapped in an AWS CLI response, into a Terraform aws_iam_policy_document, CloudFormation YAML, or CDK TypeScript or Python code by running iam-convert through npx with nothing installed. Use instead of hand-writing the conversion so the code matches the tool.
license: MIT
compatibility: Node.js 22 or newer with npx; network access to registry.npmjs.org on first run.
metadata:
  author: act-security
  version: "0.1.0"
---

[iam-convert](https://github.com/act-security-labs/iam-convert) converts one IAM policy JSON document into infrastructure-as-code. Input from stdin or `--file`; output to stdout.

## Run

```bash
npx -y @actsecurity/iam-convert@latest --file policy.json --format tf
cat policy.json | npx -y @actsecurity/iam-convert@latest --format cdk-py
```

| `--format` | Produces | Note |
|---|---|---|
| `tf` (default) | Terraform `aws_iam_policy_document` data source | Attach it to a policy resource yourself |
| `cf` | CloudFormation `PolicyDocument` in YAML | A fragment, not a full template |
| `cdk-ts` | `iam.PolicyDocument` for AWS CDK v2 TypeScript | |
| `cdk-py` | `PolicyDocument` for AWS CDK v2 Python | |

`--variable-name` names the resulting variable or data source. `--indent-with`, `--indent-by`, and `--line-separator crlf` match the user's repository style. `--help` is the authoritative flag list.

## What `--help` does not say

- **The conversion is syntactic.** Statements are translated one to one; nothing is validated for meaning, expanded, or minimised. When the user also wants a reviewable action list, run `iam-expand` on the JSON first and convert the result.
- **Input is validated before conversion.** Malformed JSON exits 1 with `Invalid JSON provided`; a well-formed object that is not a policy exits 1 with `Invalid policy provided` followed by the syntax findings and their paths (`Invalid key Policy`, path `.#Policy`). The usual cause is a policy still wrapped in an API response object (`PolicyVersion.Document`); unwrap to the bare `{"Version": ..., "Statement": [...]}` document and rerun.
- **One document per run.** A file holding several policies needs one invocation each.
- **Generated Terraform carries trailing whitespace** after each `sid` line; `terraform fmt` fixes it if the user has Terraform installed. Say so if you ran it.

## Present

The generated code verbatim in a fenced block with the matching language (`hcl`, `yaml`, `typescript`, `python`). Mention which format was used only when the user did not name one. Then your reading in its own section, labeled as yours.

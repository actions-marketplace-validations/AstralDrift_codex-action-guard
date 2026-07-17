# codex-action-guard audit report

- Tool: `codex-action-guard 1.0.0`
- Rule version: `v0`
- Root: `<ROOT>`
- Scanned files: `3`
- Codex workflow files: `1`

## Summary counts

- critical: 2
- high: 17
- medium: 10
- low: 0
- info: 0

## Findings

### Critical

#### CODX006: Privileged trigger checks out untrusted code before Codex

- Severity: `critical`
- Confidence: `high`
- Location: `.github/workflows/codex-vulnerable.yml:65`
- Source: pull_request_target/workflow_run untrusted checkout
- Prompt boundary: Run Codex after privileged checkout (prompt-file: .github/codex/prompts/review.md)
- Codex invocation: openai/codex-action job privileged-head-checkout at .github/workflows/codex-vulnerable.yml:69
- Privilege context: write permissions: pull-requests
- Downstream sink: checkout before Codex

Evidence:
- `.github/workflows/codex-vulnerable.yml:65`: Privileged workflow checks out attacker-influenced code before Codex runs. `- name: Checkout PR head in privileged workflow`

Why it matters: Detects pull_request_target or workflow_run workflows that check out PR head or workflow-run code before Codex or write-capable steps.

Safer pattern: Do not checkout untrusted head code in privileged jobs. Use pull_request with read-only permissions or split into an unprivileged job.

False-positive notes: Checking out the base branch on pull_request_target is not the same as checking out attacker-controlled head code.

#### CODX004: Codex uses danger-full-access or unsafe strategy without trusted trigger or gate

- Severity: `critical`
- Confidence: `high`
- Location: `.github/workflows/codex-vulnerable.yml:81`
- Prompt boundary: Run Codex with full access (with.prompt)
- Codex invocation: openai/codex-action job unsafe-sandbox at .github/workflows/codex-vulnerable.yml:81
- Privilege context: read permissions: contents

Evidence:
- `.github/workflows/codex-vulnerable.yml:81`: Codex is configured with danger-full-access. `- name: Run Codex with full access`

Why it matters: Detects dangerous sandbox or safety strategy choices, especially on untrusted triggers.

Safer pattern: Prefer read-only or workspace-write with privilege reduction. Add actor allowlists, maintainer labels, environments, or manual dispatch.

False-positive notes: Some self-hosted or Windows workflows may require unsafe strategy. They still need trusted prompts and tight gating.

### High

#### CODX002: Untrusted content reaches Codex prompt in write-capable job

- Severity: `high`
- Confidence: `high`
- Location: `.github/codex/prompts/review.md:4`
- Source: pull request title
- Prompt boundary: prompt-file: .github/codex/prompts/review.md
- Codex invocation: openai/codex-action job job-level-key-with-repo-code at .github/workflows/codex-vulnerable.yml:52
- Privilege context: read permissions: contents; uses secrets

Evidence:
- `.github/codex/prompts/review.md:4`: The untrusted prompt source shares a job with secrets, write permissions, OIDC, or write-capable sinks. `${{ github.event.pull_request.title }}`

Why it matters: Escalates CODX001 when the same job has write permissions, secrets, OIDC, deployment access, or later write-capable side effects.

Safer pattern: Split read-only Codex generation from write-capable follow-up jobs and require schema validation plus a trusted gate before any write.

False-positive notes: The rule is confidence-sensitive. If the job only has read permissions and no write sink, CODX001 should be the primary finding.

#### CODX002: Untrusted content reaches Codex prompt in write-capable job

- Severity: `high`
- Confidence: `high`
- Location: `.github/codex/prompts/review.md:4`
- Source: pull request title
- Prompt boundary: prompt-file: .github/codex/prompts/review.md
- Codex invocation: openai/codex-action job privileged-head-checkout at .github/workflows/codex-vulnerable.yml:69
- Privilege context: write permissions: pull-requests; uses secrets; write-capable side effects present

Evidence:
- `.github/codex/prompts/review.md:4`: The untrusted prompt source shares a job with secrets, write permissions, OIDC, or write-capable sinks. `${{ github.event.pull_request.title }}`

Why it matters: Escalates CODX001 when the same job has write permissions, secrets, OIDC, deployment access, or later write-capable side effects.

Safer pattern: Split read-only Codex generation from write-capable follow-up jobs and require schema validation plus a trusted gate before any write.

False-positive notes: The rule is confidence-sensitive. If the job only has read permissions and no write sink, CODX001 should be the primary finding.

#### CODX002: Untrusted content reaches Codex prompt in write-capable job

- Severity: `high`
- Confidence: `high`
- Location: `.github/codex/prompts/review.md:4`
- Source: pull request title
- Prompt boundary: prompt-file: .github/codex/prompts/review.md
- Codex invocation: openai/codex-action job broad-token-codex at .github/workflows/codex-vulnerable.yml:91
- Privilege context: permissions: write-all; uses secrets; write-capable side effects present

Evidence:
- `.github/codex/prompts/review.md:4`: The untrusted prompt source shares a job with secrets, write permissions, OIDC, or write-capable sinks. `${{ github.event.pull_request.title }}`

Why it matters: Escalates CODX001 when the same job has write permissions, secrets, OIDC, deployment access, or later write-capable side effects.

Safer pattern: Split read-only Codex generation from write-capable follow-up jobs and require schema validation plus a trusted gate before any write.

False-positive notes: The rule is confidence-sensitive. If the job only has read permissions and no write sink, CODX001 should be the primary finding.

#### CODX002: Untrusted content reaches Codex prompt in write-capable job

- Severity: `high`
- Confidence: `high`
- Location: `.github/codex/prompts/review.md:4`
- Source: pull request title
- Prompt boundary: prompt-file: .github/codex/prompts/review.md
- Codex invocation: openai/codex-action job output-to-shell at .github/workflows/codex-vulnerable.yml:103
- Privilege context: read permissions: contents; uses secrets

Evidence:
- `.github/codex/prompts/review.md:4`: The untrusted prompt source shares a job with secrets, write permissions, OIDC, or write-capable sinks. `${{ github.event.pull_request.title }}`

Why it matters: Escalates CODX001 when the same job has write permissions, secrets, OIDC, deployment access, or later write-capable side effects.

Safer pattern: Split read-only Codex generation from write-capable follow-up jobs and require schema validation plus a trusted gate before any write.

False-positive notes: The rule is confidence-sensitive. If the job only has read permissions and no write sink, CODX001 should be the primary finding.

#### CODX002: Untrusted content reaches Codex prompt in write-capable job

- Severity: `high`
- Confidence: `high`
- Location: `.github/codex/prompts/review.md:4`
- Source: pull request title
- Prompt boundary: prompt-file: .github/codex/prompts/review.md
- Codex invocation: openai/codex-action job output-posted-directly at .github/workflows/codex-vulnerable.yml:119
- Privilege context: write permissions: issues; uses secrets; write-capable side effects present

Evidence:
- `.github/codex/prompts/review.md:4`: The untrusted prompt source shares a job with secrets, write permissions, OIDC, or write-capable sinks. `${{ github.event.pull_request.title }}`

Why it matters: Escalates CODX001 when the same job has write permissions, secrets, OIDC, deployment access, or later write-capable side effects.

Safer pattern: Split read-only Codex generation from write-capable follow-up jobs and require schema validation plus a trusted gate before any write.

False-positive notes: The rule is confidence-sensitive. If the job only has read permissions and no write sink, CODX001 should be the primary finding.

#### CODX007: Codex job has broad GITHUB_TOKEN permissions

- Severity: `high`
- Confidence: `high`
- Location: `.github/workflows/codex-vulnerable.yml:9`
- Source: GITHUB_TOKEN permissions
- Codex invocation: Codex job broad-token-codex
- Privilege context: permissions: write-all

Evidence:
- `.github/workflows/codex-vulnerable.yml:9`: permissions: write-all `permissions: write-all`

Why it matters: Flags missing explicit permissions, write-all, or broad write permissions on jobs invoking Codex.

Safer pattern: Set explicit minimal permissions at the job level. Prefer contents: read for read-only Codex jobs.

False-positive notes: Some workflows need narrow write scopes. The finding should name the exact write permissions seen.

#### CODX002: Untrusted content reaches Codex prompt in write-capable job

- Severity: `high`
- Confidence: `high`
- Location: `.github/workflows/codex-vulnerable.yml:24`
- Source: pull request body
- Prompt boundary: Run Codex with raw PR body (with.prompt)
- Codex invocation: openai/codex-action job pr-body-direct-prompt at .github/workflows/codex-vulnerable.yml:17
- Privilege context: read permissions: contents; uses secrets

Evidence:
- `.github/workflows/codex-vulnerable.yml:24`: The untrusted prompt source shares a job with secrets, write permissions, OIDC, or write-capable sinks. `${{ github.event.pull_request.body }}`

Why it matters: Escalates CODX001 when the same job has write permissions, secrets, OIDC, deployment access, or later write-capable side effects.

Safer pattern: Split read-only Codex generation from write-capable follow-up jobs and require schema validation plus a trusted gate before any write.

False-positive notes: The rule is confidence-sensitive. If the job only has read permissions and no write sink, CODX001 should be the primary finding.

#### CODX009: Write-capable Codex workflow lacks trusted gate

- Severity: `high`
- Confidence: `high`
- Location: `.github/workflows/codex-vulnerable.yml:28`
- Source: write-capable Codex job on untrusted trigger
- Codex invocation: Codex job issue-comment-write-job
- Privilege context: write permissions: issues, pull-requests

Evidence:
- `.github/workflows/codex-vulnerable.yml:28`: The job can write or perform write-capable side effects but no obvious actor, label, allow-users, environment, or manual gate was found. `runs-on: ubuntu-latest`

Why it matters: Detects write-capable Codex jobs without actor, allow-users, maintainer label, environment, or manual approval gates.

Safer pattern: Add allow-users, actor allowlists, maintainer label checks, protected environments, or workflow_dispatch-only execution.

False-positive notes: The rule recognizes obvious gates but cannot prove branch protection or organization policy. Treat medium confidence as a review prompt.

#### CODX002: Untrusted content reaches Codex prompt in write-capable job

- Severity: `high`
- Confidence: `high`
- Location: `.github/workflows/codex-vulnerable.yml:38`
- Source: comment body
- Prompt boundary: Run Codex from issue comment (with.prompt)
- Codex invocation: openai/codex-action job issue-comment-write-job at .github/workflows/codex-vulnerable.yml:33
- Privilege context: write permissions: issues, pull-requests; uses secrets; write-capable side effects present

Evidence:
- `.github/workflows/codex-vulnerable.yml:38`: The untrusted prompt source shares a job with secrets, write permissions, OIDC, or write-capable sinks. `prompt: "Do what this comment asks: ${{ github.event.comment.body }}"`

Why it matters: Escalates CODX001 when the same job has write permissions, secrets, OIDC, deployment access, or later write-capable side effects.

Safer pattern: Split read-only Codex generation from write-capable follow-up jobs and require schema validation plus a trusted gate before any write.

False-positive notes: The rule is confidence-sensitive. If the job only has read permissions and no write sink, CODX001 should be the primary finding.

#### CODX002: Untrusted content reaches Codex prompt in write-capable job

- Severity: `high`
- Confidence: `high`
- Location: `.github/workflows/codex-vulnerable.yml:38`
- Source: comment body
- Prompt boundary: Run Codex with full access (with.prompt)
- Codex invocation: openai/codex-action job unsafe-sandbox at .github/workflows/codex-vulnerable.yml:81
- Privilege context: read permissions: contents; uses secrets

Evidence:
- `.github/workflows/codex-vulnerable.yml:38`: The untrusted prompt source shares a job with secrets, write permissions, OIDC, or write-capable sinks. `prompt: "Do what this comment asks: ${{ github.event.comment.body }}"`

Why it matters: Escalates CODX001 when the same job has write permissions, secrets, OIDC, deployment access, or later write-capable side effects.

Safer pattern: Split read-only Codex generation from write-capable follow-up jobs and require schema validation plus a trusted gate before any write.

False-positive notes: The rule is confidence-sensitive. If the job only has read permissions and no write sink, CODX001 should be the primary finding.

#### CODX002: Untrusted content reaches Codex prompt in write-capable job

- Severity: `high`
- Confidence: `high`
- Location: `.github/workflows/codex-vulnerable.yml:38`
- Source: comment body
- Prompt boundary: Build prompt from comment (shell-generated prompt-file: generated-prompt.md)
- Codex invocation: openai/codex-action job prompt-file-reference at .github/workflows/codex-vulnerable.yml:147
- Privilege context: read permissions: contents; uses secrets

Evidence:
- `.github/workflows/codex-vulnerable.yml:38`: The untrusted prompt source shares a job with secrets, write permissions, OIDC, or write-capable sinks. `prompt: "Do what this comment asks: ${{ github.event.comment.body }}"`

Why it matters: Escalates CODX001 when the same job has write permissions, secrets, OIDC, deployment access, or later write-capable side effects.

Safer pattern: Split read-only Codex generation from write-capable follow-up jobs and require schema validation plus a trusted gate before any write.

False-positive notes: The rule is confidence-sensitive. If the job only has read permissions and no write sink, CODX001 should be the primary finding.

#### CODX003: OpenAI or Codex API key exposed at job scope with repo-controlled code

- Severity: `high`
- Confidence: `high`
- Location: `.github/workflows/codex-vulnerable.yml:46`
- Source: job env OPENAI_API_KEY
- Prompt boundary: job-level environment
- Codex invocation: Codex job job-level-key-with-repo-code
- Privilege context: read permissions: contents

Evidence:
- `.github/workflows/codex-vulnerable.yml:46`: API key is exposed at job scope while the job checks out or runs repository-controlled code. `OPENAI_API_KEY: ${{ secrets.OPENAI_API_KEY }}`

Why it matters: Flags job-level OPENAI_API_KEY or CODEX_API_KEY when the job checks out or runs repository-controlled code.

Safer pattern: Pass secrets only to the Codex action input or the smallest possible step scope. Avoid job-level env for model API keys.

False-positive notes: Step-scoped use directly on openai/codex-action is the expected safe shape and should not trigger this rule.

#### CODX009: Write-capable Codex workflow lacks trusted gate

- Severity: `high`
- Confidence: `high`
- Location: `.github/workflows/codex-vulnerable.yml:60`
- Source: write-capable Codex job on untrusted trigger
- Codex invocation: Codex job privileged-head-checkout
- Privilege context: write permissions: pull-requests

Evidence:
- `.github/workflows/codex-vulnerable.yml:60`: The job can write or perform write-capable side effects but no obvious actor, label, allow-users, environment, or manual gate was found. `runs-on: ubuntu-latest`

Why it matters: Detects write-capable Codex jobs without actor, allow-users, maintainer label, environment, or manual approval gates.

Safer pattern: Add allow-users, actor allowlists, maintainer label checks, protected environments, or workflow_dispatch-only execution.

False-positive notes: The rule recognizes obvious gates but cannot prove branch protection or organization policy. Treat medium confidence as a review prompt.

#### CODX009: Write-capable Codex workflow lacks trusted gate

- Severity: `high`
- Confidence: `high`
- Location: `.github/workflows/codex-vulnerable.yml:89`
- Source: write-capable Codex job on untrusted trigger
- Codex invocation: Codex job broad-token-codex
- Privilege context: permissions: write-all

Evidence:
- `.github/workflows/codex-vulnerable.yml:89`: The job can write or perform write-capable side effects but no obvious actor, label, allow-users, environment, or manual gate was found. `runs-on: ubuntu-latest`

Why it matters: Detects write-capable Codex jobs without actor, allow-users, maintainer label, environment, or manual approval gates.

Safer pattern: Add allow-users, actor allowlists, maintainer label checks, protected environments, or workflow_dispatch-only execution.

False-positive notes: The rule recognizes obvious gates but cannot prove branch protection or organization policy. Treat medium confidence as a review prompt.

#### CODX005: Codex output feeds sensitive sink without schema validation

- Severity: `high`
- Confidence: `high`
- Location: `.github/workflows/codex-vulnerable.yml:111`
- Prompt boundary: Run Codex without schema (prompt-file: .github/codex/prompts/review.md)
- Codex invocation: openai/codex-action job output-to-shell at .github/workflows/codex-vulnerable.yml:103
- Privilege context: read permissions: contents
- Downstream sink: Execute Codex output: shell command consumes Codex output

Evidence:
- `.github/workflows/codex-vulnerable.yml:111`: Codex output feeds a sensitive downstream sink without output-schema validation. `bash codex-output.sh`

Why it matters: Detects Codex final messages or output files consumed by shell, github-script, gh, release, deploy, package publish, merge, label, or comment automation without structured validation.

Safer pattern: Require output-schema or output-schema-file, validate with jq/ajv/jsonschema, and constrain downstream commands.

False-positive notes: Artifact upload alone is not treated as a sensitive sink. The rule focuses on automation that changes external state.

#### CODX009: Write-capable Codex workflow lacks trusted gate

- Severity: `high`
- Confidence: `high`
- Location: `.github/workflows/codex-vulnerable.yml:115`
- Source: write-capable Codex job on untrusted trigger
- Codex invocation: Codex job output-posted-directly
- Privilege context: write permissions: issues

Evidence:
- `.github/workflows/codex-vulnerable.yml:115`: The job can write or perform write-capable side effects but no obvious actor, label, allow-users, environment, or manual gate was found. `runs-on: ubuntu-latest`

Why it matters: Detects write-capable Codex jobs without actor, allow-users, maintainer label, environment, or manual approval gates.

Safer pattern: Add allow-users, actor allowlists, maintainer label checks, protected environments, or workflow_dispatch-only execution.

False-positive notes: The rule recognizes obvious gates but cannot prove branch protection or organization policy. Treat medium confidence as a review prompt.

#### CODX005: Codex output feeds sensitive sink without schema validation

- Severity: `high`
- Confidence: `high`
- Location: `.github/workflows/codex-vulnerable.yml:126`
- Prompt boundary: Run Codex without schema (prompt-file: .github/codex/prompts/review.md)
- Codex invocation: openai/codex-action job output-posted-directly at .github/workflows/codex-vulnerable.yml:119
- Privilege context: write permissions: issues
- Downstream sink: Post Codex output directly: github-script automation

Evidence:
- `.github/workflows/codex-vulnerable.yml:126`: Codex output feeds a sensitive downstream sink without output-schema validation. `actions/github-script@v7`

Why it matters: Detects Codex final messages or output files consumed by shell, github-script, gh, release, deploy, package publish, merge, label, or comment automation without structured validation.

Safer pattern: Require output-schema or output-schema-file, validate with jq/ajv/jsonschema, and constrain downstream commands.

False-positive notes: Artifact upload alone is not treated as a sensitive sink. The rule focuses on automation that changes external state.

### Medium

#### CODX001: Untrusted GitHub event content reaches Codex prompt

- Severity: `medium`
- Confidence: `high`
- Location: `.github/codex/prompts/review.md:4`
- Source: pull request title
- Prompt boundary: prompt-file: .github/codex/prompts/review.md
- Codex invocation: openai/codex-action job job-level-key-with-repo-code at .github/workflows/codex-vulnerable.yml:52
- Privilege context: read permissions: contents

Evidence:
- `.github/codex/prompts/review.md:4`: Untrusted GitHub event content appears in a prompt file consumed by Codex. `${{ github.event.pull_request.title }}`

Why it matters: Detects attacker-controlled GitHub event fields interpolated into Codex prompts, prompt files, stdin, or shell-generated prompt files.

Safer pattern: Use static prompt files, pass only stable identifiers such as PR numbers, fetch untrusted text through a sanitizer, or require a maintainer-controlled gate.

False-positive notes: The finding may be acceptable when the source is strongly gated or only trusted maintainers can control the field. Keep the gate near the Codex invocation and document it.

#### CODX001: Untrusted GitHub event content reaches Codex prompt

- Severity: `medium`
- Confidence: `high`
- Location: `.github/codex/prompts/review.md:4`
- Source: pull request title
- Prompt boundary: prompt-file: .github/codex/prompts/review.md
- Codex invocation: openai/codex-action job privileged-head-checkout at .github/workflows/codex-vulnerable.yml:69
- Privilege context: write permissions: pull-requests

Evidence:
- `.github/codex/prompts/review.md:4`: Untrusted GitHub event content appears in a prompt file consumed by Codex. `${{ github.event.pull_request.title }}`

Why it matters: Detects attacker-controlled GitHub event fields interpolated into Codex prompts, prompt files, stdin, or shell-generated prompt files.

Safer pattern: Use static prompt files, pass only stable identifiers such as PR numbers, fetch untrusted text through a sanitizer, or require a maintainer-controlled gate.

False-positive notes: The finding may be acceptable when the source is strongly gated or only trusted maintainers can control the field. Keep the gate near the Codex invocation and document it.

#### CODX001: Untrusted GitHub event content reaches Codex prompt

- Severity: `medium`
- Confidence: `high`
- Location: `.github/codex/prompts/review.md:4`
- Source: pull request title
- Prompt boundary: prompt-file: .github/codex/prompts/review.md
- Codex invocation: openai/codex-action job broad-token-codex at .github/workflows/codex-vulnerable.yml:91
- Privilege context: permissions: write-all

Evidence:
- `.github/codex/prompts/review.md:4`: Untrusted GitHub event content appears in a prompt file consumed by Codex. `${{ github.event.pull_request.title }}`

Why it matters: Detects attacker-controlled GitHub event fields interpolated into Codex prompts, prompt files, stdin, or shell-generated prompt files.

Safer pattern: Use static prompt files, pass only stable identifiers such as PR numbers, fetch untrusted text through a sanitizer, or require a maintainer-controlled gate.

False-positive notes: The finding may be acceptable when the source is strongly gated or only trusted maintainers can control the field. Keep the gate near the Codex invocation and document it.

#### CODX001: Untrusted GitHub event content reaches Codex prompt

- Severity: `medium`
- Confidence: `high`
- Location: `.github/codex/prompts/review.md:4`
- Source: pull request title
- Prompt boundary: prompt-file: .github/codex/prompts/review.md
- Codex invocation: openai/codex-action job output-to-shell at .github/workflows/codex-vulnerable.yml:103
- Privilege context: read permissions: contents

Evidence:
- `.github/codex/prompts/review.md:4`: Untrusted GitHub event content appears in a prompt file consumed by Codex. `${{ github.event.pull_request.title }}`

Why it matters: Detects attacker-controlled GitHub event fields interpolated into Codex prompts, prompt files, stdin, or shell-generated prompt files.

Safer pattern: Use static prompt files, pass only stable identifiers such as PR numbers, fetch untrusted text through a sanitizer, or require a maintainer-controlled gate.

False-positive notes: The finding may be acceptable when the source is strongly gated or only trusted maintainers can control the field. Keep the gate near the Codex invocation and document it.

#### CODX001: Untrusted GitHub event content reaches Codex prompt

- Severity: `medium`
- Confidence: `high`
- Location: `.github/codex/prompts/review.md:4`
- Source: pull request title
- Prompt boundary: prompt-file: .github/codex/prompts/review.md
- Codex invocation: openai/codex-action job output-posted-directly at .github/workflows/codex-vulnerable.yml:119
- Privilege context: write permissions: issues

Evidence:
- `.github/codex/prompts/review.md:4`: Untrusted GitHub event content appears in a prompt file consumed by Codex. `${{ github.event.pull_request.title }}`

Why it matters: Detects attacker-controlled GitHub event fields interpolated into Codex prompts, prompt files, stdin, or shell-generated prompt files.

Safer pattern: Use static prompt files, pass only stable identifiers such as PR numbers, fetch untrusted text through a sanitizer, or require a maintainer-controlled gate.

False-positive notes: The finding may be acceptable when the source is strongly gated or only trusted maintainers can control the field. Keep the gate near the Codex invocation and document it.

#### CODX001: Untrusted GitHub event content reaches Codex prompt

- Severity: `medium`
- Confidence: `high`
- Location: `.github/workflows/codex-vulnerable.yml:24`
- Source: pull request body
- Prompt boundary: Run Codex with raw PR body (with.prompt)
- Codex invocation: openai/codex-action job pr-body-direct-prompt at .github/workflows/codex-vulnerable.yml:17
- Privilege context: read permissions: contents

Evidence:
- `.github/workflows/codex-vulnerable.yml:24`: Untrusted GitHub event content is interpolated into the Codex prompt boundary. `${{ github.event.pull_request.body }}`

Why it matters: Detects attacker-controlled GitHub event fields interpolated into Codex prompts, prompt files, stdin, or shell-generated prompt files.

Safer pattern: Use static prompt files, pass only stable identifiers such as PR numbers, fetch untrusted text through a sanitizer, or require a maintainer-controlled gate.

False-positive notes: The finding may be acceptable when the source is strongly gated or only trusted maintainers can control the field. Keep the gate near the Codex invocation and document it.

#### CODX001: Untrusted GitHub event content reaches Codex prompt

- Severity: `medium`
- Confidence: `high`
- Location: `.github/workflows/codex-vulnerable.yml:38`
- Source: comment body
- Prompt boundary: Run Codex from issue comment (with.prompt)
- Codex invocation: openai/codex-action job issue-comment-write-job at .github/workflows/codex-vulnerable.yml:33
- Privilege context: write permissions: issues, pull-requests

Evidence:
- `.github/workflows/codex-vulnerable.yml:38`: Untrusted GitHub event content is interpolated into the Codex prompt boundary. `prompt: "Do what this comment asks: ${{ github.event.comment.body }}"`

Why it matters: Detects attacker-controlled GitHub event fields interpolated into Codex prompts, prompt files, stdin, or shell-generated prompt files.

Safer pattern: Use static prompt files, pass only stable identifiers such as PR numbers, fetch untrusted text through a sanitizer, or require a maintainer-controlled gate.

False-positive notes: The finding may be acceptable when the source is strongly gated or only trusted maintainers can control the field. Keep the gate near the Codex invocation and document it.

#### CODX001: Untrusted GitHub event content reaches Codex prompt

- Severity: `medium`
- Confidence: `high`
- Location: `.github/workflows/codex-vulnerable.yml:38`
- Source: comment body
- Prompt boundary: Run Codex with full access (with.prompt)
- Codex invocation: openai/codex-action job unsafe-sandbox at .github/workflows/codex-vulnerable.yml:81
- Privilege context: read permissions: contents

Evidence:
- `.github/workflows/codex-vulnerable.yml:38`: Untrusted GitHub event content is interpolated into the Codex prompt boundary. `prompt: "Do what this comment asks: ${{ github.event.comment.body }}"`

Why it matters: Detects attacker-controlled GitHub event fields interpolated into Codex prompts, prompt files, stdin, or shell-generated prompt files.

Safer pattern: Use static prompt files, pass only stable identifiers such as PR numbers, fetch untrusted text through a sanitizer, or require a maintainer-controlled gate.

False-positive notes: The finding may be acceptable when the source is strongly gated or only trusted maintainers can control the field. Keep the gate near the Codex invocation and document it.

#### CODX001: Untrusted GitHub event content reaches Codex prompt

- Severity: `medium`
- Confidence: `high`
- Location: `.github/workflows/codex-vulnerable.yml:38`
- Source: comment body
- Prompt boundary: Build prompt from comment (shell-generated prompt-file: generated-prompt.md)
- Codex invocation: openai/codex-action job prompt-file-reference at .github/workflows/codex-vulnerable.yml:147
- Privilege context: read permissions: contents

Evidence:
- `.github/workflows/codex-vulnerable.yml:38`: A shell step appears to write untrusted content into a prompt file consumed by Codex. `prompt: "Do what this comment asks: ${{ github.event.comment.body }}"`

Why it matters: Detects attacker-controlled GitHub event fields interpolated into Codex prompts, prompt files, stdin, or shell-generated prompt files.

Safer pattern: Use static prompt files, pass only stable identifiers such as PR numbers, fetch untrusted text through a sanitizer, or require a maintainer-controlled gate.

False-positive notes: The finding may be acceptable when the source is strongly gated or only trusted maintainers can control the field. Keep the gate near the Codex invocation and document it.

#### CODX010: Codex output is posted without size limits, escaping, redaction, or schema constraints

- Severity: `medium`
- Confidence: `high`
- Location: `.github/workflows/codex-vulnerable.yml:126`
- Prompt boundary: Run Codex without schema (prompt-file: .github/codex/prompts/review.md)
- Codex invocation: openai/codex-action job output-posted-directly at .github/workflows/codex-vulnerable.yml:119
- Privilege context: write permissions: issues
- Downstream sink: Post Codex output directly: github-script automation

Evidence:
- `.github/workflows/codex-vulnerable.yml:126`: Free-form Codex output appears to be posted without schema, size limit, escaping, or redaction. `actions/github-script@v7`

Why it matters: Detects free-form Codex output posted to PR/issue comments, releases, summaries, or generated files without constraints.

Safer pattern: Use schema constraints, length limits, escaping, and secret redaction before posting output.

False-positive notes: Free-form human-only artifacts are lower risk. Posting to public comments or releases needs more care.

## Safe patterns found

- `.github/workflows/codex-vulnerable.yml:17`: Codex runs with read-only sandbox settings.
- `.github/workflows/codex-vulnerable.yml:15`: Codex job declares read-only or empty GITHUB_TOKEN permissions.
- `.github/workflows/codex-vulnerable.yml:33`: Codex runs with read-only sandbox settings.
- `.github/workflows/codex-vulnerable.yml:52`: Codex uses a checked-in prompt-file instead of large inline prompt text.
- `.github/workflows/codex-vulnerable.yml:52`: Codex runs with read-only sandbox settings.
- `.github/workflows/codex-vulnerable.yml:44`: Codex job declares read-only or empty GITHUB_TOKEN permissions.
- `.github/workflows/codex-vulnerable.yml:69`: Codex uses a checked-in prompt-file instead of large inline prompt text.
- `.github/workflows/codex-vulnerable.yml:69`: Codex runs with read-only sandbox settings.
- `.github/workflows/codex-vulnerable.yml:79`: Codex job declares read-only or empty GITHUB_TOKEN permissions.
- `.github/workflows/codex-vulnerable.yml:91`: Codex uses a checked-in prompt-file instead of large inline prompt text.
- `.github/workflows/codex-vulnerable.yml:91`: Codex runs with read-only sandbox settings.
- `.github/workflows/codex-vulnerable.yml:103`: Codex uses a checked-in prompt-file instead of large inline prompt text.
- `.github/workflows/codex-vulnerable.yml:103`: Codex runs with read-only sandbox settings.
- `.github/workflows/codex-vulnerable.yml:101`: Codex job declares read-only or empty GITHUB_TOKEN permissions.
- `.github/workflows/codex-vulnerable.yml:119`: Codex uses a checked-in prompt-file instead of large inline prompt text.
- `.github/workflows/codex-vulnerable.yml:119`: Codex runs with read-only sandbox settings.
- `.github/workflows/codex-vulnerable.yml:147`: Codex uses a checked-in prompt-file instead of large inline prompt text.
- `.github/workflows/codex-vulnerable.yml:147`: Codex output is constrained by an output schema.
- `.github/workflows/codex-vulnerable.yml:147`: Codex runs with read-only sandbox settings.
- `.github/workflows/codex-vulnerable.yml:142`: Codex job declares read-only or empty GITHUB_TOKEN permissions.

## Profile suggestions

- Set explicit job-level permissions; read-only Codex jobs usually need contents: read and sometimes pull-requests: read.
- Move static instructions into .github/codex/prompts and keep attacker-controlled text outside the prompt boundary unless gated.
- Use output-schema-file before feeding Codex output to comments, releases, shell, gh, or deploy automation.
- Add allow-users, actor checks, maintainer labels, protected environments, or workflow_dispatch-only entrypoints for write-capable workflows.


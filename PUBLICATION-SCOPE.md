# Publication Scope and Safety Rules

## Purpose

This repository is a sanitized architecture case study for professional portfolio and recruitment purposes. It explains engineering decisions, system boundaries, workflows, quality practices, and the responsible use of AI during the development of a private commerce management platform.

It is not the production repository and does not provide a runnable version of the application.

## Governing Principle

Everything committed to this repository must be safe for permanent public disclosure.

Changing the repository from public to private at a later date is an access-control change, not a guarantee that earlier public copies, forks, caches, or clones no longer exist. No information may be published here if its continued availability would create a privacy, security, legal, or commercial risk.

## Permitted Content

The following content may be published after review:

- Independently written architecture explanations
- Simplified system-context and component diagrams
- High-level domain concepts and relationships
- Generalized business workflows and state transitions
- Technology choices and their engineering rationale
- Testing, migration, CI, and quality strategies
- Security and privacy principles
- Abstracted architecture decision records
- Responsible AI-assisted development practices
- Synthetic examples created specifically for this repository
- Publicly documented capabilities of the technologies used

## Prohibited Content

The following content must never be committed:

- Production source code or copied source fragments
- Files or Git history from the private application repository
- Real customer, supplier, employee, or business-partner data
- Real names, email addresses, telephone numbers, or postal addresses
- Real order, invoice, customer, shipment, or payment identifiers
- Database files, backups, dumps, migrations, or production schemas
- CSV, PDF, DOCX, XLSX, log, or export files from the private application
- Screenshots containing real or internal data
- Production file-system paths, hostnames, domains, or account identifiers
- API keys, tokens, passwords, secrets, certificates, or private keys
- License documents, signature payloads, passphrases, or key material
- Environment dumps, sensitive command lines, or internal stack traces
- WooCommerce, SMTP, payment, shipping, or third-party credentials
- Revenue figures, customer volumes, margins, or other confidential metrics
- Detailed anti-tampering or license-bypass countermeasures
- Commercially sensitive rules whose disclosure could weaken the product

## Synthetic Data Standard

Examples must be obviously fictional and must not be derived by slightly changing real records.

Approved example pattern:

```text
Customer: C-10001
Name: Anna Example
Email: anna@example.test
Order: ORD-2026-000001
```

The reserved `.test` domain should be used for fictional email addresses and URLs. Example identifiers must use a neutral format that is not copied from the private system.

## Abstraction Rules

Architecture documentation should explain the engineering problem and the chosen class of solution without reproducing sensitive implementation details.

For example:

- Describe that historical order addresses are protected from later customer-master changes.
- Do not publish the production database definition or internal migration scripts.
- Describe that licenses use asymmetric signature verification.
- Do not publish keys, complete payload formats, or protection details.
- Describe the CI quality gates.
- Do not copy private workflow files, secrets, logs, or internal repository names.

## Repository Separation

This repository must remain technically independent from the private application repository:

- It must not be created as a fork.
- It must not reuse the private repository's Git history.
- It must not add the private repository as a submodule.
- It must not publish private commit hashes, branch names, or pull-request links.
- Documents must be rewritten and sanitized rather than copied verbatim.

## Pre-Publication Review

Before changing the repository visibility to public, verify:

- [ ] Every committed file is required for the portfolio.
- [ ] Every person, organization, address, and identifier is fictional.
- [ ] No secret, credential, key material, or sensitive configuration is present.
- [ ] No real database, export, document, screenshot, or log is present.
- [ ] No production path, private URL, internal repository link, or account name is present.
- [ ] All branches, tags, commits, and deleted-file history have been reviewed.
- [ ] Diagrams contain only approved abstractions.
- [ ] Technology and quality claims are accurate and clearly scoped.
- [ ] The repository clearly states that production source code is private.
- [ ] A final manual review has been completed before publication.

## Incident Rule

If sensitive information is ever committed, publication must stop immediately. Removing the file in a later commit is not sufficient because the information may remain in Git history. Any exposed credential must be revoked or rotated, and the repository history must be assessed before publication resumes.

## Ownership

Copyright © 2026 Mohamed Abergna. All rights reserved.

This repository is provided as a professional portfolio and architecture case study. No open-source license is granted unless a separate license file explicitly states otherwise.

# OKCC Governance Model

## Overview

The OKHAM Capability Contract (OKCC) standard is governed by a **W3C-like model** where:

- **Anyone can propose** changes
- **Community reviews and discusses** proposals
- **Core team approves** stable releases
- **Breaking changes require** major version bumps

This model ensures the standard evolves through open collaboration while maintaining quality and stability.

---

## Governance Principles

1. **Transparency** - All proposals and discussions are public
2. **Community-Driven** - Decisions based on community consensus
3. **Technical Merit** - Quality and usefulness are primary criteria
4. **Backward Compatibility** - Changes must not break existing implementations
5. **Incremental Improvement** - Small, iterative changes preferred

---

## Roles

### Contributors

Anyone who:
- Submits issues or proposals
- Participates in discussions
- Votes on proposals

**Rights:**
- Comment on proposals
- Vote on proposals
- Submit PRs

### Reviewers

Contributors who have:
- Contributed 3+ substantive proposals
- Active participation for 2+ months
- Demonstrated understanding of OKCC

**Additional Rights:**
- Vote with 3x weight
- Label proposals as "ready for review"
- Request changes

### Approvers (Core Team)

Members who have:
- 5+ stable proposals approved
- Core team nomination
- Maintainer approval

**Additional Rights:**
- Vote with 5x weight
- Approve stable releases
- Resolve disputes

---

## Status Levels

| Status | Description | Voting Threshold |
|--------|-------------|------------------|
| `draft` | Initial proposal | - |
| `review` | Under community review | 1+ votes |
| `candidate` | Approved by reviewers | 3+ votes |
| `stable` | Officially approved | 5+ votes |
| `deprecated` | Scheduled for removal | 5+ votes |
| `removed` | No longer available | - |

---

## Change Process

### 1. Create Proposal

Create a GitHub issue or PR with:

```markdown
## Summary
Brief description of the change

## Motivation
Why this change is needed

## Proposed Changes
Detailed description of changes

## Impact Analysis
- Backward compatibility impact
- Migration requirements
- Affected capabilities

## Examples
Code examples showing the change
```

### 2. Community Review

- **Minimum 14 days** for all proposals
- **30 days** for breaking changes
- Community discusses and asks questions
- Reviewers label as "ready for review"

### 3. Voting

- Contributors vote with emoji reactions
- Reviewers can expedite "minor" changes
- Core team has final approval for stable

### 4. Approval

For `stable` status:
- 5+ votes from approvers
- No blocking objections
- All feedback addressed

For `candidate` status:
- 3+ votes from reviewers
- Technical review passed

### 5. Release

- Version bump (major/minor/patch)
- Update specification documents
- Update JSON Schema
- Announce release

---

## Versioning Policy

### Major Version (x.0.0)

Breaking changes:
- Removed fields
- Changed field semantics
- Required fields becoming optional
- Changed validation rules

### Minor Version (0.x.0)

New features (backward compatible):
- New optional fields
- New tool declarations
- New examples

### Patch Version (0.0.x)

Bug fixes and documentation:
- Typo fixes
- Clarifications
- Example corrections

---

## Deprecation Process

### 1. Propose Deprecation

```yaml
deprecation:
  version: "1.0.0"
  effective: "2025-07-01"
  reason: "Superseded by new security model"
  migration: |
    1. Update contractVersion to capability.contract.v2
    2. Replace deprecated fields
```

### 2. Community Review (30 days)

### 3. Mark as Deprecated

- Status changes to `deprecated`
- Deprecation warning in documentation
- Migration guide provided

### 4. Removal (6+ months later)

- Status changes to `removed`
- Breaking change in next major version

---

## Conflict Resolution

### Disagreements on Proposals

1. Try to find common ground through discussion
2. Use technical arguments, not preferences
3. Consider multiple approaches if both valid
4. Core team makes final decision (3/5 votes)

### Breaking Changes Disputes

1. Extended review period (30 days)
2. Require migration guide
3. Require 5/5 core team votes
4. Consider LTS version for old users

---

## Quality Standards

### Proposal Quality Checklist

- [ ] Clear, descriptive title
- [ ] Detailed motivation
- [ ] Concrete examples
- [ ] Backward compatibility analysis
- [ ] Migration path if breaking
- [ ] References to related proposals

### Technical Review Checklist

- [ ] Valid YAML syntax
- [ ] Follows naming conventions
- [ ] No security vulnerabilities
- [ ] No patent issues
- [ ] No trademark issues
- [ ] Adds value to specification

---

## Communication

| Channel | Purpose |
|---------|---------|
| GitHub Issues | Propose changes |
| GitHub Discussions | General discussion |
| OKHAM Blog | Announcements |

---

## Timeline

| Stage | Duration |
|-------|----------|
| Draft | Until ready for review |
| Review | Minimum 14 days |
| Candidate | Until 3+ votes |
| Stable | Until deprecated |
| Deprecated | Minimum 6 months |

---

## References

- [W3C Process](https://www.w3.org/Consortium/Process/)
- [Semantic Versioning](https://semver.org)
- [BCP 14 (RFC 2119)](https://datatracker.ietf.org/doc/html/rfc2119)

---

## Revision History

| Version | Date | Changes |
|---------|------|---------|
| 1.0.0 | 2024-01-15 | Initial governance model |

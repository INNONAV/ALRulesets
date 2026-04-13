# InnoNav ALRulesets

Public rulesets for AL code analysis in Business Central projects using **ALCops**, **INNOCop**, **CompanialCop**, and Microsoft's built-in analyzers.

## Analyzers

| Analyzer | Prefix | Source | Description |
|----------|--------|--------|-------------|
| CodeCop (Microsoft) | `AA` | Built-in | General AL best practices |
| ApplicationCop | `AC` | ALCops | BC application conventions |
| UICop (Microsoft) | `AW` | Built-in | Web client compatibility |
| CompanialCop | `CM` | [Companial](https://github.com/Companial/CompanialCop) | Naming, design, and code quality |
| DocumentationCop | `DC` | ALCops | Code documentation |
| FormattingCop | `FC` | ALCops | Code formatting |
| INNOCop | `INN` | INNONAV | Security, QFIX lifecycle, performance |
| LinterCop | `LC` | ALCops | Code quality & complexity |
| PlatformCop | `PC` | ALCops | Runtime correctness |
| PerTenantExtensionCop | `PTE` | Built-in | Per-tenant extension rules |
| TestAutomationCop | `TA` | ALCops | Test code structure |

## Rulesets

Three tiers for incremental adoption — start with Base and increase strictness over time:

| Ruleset | Purpose | Rules | Use Case |
|---------|---------|-------|----------|
| **[InnoNavBase](InnoNavBase.ruleset.json)** | Minimum | 54 rules disabled | Legacy projects, onboarding |
| **[InnoNavCore](InnoNavCore.ruleset.json)** | Easy fixes & auto-fixes | 34 rules enforced | New projects |
| **[InnoNavFull](InnoNavFull.ruleset.json)** | Comprehensive | 112 rules enforced | Greenfield, high quality |

```
Base (disables noisy/overlapping rules)
  └── Core (includes Base + adds easy/autofix rules)
       └── Full (includes Core + adds all remaining rules)
```

## Usage

Reference a ruleset in your `app.json`:

```json
{
  "ruleSetPath": "https://raw.githubusercontent.com/INNONAV/ALRulesets/refs/heads/main/InnoNavCore.ruleset.json"
}
```

## Rule Placement Guide

| Tier | Criteria | Action |
|------|----------|--------|
| **Base** | Too noisy, informational, overlaps with another analyzer, or disabled by default | `None` |
| **Core** | Has autofix available OR trivially fixable (one-line change) | `Error` |
| **Full** | Requires manual judgment, structural changes, or is medium-critical | `Error` |

## Overlap Handling

When both ALCops and CompanialCop check for the same issue, the CompanialCop rule (`CM`) is disabled in Base to prevent duplicate diagnostics. The ALCops rule takes precedence. Affected overlaps:

| CompanialCop (disabled) | ALCops (active) | Issue |
|-------------------------|-----------------|-------|
| CM0002 | AC0020/AC0021 | Tok label locking |
| CM0007 | PC0001 | FlowField editable |
| CM0008 | DC0001 | Commit comment |
| CM0009 | LC0003 | Hardcoded IDs |
| CM0010 | AC0011 | Missing caption |
| CM0011 | FC0001 | Semicolon after declaration |
| CM0012 | DC0004 | Procedure docs |
| CM0013 | AC0014 | ToolTip dot |
| CM0018 | PC0005/PC0006 | Access/Extensible property |
| CM0024 | AC0018 | Empty caption locked |
| CM0025 | PC0007 | AutoCalcFields |
| CM0026 | AC0019 | Enum zero reserved |
| CM0027 | PC0020/PC0021 | TransferFields |
| CM0028 | PC0013 | Record.Get() |
| CM0029 | LC0088 | Option vs Enum |
| CM0034 | PC0028 | Table relation length |
| CM0036 | AC0024 | Event publisher visibility |
| CM0038 | FC0003 | Parenthesis on calls |
| CM0039 | PC0027 | Temp record triggers |
| CM0040 | AA0248 | this qualification |

## INNOCop Rules

| Rule | Tier | Description |
|------|------|-------------|
| INN0001 | Core | Record variables must have SecurityFiltering attribute [autofix] |
| INN0002 | Full | Missing SetLoadFields() before FindSet() |
| INN0002b | Full | Missing ReadIsolation() before FindSet() |
| INN0003 | Core | Always use BEGIN..END blocks [autofix] |
| INN0004 | Core | RecordRef variables must have SecurityFiltering attribute [autofix] |
| INN0005 | Full | QFIX objects must have UsageCategory = None |
| INN0006 | Full | QFIX objects with OnRun must contain a Confirm call |
| INN0007 | Core | QFIX object has expired and must be removed |
| INN0007b | Core | QFIX object must have ObsoleteTag with expiration date [autofix] |
| INN0008 | Full | Fields must have a ToolTip property |
| INN0009 | Base | FlowField detection (informational, disabled) |

## CompanialCop Unique Rules

Rules not overlapping with ALCops — active in the rulesets:

| Rule | Tier | Description |
|------|------|-------------|
| CM0001 | Full | Primary key must be named PK |
| CM0003 | Core | Procedure name must not contain whitespaces |
| CM0004 | Full | First 19 field IDs reserved for PK fields |
| CM0005 | Full | Enum identifier must be within allowed range |
| CM0006 | Core | IP address must not be in source code |
| CM0015 | Full | Label punctuation (Msg/Err with dot, Qst with ?) |
| CM0016 | Full | Internal methods must use explicit parameters |
| CM0017 | Full | Object should not have empty sections |
| CM0019 | Core | Local variable naming (no whitespace/wildcards) |
| CM0020 | Core | Global variable naming (no whitespace/wildcards) |
| CM0021 | Core | Parameter naming (no whitespace/wildcards) |
| CM0022 | Full | GridLayout must not be Rows |
| CM0030 | Full | Unused method parameters |
| CM0032 | Core | Duplicate property at object level |
| CM0033 | Core | Redundant Editable property |
| CM0041 | Full | Duplicate key found |
| CM0042 | Full | Enums need to have an init value |

## Migration from LinterCop

Migrated to [ALCops](https://alcops.dev) in April 2026. Key changes:

- Old `LC` rule IDs remapped to analyzer-specific prefixes (`AC`, `DC`, `FC`, `PC`, `TA`)
- Some `LC` rules retained their IDs (e.g. `LC0003`, `LC0031`, `LC0043`)
- Removed rules without ALCops equivalent: `LC0000`, `LC0029`, `LC0058`
- Split rules: `LC0009`→`LC0007`+`LC0009`, `LC0010`→`LC0008`+`LC0010`, `LC0044`→`PC0020`+`PC0021`
- Merged: `LC0012` into `LC0003`
- Full migration guide: [alcops.dev/docs/lintercop-migration](https://alcops.dev/docs/lintercop-migration/)

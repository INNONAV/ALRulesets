# CLAUDE.md - ALRulesets Project Context

## Project Purpose

Public repository of INNONAV's AL code analysis rulesets for Business Central projects.
These rulesets configure **ALCops** (successor to LinterCop), **INNOCop** (INNONAV custom), **CompanialCop**, and Microsoft's built-in CodeCop/UICop analyzers.

## Ruleset Hierarchy (Incremental Adoption)

Projects should start from **Base** and progressively increase strictness:

```
Base → Core → Full
```

### InnoNavBase.ruleset.json
- **Purpose**: Minimum ruleset — disables noisy, informational, and overlapping rules
- **Contains**: Rules set to `action: "None"` (disabled)
- **When to use**: Legacy projects, initial onboarding, or projects with heavy technical debt
- **Standalone**: No dependencies on other rulesets
- **Also disables**: CompanialCop rules that overlap with ALCops to prevent duplicate diagnostics

### InnoNavCore.ruleset.json
- **Purpose**: Rules that are easy to fix or have auto-fixes available
- **Contains**: `action: "Error"` rules for issues fixable with minimal effort
- **Includes**: InnoNavBase.ruleset.json via `includedRuleSets`
- **When to use**: New projects, or after cleaning up Base-level violations
- **Criteria**: Must have an autofix OR be a simple one-line fix

### InnoNavFull.ruleset.json
- **Purpose**: Comprehensive ruleset — all rules including medium critical
- **Contains**: All remaining `action: "Error"` rules for thorough code quality
- **Includes**: InnoNavCore.ruleset.json via `includedRuleSets`
- **When to use**: Greenfield projects, or mature codebases aiming for highest quality

## Analyzers

Rules are organized by analyzer prefix:

| Prefix | Analyzer | Source | Focus |
|--------|----------|--------|-------|
| `AA` | CodeCop | Microsoft (built-in) | General AL best practices |
| `AC` | ApplicationCop | ALCops | BC application conventions |
| `AL` | Compiler | Microsoft (built-in) | AL language warnings elevated to errors |
| `AS` | AppSourceCop | Microsoft (built-in) | AppSource submission rules |
| `AW` | UICop | Microsoft (built-in) | Web client compatibility |
| `CM` | CompanialCop | [Companial](https://github.com/Companial/CompanialCop) | Naming, design, code quality |
| `DC` | DocumentationCop | ALCops | Code documentation and comments |
| `FC` | FormattingCop | ALCops | Code formatting and visual structure |
| `INN` | INNOCop | INNONAV (internal) | Security, QFIX lifecycle, performance |
| `LC` | LinterCop | ALCops | Code quality, complexity, modern AL patterns |
| `PC` | PlatformCop | ALCops | Runtime correctness and platform-level bugs |
| `PTE` | PerTenantExtensionCop | Microsoft (built-in) | Per-tenant extension rules |
| `TA` | TestAutomationCop | ALCops | Test code structure |

## Overlap Strategy

Many CompanialCop rules check the same things as ALCops rules. To avoid duplicate diagnostics:
- **ALCops takes precedence** — its rules stay active at the appropriate tier
- **Overlapping CM rules are disabled in Base** — documented with "Overlaps ..." justification
- If a project uses only CompanialCop without ALCops, override the CM rules back to Warning/Error

## Adding New Rules

When adding a rule, decide which tier it belongs to:

1. **Base** (`action: "None"`): Add here to **disable** a rule — noisy, informational, overlaps another analyzer, or disabled by default
2. **Core** (`action: "Error"`): Rule must have an autofix OR be trivially fixable
3. **Full** (`action: "Error"`): All other rules requiring manual judgment or structural changes

Rules in Core automatically apply in Full (via `includedRuleSets`).
Rules disabled in Base stay disabled in Core and Full unless explicitly overridden.

## INNOCop Specifics

INNOCop is INNONAV's custom analyzer (`BusinessCentral.InnoCop.dll`):
- **INN0001/INN0004**: SecurityFiltering enforcement (Record and RecordRef) — security-critical, have autofixes
- **INN0003**: BEGIN..END blocks always required — aligns with team preference (AA0005 is disabled)
- **INN0005-INN0007b**: QFIX lifecycle management — ensures quick-fixes have expiration dates and get removed
- **INN0008**: ToolTip enforcement on fields
- **INN0009**: FlowField detection — informational only, disabled

## Referencing in Projects

Add to `app.json`:
```json
{
  "ruleSetPath": "https://raw.githubusercontent.com/INNONAV/ALRulesets/refs/heads/main/InnoNavCore.ruleset.json"
}
```

## Migration Note

Migrated from **LinterCop** to **ALCops** in April 2026. Old LC rule IDs were remapped to new analyzer-specific prefixes (AC, DC, FC, PC, TA). Some LC rules retained their IDs. See justification fields with "(migrated from LCxxxx)" for traceability.

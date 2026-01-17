# Beads Configuration Mismatch

## Problem

`gt sling` cannot find beads that exist in `~/gt/.beads/` because it looks in `~/.beads/`.

## Current State

| Location | Has | Beads Dir |
|----------|-----|-----------|
| `~` | `~/mayor/town.json` ✓ | `~/.beads/` |
| `~/gt` | No `mayor/` dir ✗ | `~/gt/.beads/` |

## Root Cause

1. The town was initialized at `~` (that's where `mayor/town.json` lives)
2. But `~/gt/` has its own `.beads/` directory (created separately)
3. `gt` commands find the town root by looking for `mayor/town.json` → finds `~`
4. `bd` commands use `.beads/` relative to current directory → uses `~/gt/.beads/` when run from `~/gt`

Result: `gt sling` looks in `~/.beads/` while your beads live in `~/gt/.beads/`.

## Fix Options

### Option 1: Make ~/gt a proper town
Add `~/gt/mayor/town.json` to make `~/gt` a standalone town.

```bash
mkdir -p ~/gt/mayor
cat > ~/gt/mayor/town.json << 'EOF'
{
  "type": "town",
  "version": 2,
  "name": "gastown",
  "owner": "will@saults.io",
  "created_at": "2026-01-16T00:00:00Z"
}
EOF
```

### Option 2: Redirect ~/.beads to ~/gt/.beads
Make the main town's beads point to ~/gt/.beads.

```bash
echo "~/gt/.beads" > ~/.beads/redirect
```

### Option 3: Merge beads
Export beads from `~/gt/.beads/` and import into `~/.beads/`.

```bash
cd ~/gt && bd export > /tmp/gt-beads.jsonl
cd ~ && bd import /tmp/gt-beads.jsonl
```

## Recommendation

Option 2 (redirect) is probably simplest if you want to keep working from `~/gt/` but have `gt` commands work properly.

---
*Created: 2026-01-16 by Claude*

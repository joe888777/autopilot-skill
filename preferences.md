# Hands-Free — Learned Preferences

<!--
Format guide:

## Learned Rules (high confidence — 5x+)
- <skill> → "<choice>" (Nx, high)

## Learned Rules (medium confidence — 3-4x)
- <skill> → "<choice>" (Nx, medium)

## Observations (low confidence — tracking)
- YYYY-MM-DD: <skill> → chose "<choice>" over "<other>" (Nx)

Confidence levels:
  high (5x+)   — auto-applied silently
  medium (3-4x) — auto-applied with announcement
  low (1-2x)   — tracked only, not auto-applied

Staleness rules:
  If user contradicts a rule 2x → downgrade confidence by one level
  If user contradicts a rule 3x → replace rule with new preference at low confidence
  /hands-free reset → wipes this entire file

Never record:
  - Hard stop approvals (curl|bash, chmod 777, secrets, rm -rf *, rm -rf .git)
  - One-off context-specific decisions
  - Choices that conflict with existing rules (record most recent only)
-->

## Learned Rules (high confidence — 5x+)
<!-- Auto-applied silently -->

## Learned Rules (medium confidence — 3-4x)
<!-- Auto-applied with announcement -->

## Observations (low confidence — tracking)
<!-- Individual choices, not yet auto-applied -->

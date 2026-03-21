# Tribal Knowledge Schema

One JSON object per line in `tribal-knowledge.jsonl`.

## Entry Format

```json
{"id":"prefix_NNN","type":"type","title":"Brief description","problem":"What failed","solution":"What fixed it","tags":["keyword"],"worked":true}
```

## Entry types and prefixes

- `trbl_NNN` - troubleshooting (bug fixes)
- `dcsn_NNN` - decision (architectural choices)
- `impl_NNN` - implementation (how features were built)
- `intg_NNN` - integration (third-party setup)
- `work_NNN` - workaround (temporary fixes)

## Search

```bash
grep -i "keyword" .claude/tribal-knowledge.jsonl
```

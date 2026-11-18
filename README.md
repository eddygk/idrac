# iDRAC

Monitor and manage Dell PowerEdge servers through the iDRAC Redfish API (iDRAC 8 and 9).

Works as an agent skill (OpenClaw, Claude Code and other `SKILL.md`-based agents) or as a standalone shell helper.

**Published skill:** https://clawhub.ai/eddygk/idrac

## What it does

- System summary: model, power state, CPU and memory
- Health checks: temperatures, fans and power supplies
- Hardware inventory, RAID and disk status, BIOS and firmware versions
- System event log (SEL) and Lifecycle Controller logs
- Power operations (on, graceful shutdown, force restart) as documented Redfish calls in `references/endpoints.md`; the skill tells the agent to confirm with the user before any of them

## Requirements

- `curl` and `jq`
- Network access to the iDRAC over HTTPS
- Optional: 1Password CLI (`op`) to fetch credentials

## Setup

Create `~/.config/idrac-skill/config`:

```bash
mkdir -p ~/.config/idrac-skill
cat > ~/.config/idrac-skill/config <<'EOF'
IDRAC_IP="<your-idrac-ip>"
# Credential source: "1password" | "file" | "env"
CREDS_SOURCE="file"
EOF
```

Credential sources:

| Source | Setting | How it works |
|---|---|---|
| `file` (default) | none | Reads `~/.idrac-credentials` (`user:pass`, mode 600) |
| `1password` | `OP_ITEM="<item-name>"` | Reads the username and password with `op`, then caches them to `~/.idrac-credentials` |
| `env` | none | Uses `$IDRAC_USER` and `$IDRAC_PASS` |

## Usage

```bash
scripts/idrac.sh test         # connectivity and authentication
scripts/idrac.sh status       # system summary
scripts/idrac.sh health       # temperatures, fans, power
scripts/idrac.sh inventory    # full hardware inventory
scripts/idrac.sh storage      # RAID and disk status
scripts/idrac.sh logs         # last 10 SEL entries
scripts/idrac.sh thermal      # detailed thermal and fan status
scripts/idrac.sh power        # power state
scripts/idrac.sh reset-types  # available power reset types
```

Redfish endpoint notes are in [`references/endpoints.md`](references/endpoints.md); agent instructions are in [`SKILL.md`](SKILL.md).

## Security notes

- TLS verification is disabled because iDRACs ship with self-signed certificates. Use it only on a trusted management network.
- Credentials never go in this repo or in the config file; keep them in `~/.idrac-credentials` (mode 600), 1Password or environment variables.

## License

MIT, see [LICENSE](LICENSE).

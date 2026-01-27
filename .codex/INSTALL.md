# Installing Omnipowers for Codex

Quick setup to enable omnipowers skills in Codex.

## Installation

1. **Clone omnipowers repository**:
   ```bash
   mkdir -p ~/.codex/omnipowers
   cd ~/.codex/omnipowers
   git clone https://github.com/aaione/omnipowers.git .
   ```

2. **Create personal skills directory**:
   ```bash
   mkdir -p ~/.codex/skills
   ```

3. **Update ~/.codex/AGENTS.md** to include this omnipowers section:
   ```markdown
   ## Omnipowers System

   <EXTREMELY_IMPORTANT>
   You have omnipowers. Omnipowers teach you new skills and capabilities. RIGHT NOW run: `~/.codex/omnipowers/.codex/omnipowers-codex bootstrap` and follow the instructions it returns.
   </EXTREMELY_IMPORTANT>
   ```

## Verification

Test the installation:
```bash
~/.codex/omnipowers/.codex/omnipowers-codex bootstrap
```

You should see skill listings and bootstrap instructions. The system is now ready for use.

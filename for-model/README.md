# Frontend React ERP Template

## File Structure

```
.claude/

└── skills/

    └── frontend-react-erp/

        ├── SKILL.md                        # Entry guide for frontend code generation (main entry point)

        └── references/

            ├── generation.md               # Core generation rules (highest priority)

            ├── eventstorming.md            # Event Storming–based design rules

            ├── fixed-generation.md         # Fixed-pattern code generation rules

            ├── package-structure.md        # Project package structure

            ├── technical-stack.md          # Technology stack and framework guide

            └── test-generation.md          # Test code generation rules

--

Frontend-PRD.txt                              # Module-level requirements template
frontend-setup-prd.txt                        # Single frontend setup and React app configuration
erp-component-prd.txt                         # ERP component creation and integration rules
```

## Usage

### 1. Open Terminal
Open a terminal in your working directory.

### 2. Set Node Version (Node 20+)
```bash
nvm use 22
```

### 3. Run Claude Code
Execute Claude Code in your IDE.

### 4. Add PRD File to Prompt
Add `@Frontend-PRD.txt` to the prompt area and execute.
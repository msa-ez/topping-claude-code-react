---
name: Frontend Generation Guide
description: Integrated guidelines for React-based frontend code generation
---
# Frontend Project Generation Guidelines
## Instruction

This document provides the skillset and guidelines to apply when handling frontend code generation requests based on Frontend-PRD files.

## Request Type Identification

When receiving a code generation request, first check if it is a **Frontend-PRD** request.

## Skill Application Rules

### Frontend-PRD Request (Frontend)

For frontend code generation, refer to all references in the `.claude/skills/react-frontend` directory.

#### Frontend Generation Order

Frontend generation must proceed in the following order, and must be completed without omission according to the detailed sequence specified in the referenced files.

Frontend generation proceeds in the following order:

**Step 1: Generate Frontend Base Structure**
- First read and execute @frontend-setup-prd.md
- Create base structure including package structure, sidebar, routing, etc.
- If frontend structure already exists, only adjust sidebar and main cards
- Frontend generation is always created at root level

**Step 2: Generate Metadata-based Frontend Code and ERP Features**
- Read and execute @.claude/skills/react-frontend/references/generation.md
- Follow all steps in generation.md sequentially (Steps 1-7):
  - Steps 1-6: Metadata-based component generation (Aggregates, Commands, ReadModels, etc.)
  - Step 7: ERP essential features implementation
    - **Phase 1**: Generate ERP feature components (Export/Import, Search/Filter, Activities, Reports, Dashboard)
    - **Phase 2**: Integrate components into appropriate pages
      - Features 7.1~7.3 → Aggregate list pages
      - Features 7.4~7.5 → Main page (HomePage/DashboardPage)
- Complete build and error fixes

**Step 3: Validation and Verification**
- Follow 6-step validation process in @.claude/skills/react-frontend/references/validation-guide.md:
  1. Basic file generation check (Service/Hook/Type/Page)
  2. wireframe.md-based component generation and connection check
  3. Routing configuration check
  4. **ERP component generation AND connection check** (Must verify imports with grep)
     ```bash
     grep -r "import.*ExportDialog" src/pages/
     grep -r "import.*AdvancedSearch" src/pages/
     ```
     If no results found, Step 2 Phase 2 incomplete - Execute connection work immediately
  5. Build and error fixes through `npm run build`
  6. UI operation verification - Check dialog display when Export/Import buttons are clicked

#### Frontend Reference Materials to Apply

1. **Frontend Setup** (Step 1)
   - Description: Frontend base structure and sidebar generation
   - Path: @.claude/skills/react-frontend/references/frontend-setup-prd.md

2. **Generation Reference** (Step 2 - Highest Priority)
   - Description: Complete frontend code generation guide including metadata-based components and ERP essential features
   - Path: @.claude/skills/react-frontend/references/generation.md
   - Includes: Steps 1-6 (Metadata-based generation) + Step 7 (ERP features implementation)

3. **Architecture Reference**
   - Description: Frontend architecture, technology stack, project structure and organization principles
   - Path: @.claude/skills/react-frontend/references/architecture.md

4. **Design System Reference**
   - Description: UI design system and component design rules
   - Path: @.claude/skills/react-frontend/references/design-system.md

5. **Validation Guide** (Step 3)
   - Description: Frontend code validation guidelines and common error solutions
   - Path: @.claude/skills/react-frontend/references/validation-guide.md

## Important Notes
- **Generation Reference**: After Step 1 frontend base structure generation, this must be checked first when generating metadata-based frontend code
- **All Reference Materials Must Be Applied**: All reference files in the directory must be applied without exception
- **Prevent Metadata Omissions and Misreferences**: Files must be generated according to the metadata defined in Frontend-PRD.txt, and unrelated files should not be generated using arbitrary metadata not defined in the document
- **Response Language**: All responses, explanations and communications must be provided in English



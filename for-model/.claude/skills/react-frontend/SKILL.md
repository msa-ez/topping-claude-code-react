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

#### Mandatory Priority: Read generation first
Before starting any code generation work, you must **read the following file first**:
- @.claude/skills/react-frontend/references/generation.md

#### Frontend Generation Order

Frontend generation must proceed in the following order, and must be completed without omission according to the detailed sequence specified in the referenced files.

**Step 1: Generate Frontend Base Structure**
- Generate frontend base structure using the react-frontend-architect agent
- The agent will handle: package structure, sidebar, routing, main layout, configuration files
- If frontend structure already exists, only adjust sidebar and main cards
- Frontend generation is always created at root level

**Step 2: Generate Metadata-based Frontend Code**
- Read and execute @.claude/skills/react-frontend/references/generation.md
- Follow all steps in generation.md sequentially (Steps 1-6):
  - Metadata-based component generation (Aggregates, Commands, ReadModels, etc.)
- Complete build and error fixes

**Step 3: Generate ERP Essential Features**
- Generate ERP feature components using the erp-frontend-architect agent
- The agent will handle component generation, page integration, and self-verification

**Step 4: Validation and Verification**
- Follow validation process in @.claude/skills/react-frontend/references/validation-guide.md:
  1. Basic file generation check (Service/Hook/Type/Page)
  2. wireframe.md-based component generation and connection check
  3. Routing configuration check
  4. Build and error fixes through `npm run build`
  5. UI operation verification

#### Frontend Reference Materials to Apply

1. **Generation Reference** (Highest Priority)
   - Description: Basic rules and procedures for frontend code generation
   - Path: @.claude/skills/react-frontend/references/generation.md

2. **Base Structure Generation Reference**
   - Description: Frontend base structure and main layout implementation
   - Instructions: Use the react-frontend-architect agent to create project configuration, structure, sidebar, and routing

3. **ERP Features Generation Reference**
   - Description: ERP common module design and implementation guide
   - Instructions: Use the erp-frontend-architect agent to create Export/Import, Search/Filter, Activities, Reports, and Dashboard components

4. **Architecture Reference**
   - Description: Frontend architecture, technology stack, project structure and organization principles
   - Path: @.claude/skills/react-frontend/references/architecture.md

5. **Design System Reference**
   - Description: UI design system and component design rules
   - Path: @.claude/skills/react-frontend/references/design-system.md

6. **Validation Guide**
   - Description: Frontend code validation guidelines and common error solutions
   - Path: @.claude/skills/react-frontend/references/validation-guide.md

## Important Notes
- **Generation Reference Always Has Priority**: Must be read before all other references
- **All References Must Be Applied**: All reference files in the relevant directory must be applied without exception
- **Prevent Metadata Omissions and Misreferences**: Files must be generated according to the metadata defined in Frontend-PRD.txt, and unrelated files should not be generated using arbitrary metadata not defined in the document
- **Response Language**: All responses, explanations and communications must be provided in English



# Agent team

I will use GitHub Copilot CLI in a Codespace to orchestrate the custom agent team building Mona's Project Pulse dashboard:

| Agent | Target model | Responsibility | Definition |
| --- | --- | --- | --- |
| **Orchestrator** | Claude Opus 4.7 (copilot) | Coordinates the project, delegates work to specialists, manages dependencies and file ownership, and verifies that the integrated result works together. | `.github/agents/orchestrator.agent.md` |
| **Planner** | Claude Opus 4.7 (copilot) | Researches the repository and requirements, identifies risks and edge cases, and produces an ordered implementation plan with assignments and validation expectations. | `.github/agents/planner.agent.md` |
| **Coder** | GPT-5.5 (copilot) | Implements dashboard logic and other assigned code, follows repository patterns, keeps errors explicit, and validates deterministic, testable behavior. | `.github/agents/coder.agent.md` |
| **Designer** | Gemini 3.1 Pro (copilot) | Shapes the dashboard's UI/UX, accessibility, information hierarchy, responsive behavior, visual clarity, and polished Project Pulse styling. | `.github/agents/designer.agent.md` |

The Orchestrator will use the Planner's research to sequence work, then coordinate the Coder and Designer within explicit file scopes so implementation and design changes can proceed safely.

# Orchestrator Agent Description

**Role:** Gateway & Intake Coordinator  
**Primary Tooling:** n8n Manual Trigger / Chat Node  
**Recommended LLM:** GPT-4o mini or DeepSeek-V3  

### Purpose
The Orchestrator ensures the "Garbage In, Garbage Out" rule is avoided. It acts as a human-to-machine interface that translates natural language desires into structured project briefs.

### Key Interactions
- **User:** Clarifies the app vision.
- **Architect:** Passes the validated ProjectBrief to begin template generation.

### Performance Metrics
- **Clarity:** How well the rephrased idea captures the user's intent.
- **Efficiency:** Number of turns required to reach a validated project brief (Target: < 3).

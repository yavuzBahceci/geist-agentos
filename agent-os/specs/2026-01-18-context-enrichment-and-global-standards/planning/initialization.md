# Spec Initialization

## Raw User Description

What i want is this, i want to update profiles/defaults structure for following. It should effect the following on project loaded agent-os, not in profile/default template structure.

1- during deploy agents, we need to be extracting standards can be applied to all project, not feature or module specific, i don't want to overcrowd standards files. rules can be applied everywhere, like testing strategy, lint rules, namings, error handling, sdd standards especially, so it's like main patterns in project. 

2- What I actually want is once i run spec/implementation commands, for every command what we do actually, we are narrowing the focus but also expanding details and knowledge. What i want to achieve, output of every command, should be going to enrich context with needed patterns and standard for the spec we are working on, it's going to necessary scope and expand it's knowledge by extracting necessary knowledge for the next command, by checking basepoint files, and if necessary going into codebase itself and checking code with the help of basepoint files explaining where they can find necessary information. So when i run orchestrate-tasks or create-tasks or any other spec/implementation command they are creating special context scope narrowed to specific spec and knowledge expanded from sources like basepoints, product, codebase itself navigated from basepoint where to enrich if necessary, code reading especially can be needed for implementation commands, task commands. 

## Spec Path

`agent-os/specs/2026-01-18-context-enrichment-and-global-standards`

## Structure Created

- `planning/` - For requirements and specifications
- `planning/visuals/` - For mockups and screenshots  
- `implementation/` - For implementation documentation

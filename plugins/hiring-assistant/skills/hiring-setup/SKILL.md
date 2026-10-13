---
name: hiring-setup
description: First-time setup for the hiring-assistant plugin in the current project - installs the organization template, creates resume/evaluation/report folders, adds privacy .gitignore rules, then hands off to the setup-wizard agent to configure levels, rubrics, matrices and regional context. Use whenever the user wants to set up, configure, or initialize the hiring framework, add a new organization, or when an evaluation fails because no org config exists.
---

# Hiring Setup

This skill's base directory contains `assets/org-template/` (the reference org config) and `assets/project.gitignore` (privacy rules for candidate data).

1. In the user's project root, if `.org/example/` does not exist, copy `assets/org-template/` from this skill's directory to `.org/example/`. Never edit `.org/example/` afterwards; it is the reference template.
2. Create any missing folders: `resumes/developers/new/`, `resumes/managers/new/`, `evaluations/developers/`, `evaluations/managers/`, `reports/`.
3. If the project is a git repo and `.gitignore` does not already exclude `.org/*`, `resumes/`, `evaluations/` and `reports/`, show the user the relevant block from `assets/project.gitignore` and offer to append it. Candidate data must not be committed.
4. Launch the `setup-wizard` agent, passing it this skill's base directory, to create `.org/<company>/` and walk the user through levels, thresholds, tech stack, rubrics and regional context.
5. Finish by telling the user which level subfolders to create under `resumes/*/new/` (they must match `role_levels` in config.yaml) and that `/hiring-assistant:evaluate-resumes` runs the workflow.

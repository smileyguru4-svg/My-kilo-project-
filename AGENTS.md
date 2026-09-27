# AGENTS.md — Buckeye Platform Autopilot

## Project: Buckeye Rubric-Task Platform

This workspace is a **Buckeye evaluation environment**. It contains a zipped archive of
a harbor/mooring district document set (`workspace (1) (1).zip`), an instructional guide
(`Buckeye instruction (2).docx`), and this configuration.

### What is Buckeye?

Every task on this platform is a **rubric task**:

1. Triage an assigned environment of documents.
2. Write a professional prompt that the environment can support.
3. Build the golden solution that answers it correctly.
4. Repair the auto-generated rubric of criteria.
5. Grade model responses against that rubric.

Target difficulty: strong reference ≤ 0.50 avg, weaker reference ≤ 0.20 avg, ≥ 0.20 gap.

### Autopilot Skill

The **autopilot** skill (`.kilo/skills/autopilot/SKILL.md`) encodes the complete Buckeye
workflow as an operating procedure. Invoke it with `use the autopilot skill` to run a full
rubric task from environment exploration through final scoring with no further human direction.

### Document Environment

The working environment is inside `workspace (1) (1).zip`. Key folders:

| Folder | Content |
|--------|---------|
| audit_liaison/ | Auditor responses, amendment logs, calibration scoring |
| customer_service/ | Briefing notes, complaint logs, offer scripts |
| district_clerk/ | Appeals, minutes, presentations, resolutions |
| facilities_and_slips/ | Slip inventory, basin layouts, maintenance, plans |
| finance_and_billing/ | Aging, billing transactions, late fees, rate study, revenue reports |
| harbor_operations/ | Haul-outs, dock inspections, storm notices, season notices |
| information_systems/ | Access register, change requests, IS memos, system reports |
| moorage_counter/ | Agreements, day logs, measurement certificates |
| public/ | Federal registers, DMV data, tool library inventories |
| scoring_notes/ | Working scratch memos from prior audit passes |
| training_and_certification/ | Training manual, materials, certification records |
| waiting_list_administration/ | Confirmations, letters, exports, offers, skip files, purge cases |

### Allowed file types for task inputs

PNG, JPEG, WebP, GIF, PDF, XLSX, PPTX, DOCX. Convert any CSV/TXT to XLSX. No archives as
task inputs — extract and re-save the relevant files.

## Working Conventions

- All values in deliverables must be traced back to an input file. No fabrication.
- Use a reporting cutoff with full year on every date (e.g. "as of 31 March 2026").
- Golden deliverable must score ≥ 0.97 against its own rubric.
- Rubric must be 30–45 criteria, 3–5 penalty rows, 60–70% Reasoning & Accuracy weight.

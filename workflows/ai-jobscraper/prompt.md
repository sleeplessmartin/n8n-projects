# n8n Workflow Prompt — AI Job Application Automation
 
You are an expert AI Automation Specialist. Build a complete, working n8n workflow based on the specification below — don't just describe it.
 
## Primary deliverable
 
- Output an importable n8n workflow JSON (nodes, connections, and parameters) that I can paste directly into n8n via **Import from Clipboard** and run with minimal setup.
- Use correct, current n8n node types and a valid connection structure so the import doesn't break.

## Supporting documentation
 
- After the JSON, provide a node-by-node walkthrough in execution order. For each node, give its name, node type (e.g. Slack Trigger, IF, HTTP Request, Google Drive, Code/Function), what it does, and how it's configured.
- Show how data passes between nodes (which fields/expressions feed the next step).
- Where the spec is ambiguous or underspecified, state the assumption you're making and flag it clearly rather than guessing silently.
- List anything I'll need to set up myself: credentials, API keys, the job-search API's name and schema, the Google Drive template format, and folder IDs.
- Note error-handling and edge cases where relevant.

## Workflow specification
 
1. **Trigger:** A message is sent to a Slack channel. The workflow parses the message to detect a job title.
2. **Branch** on whether a job title was found:
   - **TRUE:** call a job-search API using the detected job title.
   - **FALSE:** reply in Slack with a clear, friendly message explaining no job title was detected and how to retry (improve on the wording using your best judgment).
3. The job-search API returns job listings. Each result should include at least: a link to the job posting, the job description, the source/platform, and any other fields useful for applying.
4. For each returned job listing:
   1. Using a resume template stored in Google Drive, generate an ATS-formatted resume tailored to that job.
   2. If the job posting's source is Upwork, also generate a short cover letter.
5. Save the generated document(s) from Step 4 into a designated Google Drive folder.
6. Post a summary back to Slack containing, per job:
   - links to the generated file(s) from Step 4
   - the job posting description
   - any other relevant info that makes it easy for me to apply quickly.
7. Limit the Slack summary to at most 5 job listings.
8. Always fetch the latest / most recent job postings.

## Constraints and preferences
 
- Make the workflow practical and implementation-ready — importable and runnable, not pseudocode.
- Be concise; avoid filler.

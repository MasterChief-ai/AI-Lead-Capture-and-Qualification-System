# AI Lead Capture and Qualification System

## Overview
This repository contains the Make.com configuration for an intelligent real estate lead management pipeline. Based on the `AI LEAD CAPTURE AND QUALIFICATION SYSTEM.blueprint.json` file, this system automates the ingestion, evaluation, and distribution of prospect inquiries for PrimeStone Developments, specifically targeting "The Oak Residences".

By leveraging AI, the system evaluates incoming leads based on their stated budget, property interest, and inquiry message, automatically categorizing them into priority tiers to ensure the sales team focuses on high-value prospects first.

## Core Features
* **Automated Data Ingestion:** Captures prospect details (name, phone, email, budget, preferred location, and message) via a custom webhook.
* **AI-Powered Lead Scoring:** Uses the Gemini 2.5 Flash model to analyze lead parameters and output a standardized JSON response containing a Lead Score (0-100), Priority (High, Medium, Low), and a short analytical reasoning.
* **Dynamic Budget Evaluation:** Automatically assesses budgets (Low: < ₦150,000,000, Medium: ₦200,000,000 - ₦350,000,000, High: ₦400,000,000+).
* **Automated CRM Syncing:** Pushes contacts and corresponding deals directly into HubSpot CRM based on their qualified tier.
* **Instant Team Notifications:** Dispatches formatted alerts to specific private Slack channels (e.g., `#hot-leads`, `#medium-leads`) with the AI's summary and direct CRM links.
* **Automated Client Communication:** Sends customized, premium HTML emails to prospects locking in their entry-level pricing for 72 hours and providing a direct WhatsApp connection link.

## System Architecture & Workflow

1. **Webhook Trigger (`gateway:CustomWebHook`):** Listens for incoming property inquiries from front-end lead forms.
2. **AI Qualification (`gemini-ai:simpleTextPrompt`):** The data is fed into Gemini 2.5 Flash, which scores the lead and assigns a priority tier.
3. **Data Parsing (`json:ParseJSON`):** Extracts the structured AI evaluation for conditional routing.
4. **Router (`builtin:BasicRouter`):** Splits the automation into three distinct paths based on the assigned priority:
   * **High Priority:**
     * Logs the lead to a Google Sheet with a task due date of "now".
     * Sends a priority HTML confirmation email.
     * Upserts the contact in HubSpot as a `salesqualifiedlead` and creates a "High Priority Deal".
     * Notifies the team in the `#hot-leads` Slack channel.
   * **Medium Priority:**
     * Logs the lead to a Google Sheet with a task due date of 1 day from creation.
     * Sends a standard HTML confirmation email.
     * Upserts the contact in HubSpot as a `marketingqualifiedlead` and creates a "Medium Priority Deal".
     * Notifies the team in the `#medium-leads` Slack channel.
   * **Low Priority:**
     * Logs the lead to a Google Sheet with a task due date of 3 days from creation.

## Installation and Deployment

1. Download the `AI LEAD CAPTURE AND QUALIFICATION SYSTEM.blueprint.json` file from this repository.
2. Log into your Make.com (Integromat) account and create a new scenario.
3. Use the "More" (three dots) menu at the bottom of the scenario designer and select **Import Blueprint**.
4. Upload the `AI LEAD CAPTURE AND QUALIFICATION SYSTEM.blueprint.json` file.
5. Re-authenticate all module connections (Google Sheets, Gmail, Gemini AI, HubSpot CRM, and Slack) using your organization's credentials.
6. Update the Google Sheets Spreadsheet ID and Slack Channel IDs to match your internal workspace environments.

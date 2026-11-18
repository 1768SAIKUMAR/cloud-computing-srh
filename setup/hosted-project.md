# Course account: a project I host for you

If the Google Cloud free trial sign-up doesn't work for you, send me the Gmail address you want to use. I'll create a project for you on the course billing account, so you don't need a card.

## What you get
- One project, with an ID like `cc-yourname-1234`. I send you the ID.
- Access with the Google account you gave me. You can do everything the workshops need, including the IAM steps in session 1 and enabling APIs.
- You can't create other projects, or see or change billing and budgets. That's on purpose.

## Getting started
1. Open [console.cloud.google.com](https://console.cloud.google.com), signed in with the Google account you gave me.
2. In the project picker at the top, choose your project.
3. In Cloud Shell, point `gcloud` at it:

   ```bash
   gcloud config set project YOUR-PROJECT-ID
   ```

4. Follow the workshops. Where a step differs, use the **Course account** version:
   - Skip the free-trial sign-up and the budget alert. Your project already has a cap.
   - Your project ID is the one I sent you. Use it wherever a workshop says `project_id`.

## The spending cap
Your project can spend up to €50 a month. If it passes that, billing is switched off and everything in the project stops until I switch it back on. Budget figures lag by a few hours, so you won't see a warning in time.

Treat teardown steps as essential: stop Dataflow jobs and delete clusters at the end of every session.

You can't open the billing pages, so if you want to know what you've spent so far, ask me.

## If something goes wrong
- **You can't see the project:** check you're signed in with the Google account you gave me. The account switcher is at the top right.
- **`Billing account for project is not found`** or **`billing is disabled`**: your project hit its cap. Tell me.
- **`API not enabled`**: the error message contains a link. Open it, enable the API, wait a minute, and try again. You have the rights to do this.
- **`Permission denied`** on something a workshop asks for: tell me which step. I'll add the role.

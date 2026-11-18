# Google Cloud setup

You need one Google Cloud project for the whole module. There are two ways to get one, and every workshop works with both:

| | Own account | Course account |
|---|---|---|
| Who it's for | Everyone, by default | Anyone whose free-trial sign-up fails |
| Card | Needed to verify your identity; not charged | Not needed |
| Credit | $300 (about €269) for 90 days | Up to €50 a month, paid by the course |
| Spending limit | A budget alert you set: emails only | A hard cap I set: billing stops at €50 |
| Who creates the project | You | I do, and send you its ID |

Where a workshop step differs, it shows both versions, labelled **Own account** and **Course account**. Everything else is the same for everyone.

## Own account: the free trial

1. Go to cloud.google.com and click **Get started for free**.
2. Sign in with a personal Google account.
3. Choose your country and the individual account type, then add a card. It verifies your identity; nothing is charged unless you upgrade.
4. You get $300 (about €269) of credit for 90 days.
5. Search **Budgets and alerts**, create a budget of €50, keep the 50/90/100% alerts.
6. Create a project called `cc-<your-name>`.

Budgets only send emails; they don't stop anything. Stop Dataflow jobs and scale clusters down at the end of every session.

## Course account: a project I host for you

If the free-trial sign-up doesn't work for you, send me the Gmail address you want to use. Then follow [hosted-project.md](hosted-project.md).

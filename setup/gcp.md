# Google Cloud setup

You need one Google Cloud project for the whole module, on your own account with the free trial.

## The free trial

1. Go to cloud.google.com and click **Get started for free**.
2. Sign in with a personal Google account.
3. Choose your country and the individual account type, then add a card. It verifies your identity; nothing is charged unless you upgrade.
4. You get $300 (about €269) of credit for 90 days.
5. Search **Budgets and alerts**, create a budget of €50, keep the 50/90/100% alerts.
6. Create a project called `cc-<your-name>`.

Budgets only send emails; they don't stop anything. Stop Dataflow jobs and scale clusters down at the end of every session.

## Cloud Shell tips

Most workshops run in Cloud Shell. It keeps everything in your home folder (`~`) between sessions, and Terraform is already installed. You should never need to install anything. Do these once:

1. **Turn off ephemeral mode.** In the Cloud Shell toolbar, open the **⋮** menu and check **Ephemeral mode** is off. If it's on, your home folder is wiped when the session ends, and you lose the repo, your `terraform.tfvars` and your Terraform state.
2. **Share Terraform's downloads between sessions.** Each session's `starter/` folder is its own Terraform project, so `terraform init` downloads the Google provider again in each one. Run this once and it downloads only once:

   ```bash
   mkdir -p ~/.terraform.d/plugin-cache && echo 'plugin_cache_dir = "$HOME/.terraform.d/plugin-cache"' > ~/.terraformrc
   ```

3. **Don't install Terraform yourself.** Anything installed outside your home folder, with `apt` or a downloaded file, is gone the next time Cloud Shell starts. Run `terraform version` to see the one that's already there.

If your repo folder has disappeared, run `git clone` again. If `terraform apply` then says a bucket or table **already exists**, Terraform has lost its state: tell me, and we'll bring your resources back under Terraform with `terraform import`.

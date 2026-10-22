# ci-workflows
Shared CI workflows

## Email notification
`notify_email.yml` sends an email if any job of the calling workflow run failed or was cancelled.
Add it as the last job of a workflow:

```yaml
  notify:
    name: Email notification
    # Wait for all other jobs
    needs: [job_a, job_b]
    # Only run in main repo and even if previous step failed
    if: github.repository_owner == 'stormchecker' && always()
    uses: stormchecker/ci-workflows/.github/workflows/notify_email.yml@v1
    with:
      # Optional, used in the email subject
      title: CI
    secrets: inherit
```

The secrets `STORM_CI_MAIL_SERVER`, `STORM_CI_MAIL_USERNAME`, `STORM_CI_MAIL_PASSWORD` and `STORM_CI_MAIL_RECIPIENTS` must be available to the calling repository.

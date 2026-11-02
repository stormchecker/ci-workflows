# ci-workflows
Shared CI workflows

## Email notification
`notify_email.yml` sends an email if any job of the calling workflow run failed, was cancelled or timed out.
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
    permissions:
      actions: read
```

The secrets `STORM_CI_MAIL_SERVER`, `STORM_CI_MAIL_USERNAME`, `STORM_CI_MAIL_PASSWORD` and `STORM_CI_MAIL_RECIPIENTS` must be available to the calling repository.

## Docker release
`release_docker.yml` builds a Release (`<tag>`) and a Debug (`<tag>-debug`) Docker image for `linux/amd64` and `linux/arm64` and pushes them to DockerHub.

Example call:

```yaml
  release:
    uses: stormchecker/ci-workflows/.github/workflows/release_docker.yml@v1
    with:
      image: movesrwth/storm
      tag: ${{ inputs.tag }}
      dockerfile: .github/workflows/Dockerfile.release
      # Additional build arguments for both variants (one per line)
      build_args: |
        BASE_IMAGE=movesrwth/storm-dependencies:latest
      # Additional build arguments for a single variant
      debug_build_args: developer=ON
      release_build_args: developer=OFF
    secrets: inherit
```

The build arguments `build_type` (`Debug`/`Release`) and `no_threads` (default `4`) are always passed.
The secrets `STORM_CI_DOCKER_USERNAME` and `STORM_CI_DOCKER_TOKEN` must be available to the calling repository.

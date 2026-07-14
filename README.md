# VouchSpec publisher-CI demo

[![VouchSpec publisher evidence](https://github.com/mordiaky/vouchspec-demo/actions/workflows/vouchspec.yml/badge.svg)](https://github.com/mordiaky/vouchspec-demo/actions/workflows/vouchspec.yml)

This is the smallest complete publisher-side integration for
[VouchSpec](https://github.com/mordiaky/vouchspec). It checks the exact repository commit,
statically inspects a passing and an intentionally structurally failing public Agent Skill
without executing either, creates receipt drafts and workflow-binding requests, attests the
files with GitHub's OIDC-backed artifact attestation service, and uploads them as workflow
artifacts.

Every third-party action is pinned to a full commit SHA. The sample workflow is in
[`.github/workflows/vouchspec.yml`](.github/workflows/vouchspec.yml); adopters only need to
change `skill-path`.

## What the workflow produces

- Per matrix case, `receipt.json`: a deterministic VouchSpec receipt draft bound to the repository, full
  commit, skill path, and exact directory digest.
- `publisher-ci-request.json`: the repository, workflow, run, action ref, commit, and
  receipt hash that VouchSpec can independently verify.
- Two GitHub artifact attestations per case: publisher workflow provenance for those exact files.

The uploaded files remain available from the successful workflow run for 30 days. GitHub's
attestation pages remain linked from the job summary.

## Verify an attestation

After downloading either evidence file from a successful run, verify its GitHub provenance:

```bash
gh attestation verify receipt.json --repo mordiaky/vouchspec-demo
gh attestation verify publisher-ci-request.json --repo mordiaky/vouchspec-demo
```

Then inspect the JSON and confirm that the repository, commit, workflow, action ref, and
receipt hash match the run you intended to trust. VouchSpec's independent verifier and
lifecycle checks apply after VouchSpec issuer-signs an accepted receipt.

## Evidence boundary

The two cases prove that both a structural pass and an explicit structural failure remain
attestable evidence; the workflow asserts each expected status. This workflow establishes
exact-byte static-inspection and publisher-workflow provenance.
It does not execute the skill, verify publisher identity, observe runtime behavior, prove
compatibility, guarantee security, or certify safety. A structural failure is still a useful
result when reported accurately.

This repository is controlled by the VouchSpec operator. It is a public demonstration only
and is deliberately excluded from external-adoption and commercial-use metrics.

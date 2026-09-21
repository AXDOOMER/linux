# CircleCI Setup

This project is followed on CircleCI and linked to `AXDOOMER/linux`. Setup
Workflows (`advanced.setup_workflows`) and the `GITLAB_TOKEN` project
environment variable were both enabled/created via the CircleCI API — no
manual dashboard steps were required.

`.circleci/config.yml` is a minimal "setup" config (`setup: true`). Its single
job clones the private
`arctiq-team/alexandrelabonte/ci-pipeline-generation/ci-pipeline-generator`
GitLab repository using `GIT_ASKPASS` (populated from the `GITLAB_TOKEN` env
var), builds it with `go build` inside `golang:1.25`, and runs it with
`CIRCLECI=true` to produce `generated-config.yml`. The official
`circleci/continuation` orb then posts that file to the pipeline continuation
API, which replaces this setup config with the real, dynamically generated
workflow for the rest of the pipeline.

Unlike the Buildkite bootstrap (which uploads the generated YAML directly via
`buildkite-agent pipeline upload`), CircleCI requires "Setup Workflows" to be
enabled on the project and uses the continuation API/orb instead — the
`CIRCLE_CONTINUATION_KEY` needed to authenticate that call is injected
automatically by CircleCI once Setup Workflows is on, so no personal API
token needs to be stored as a project secret.

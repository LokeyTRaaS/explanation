# Lokey explanation

[![CI](https://img.shields.io/github/actions/workflow/status/LokeyTRaaS/explanation/presentation.yml?branch=main&label=CI)](https://github.com/LokeyTRaaS/explanation/actions/workflows/presentation.yml)
[![Release](https://img.shields.io/github/v/release/LokeyTRaaS/explanation?label=release)](https://github.com/LokeyTRaaS/explanation/releases)
[![semantic-release](https://img.shields.io/badge/semantic--release-conventionalcommits-e10079?logo=semantic-release)](https://github.com/semantic-release/semantic-release)

This repository is the slide deck for the Lokey explanation. [Lokey](https://lokey.cloud) feeds hardware randomness into the stack you already run: firmware, the kernel, virtio, and containers.

The talk treats randomness as a process, not a sample. The claim is available when every step from the noise to the byte can be read, no closed step sits in that path, and you run the process yourself. CPU and GPU generators, a cold kernel pool, and virtio hand you bytes from a process you did not run. Lokey is the software that feeds those same consumers from a process you operate. What integrates today sits on a closed NIST SP 800-90 part. The open circuit is what meets the requirement.

The deck source is [`presentation/lokey.md`](presentation/lokey.md), styled by [`presentation/lokey-theme.css`](presentation/lokey-theme.css).

## Build

The build uses Node.js, [Task](https://taskfile.dev), and Chrome. Chrome renders the PDF.

```sh
task html   # presentation/lokey.html
task pdf    # presentation/lokey.pdf
```

`task` runs both. The HTML file is kept in the repository. The PDF is generated and is not committed.

## Releases

Pull requests and pushes to `main` render the PDF in GitHub Actions. A push to `main` whose commits call for a release publishes a GitHub Release and attaches the PDF as `lokey-explanation-vX.Y.Z.pdf`.

Versions follow [conventional commits](https://www.conventionalcommits.org/), applied by semantic-release:

- `feat` releases a minor version
- `fix` releases a patch version
- `BREAKING CHANGE` releases a major version

A push with none of those does not publish a release. With no previous tag, the first release is `v1.0.0`.

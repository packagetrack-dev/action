# PackageTrack Action

Pushes the dependency set of a build to [PackageTrack](https://packagetrack.dev)
from GitHub Actions.

## Usage

```yaml
- uses: packagetrack-dev/action@v1
  with:
    api-key: ${{ secrets.PACKAGETRACK_API_KEY }}
```

Create an API key at [packagetrack.dev/app/keys](https://packagetrack.dev/app/keys)
and store it as a repository secret.

## Inputs

| Input | Default | Description |
|---|---|---|
| `api-key` | required | PackageTrack API key. |
| `project` | `.` | Directory containing the lock file. |
| `name` | directory name | Project name in PackageTrack. |
| `ecosystem` | all | `pypi` or `npm`, to read only one lock file type. |
| `server` | `https://packagetrack.dev` | Server URL, for self-hosted installs. |
| `dry-run` | `false` | Print the payload and send nothing. No key needed. |

## Examples

Monorepo, one project per lock file:

```yaml
- uses: packagetrack-dev/action@v1
  with:
    api-key: ${{ secrets.PACKAGETRACK_API_KEY }}
    project: services/api
    name: api
- uses: packagetrack-dev/action@v1
  with:
    api-key: ${{ secrets.PACKAGETRACK_API_KEY }}
    project: apps/web
    name: web
```

Run on lock file changes and on a weekly schedule:

```yaml
on:
  push:
    branches: [main]
    paths: ["**/uv.lock", "**/package-lock.json", "**/requirements.txt"]
  schedule:
    - cron: "0 6 * * 1"
```

## Behavior

The action installs [packagetrackdev](https://github.com/packagetrack-dev/packagetrackdev)
with `install.sh` and runs `packagetrackdev push`. The payload is package
names, versions and their parent packages; no source code or file paths.
Use `dry-run: true` to see it in the log.

Outdated dependencies do not fail the step. It fails only when the push
itself cannot be made: missing key, no lock file, or the server unreachable.

## License

MIT

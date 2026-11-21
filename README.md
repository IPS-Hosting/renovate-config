# renovate-config
[Renovate](https://docs.renovatebot.com/) configuration preset used at IPS Hosting.
See https://docs.renovatebot.com/config-presets/#shareable-config-presets.

## Useful links:
* Renovate docs: https://docs.renovatebot.com
* Default Presets: https://docs.renovatebot.com/presets-default/

## Usage

Extend from this preset in your `renovate.json` configuration:

```json
{
	"extends": ["github>IPS-Hosting/renovate-config"]
}
```

## Update policy

The preset aims for few, predictable pull requests without slowing down security fixes:

| Updates | When | Pull requests |
| --- | --- | --- |
| Security fixes (GitHub and osv.dev advisories) | Immediately, no minimum release age | One per dependency, labelled `security` |
| Minor, patch, digest and pin updates | Mondays before 6am (Europe/Berlin) | One per ecosystem (see below) |
| Major updates | Mondays before 6am | One per dependency, only for the newest major version |
| Lock file maintenance | Mondays before 6am | One |

Non-major updates are grouped per ecosystem:

- `npm dependencies (non-major)` (including Node.js and pnpm versions)
- `Go modules (non-major)`
- `Python dependencies (non-major)`
- `Ansible (non-major)`
- `Nix (non-major)`
- `GitHub Actions (non-major)`
- `container images and Helm charts (non-major)`: every Docker image and Helm chart, whichever file references it
- `other dependencies (non-major)`: everything else, for example versions tracked by custom regex managers

All updates wait for a minimum release age of 3 days.

Pull requests are only rebased when they conflict with the base branch (`rebaseWhen: conflicted`), so that a merge to
the base branch does not trigger a CI run for every open pull request.

Automerge is enabled for stable non-major updates, digests, lock file maintenance and linters, testers and types,
as long as all status checks pass. A group is merged automatically only if every update in it may be automerged.

## Adapting a repository

Package rules of a repository are applied after the ones of this preset, so a repository can override any of the
above. Common adaptions:

- **Keep related packages together**: a rule with `groupName` applies to every update type and therefore takes
  the matching packages out of the ecosystem group. To keep only their major updates together, add
  `"matchUpdateTypes": ["major"]`.
- **Split a group**, e.g. one npm group per `package.json`, by a rule with `matchFileNames` and its own `groupName`.
- **Disable automerge** for some updates with a rule that sets `"automerge": false`.
- **A different schedule** for a repository or package: set `schedule` at the top level or in a package rule.
  Security fixes are not affected by it.

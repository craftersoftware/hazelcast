# CrafterCMS Hazelcast

This is CrafterCMS's fork of Hazelcast OSS. Hazelcast doesn't publish patch releases for the
open-source edition, so this fork exists only to ship CVE fixes on top of upstream releases.
It is published as `org.craftercms.hazelcast:*` (extensions: `org.craftercms.hazelcast.jet:*`) to
Sonatype Central. Deployment is manual and is not done from CI (see below).

## The rule: don't deviate from upstream Hazelcast

The code must match upstream Hazelcast, apart from security fixes. Don't change runtime or test
code to add features, refactor, tidy up, or work around problems, including test or CI failures.

Allowed:
- **CVE and security fixes**: preferably cherry-picks of upstream commits (`git cherry-pick -x`),
  and dependency bumps that fix a CVE. When there's no upstream fix, keep the patch as small as
  possible and document it.
- **Build, packaging and release**: the groupId, version, `distributionManagement`, which modules
  are published (only Apache-2.0 artifacts are published; HCL modules set `maven.deploy.skip`),
  `.mvn/`, `.github/`, `tag-release.sh`, and docs such as this one.

When something fails, fix it in CI or build configuration, not in the code. For example, the
GitHub workflow blocks the cloud metadata address instead of changing the discovery code. If a
code change seems unavoidable, stop and ask first.

## Branches and versions

- `master` tracks upstream (`upstream` remote: hazelcast/hazelcast).
- `release/<major>.<minor>` (e.g. `release/5.7`) is cut from the upstream release tag (`v5.7.0`)
  and carries the fork's changes.
- Versions are `X.Y.Z-craftercms-N` (snapshots: `-craftercms-N-SNAPSHOT`). Hazelcast parses its own
  version at runtime, so don't use four-part versions.
- Release tags are created by `./tag-release.sh -p <version>`. CI (`.github/workflows/build.yml`)
  only builds and tests; it doesn't deploy. Releases are deployed manually from the tag with
  `./mvnw clean deploy -Prelease -Penable-maven-central`, using the `central-releases` server
  credentials in `~/.m2/settings.xml`, as for CrafterCMS mariaDB4j.
- Keep `com.hazelcast.*` package names and XML namespaces unchanged for compatibility.

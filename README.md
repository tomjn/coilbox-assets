# coilbox-assets

The durable asset tier for [coilbox-hub](https://github.com/tomjn/coilbox-hub). It holds images extracted from games and maps - minimaps, buildpics and unit renders - and publishes them through GitHub Pages at https://tomjn.github.io/coilbox-assets/.

Automation writes to this repo. Do not add files by hand.

None of these images are original work by coilbox, and this repository is a mirror rather than a licensor. Read [NOTICE.md](NOTICE.md) before reusing anything, and for how to ask for a file to be taken down.

Assets are content addressed, so a file's contents determine its path and a path never points at different bytes later. The hub stores that path on its own, never a full URL, and puts the host in front of it from a single configuration value. Moving to another host, or serving from the app itself, is a change to that one value rather than a data migration.

## Publishing

Every push to `main` deploys the whole repo with the workflow in `.github/workflows/pages.yml`. This is a custom Actions workflow rather than the default branch build, which caps out at ten builds an hour - too few for a scheduled job that pushes new assets.

## Promotion

`.github/workflows/promote.yml` runs daily and is what puts assets here. It moves anything the hub approved more than a day ago out of the hub's staging area: it commits the objects here, waits until Pages is serving them, tells the hub the pictures moved, and only then asks the hub to delete the staged copies. The job itself lives in [coilbox-hub](https://github.com/tomjn/coilbox-hub) at `scripts/promote-assets.ts`, and this repo checks it out to run it.

It needs two things set on this repository, and neither is optional:

- `PROMOTION_TOKEN`, a repository secret. Opens the hub's `/api/v1/promotion` routes and nothing else. `php artisan promotion:token` on the hub makes one.
- `HUB_URL`, a repository variable rather than a secret, because it is the hub's public address: `https://hub.coilbox.org`.

`.nojekyll` at the root turns off Jekyll. Without it Pages would skip files and directories whose names begin with an underscore, and would work through a few hundred megabytes of binaries for nothing.

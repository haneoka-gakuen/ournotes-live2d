# Our Notes Live2D

Complete portable Live2D models for BanG Dream! Our Notes (JP and Intl).

[![Dataset publication](https://github.com/haneoka-gakuen/ournotes-live2d/actions/workflows/release.yml/badge.svg)](https://github.com/haneoka-gakuen/ournotes-live2d/actions/workflows/release.yml)

## Data layout

Each model folder contains its standard `<model-id>.model3.json`, original `.moc3`, all referenced textures, motions, expressions and physics. Shared website textures are copied into the model folder and references become local relative paths. `index.json` lists every model. The model descriptor remains Cubism Model3 format with `Version: 3`; MOC version and binary contents are preserved.

The root `manifest.json` is the single version record for both servers. It records the Master generation, its content hash, the dataset SHA-256 and file count. Operational logs and retry state stay in the producer's private control namespace, outside this repository.

## Use

```sh
git clone https://github.com/haneoka-gakuen/ournotes-live2d.git
cd ournotes-live2d
```

```sh
python tools/package.py jp 001_casual_spring_01
# Unzip 001_casual_spring_01.zip into your game/figure directory.
# WebGAL scene:
# changeFigure:001_casual_spring_01/001_casual_spring_01.model3.json;
```

For one server, use Git sparse checkout:

```sh
git clone --filter=blob:none --sparse https://github.com/haneoka-gakuen/ournotes-live2d.git
cd ournotes-live2d
git sparse-checkout set jp tools
```

## WebGAL

Extract a model ZIP into `game/figure`, preserving its model folder, then call its `.model3.json`:

```text
changeFigure:001_casual_spring_01/001_casual_spring_01.model3.json -motion=mtn_smile01_C -expression=exp_bsmile01;
```

Motion files also have standard named motion groups so Terre can list and select them. The original motion groups are retained; an `Idle` group is supplied when the source contains idle motions. Textures and all other references are local to the model folder. Terre's ordinary asset uploader saves files; unzip the package before placing it in the figure directory.

Supply the Live2D runtimes according to [WebGAL's setup guide](https://docs.openwebgal.com/en/live2D.html). For models requiring a larger Core heap, use WebGAL's `Cubism_Memory_Reserved_Size_MB` game setting. This setting belongs to the host; the model retains the standard Cubism format.

For a one-model download without cloning the corpus, run the standalone packaging command in an empty directory:

```sh
python tools/package.py jp 001_casual_spring_01 --fetch
```

The optional `--fetch` mode requires curl with HTTP/2 support. It pins a public Haneoka release and verifies every downloaded file against its SHA-256 before producing the ZIP. Add `--resume` after an interrupted fetch; matching local files are verified and reused.

## Snapshots and downloads

`main` contains complete published datasets. Pin a `jp-<master-version>` or `intl-<master-version>` tag for a reproducible snapshot. The version follows the Master generation. The Master content identity is stored separately as `hash`.

[GitHub Releases](https://github.com/haneoka-gakuen/ournotes-live2d/releases) provide complete archives and `SHA256SUMS`:

```sh
sha256sum -c SHA256SUMS
# For a split archive, concatenate parts in filename order:
cat jp.tar.gz.part* > jp.tar.gz
tar -xzf jp.tar.gz
```

Validate a local full checkout with `python tools/release.py --tag jp-1.1.0.200 --check-only`. This verifies the complete file digest and local Cubism dependency closure where applicable.

## Automatic updates

[Our Notes Data Pipeline](https://github.com/haneoka-gakuen/ournotes-data-pipeline) checks official Master and MoeNotes version signals every 20 minutes and exports successful Haneoka resource builds. This repository consumes the sealed feed with its own short-lived `GITHUB_TOKEN`; it needs no cross-repository credentials. Data commits occur when the dataset or its version record changes. Every file is verified before the complete snapshot reaches `main`; release tags never request another resource build.

Each export uses one fixed, hash-verified production resource release. JP and Intl publish independently; a failed export preserves the previous complete snapshot.

## Related projects

[Masterdata](https://github.com/haneoka-gakuen/ournotes-masterdata) · [Stories](https://github.com/haneoka-gakuen/ournotes-stories) · [Charts](https://github.com/haneoka-gakuen/ournotes-charts) · [Live2D](https://github.com/haneoka-gakuen/ournotes-live2d) · [Spine](https://github.com/haneoka-gakuen/ournotes-spine) · [Resource catalog](https://github.com/haneoka-gakuen/ournotes-resource-catalog) · [Automation](https://github.com/haneoka-gakuen/ournotes-data-pipeline)

Maintained by [Haneoka](https://haneoka.org). See [NOTICE.md](NOTICE.md) for game-content attribution and [LICENSE](LICENSE) for the helper-code license.

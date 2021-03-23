# Data Converge Outputs -> Frictionless Migration

## Installation

Validation was performed using [frictionless-py](https://github.com/frictionlessdata/frictionless-py) v4.2.1. ~~As of writing (1/13/21), `5bd794179dcbaab080e1e0d0e692b6c875e44d43` is the only stable commit that avoids [#609](https://github.com/frictionlessdata/frictionless-py/issues/609) and [#616](https://github.com/frictionlessdata/frictionless-py/issues/616).~~

## Validate Using Frictionless CLI

```bash
$ frictionless validate datapackage.json
```

## Pull Local Copy of Examplary Dataset

```bash
$ scp -r eho@stampede2.tacc.utexas.edu:/work/projects/SD2E-Community/prod/projects/sd2e-project-43/reactor_outputs/complete/NovelChassis-EcN-WT-1/20210310165236 ./data
```

# Data Converge Outputs -> Frictionless Migration

## Installation

Validation was performed using [frictionless-py](https://github.com/frictionlessdata/frictionless-py) v4.2.1. ~~As of writing (1/13/21), `5bd794179dcbaab080e1e0d0e692b6c875e44d43` is the only stable commit that avoids [#609](https://github.com/frictionlessdata/frictionless-py/issues/609) and [#616](https://github.com/frictionlessdata/frictionless-py/issues/616).~~

## Validate Using Frictionless CLI

```bash
frictionless validate datapackage.json
```

## Pull Local Copy of Examplary Dataset

NovelChassis-EcN-WT-1:
```bash
scp -r eho@stampede2.tacc.utexas.edu:/work/projects/SD2E-Community/prod/projects/sd2e-project-43/reactor_outputs/complete/NovelChassis-EcN-WT-1/20210310165236 ./data
```

Duke-YeastSTATES-Ethanol-Time-Series-LiveDeadClassification:
```bash
scp -r eho@stampede2.tacc.utexas.edu:/work/projects/SD2E-Community/prod/projects/sd2e-project-43/reactor_outputs/preview/Duke-YeastSTATES-Ethanol-Time-Series-LiveDeadClassification/20201217201845 ./data
```

YeastSTATES-Activator-Circuit-Dox-Growth-Curves-30C:
```bash
scp -r eho@stampede2.tacc.utexas.edu:/work/projects/SD2E-Community/prod/projects/sd2e-project-43/reactor_outputs/complete/YeastSTATES-Activator-Circuit-Dox-Growth-Curves-30C/20210211174214 ./data
```

NovelChassis-Bacillus-WT-Characterization:
```bash
scp -r eho@stampede2.tacc.utexas.edu:/work/projects/SD2E-Community/prod/projects/sd2e-project-43/reactor_outputs/complete/NovelChassis-Bacillus-WT-Characterization/20201029172044 ./data
```

NovelChassis-Endogenous-Promoter:
```bash
rsync -av --progress eho@stampede2.tacc.utexas.edu:/work/projects/SD2E-Community/prod/projects/sd2e-project-43/reactor_outputs/preview/NovelChassis-Endogenous-Promoter/20210318183648/ ./data --exclude "*events.json"
```


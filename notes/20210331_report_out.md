# Data Converge Frictionless Migration Report Out

## Overall Goal

Prototype migration of Data Converge (DC) outputs to Frictionless.

## Why DC before PDT?

- Data Converge (DC) pipeline was written by fewer authors than Precomputed Data Tables (PDT), and has a more stable schema.
- Neither has explicitly defined schemas, though they are implicit in the source code

## Status Quo

- Two DC runs in the Data Package
- Passing validation
- Schemas inferred for all data types handled by DC:
```jsonc
"resources": [
    {
        "name": "platereader",
        // ...
        "schema": "schemas/dc_platereader.json",
        "description": "Platereader data. Data type 'P' in Experiment Analysis Dashboard."
    },
    {
        "name": "rnaseq_meta",
        // ...
        "schema": "schemas/dc_rnaseq.json",
        "description": "RNAseq data. Data type 'R' in Experiment Analysis Dashboard."
    },
    {
        "name": "cfu",
        // ...
        "schema": "schemas/cfu.json",
        "description": "CFU data. Data type 'C' in Experiment Analysis Dashboard."
    },
    {
        "name": "fc_meta",
        // ...
        "schema": "schemas/fc_meta.json",
        "description": "Flow cytometry metadata. Data type 'F' in Experiment Analysis Dashboard."
    },
    {
        "name": "fc_raw_log10_stats",
        // ...
        "schema": "./schemas/fc_raw_log10_stats.json",
        "description": "Flow cytometry counts data. Data type 'F' in Experiment Analysis Dashboard."
    }
]
```

## Pain Points

- DC outputs **are not 1NF**
- For example, instance of [duplicate primaryKey](https://jupyter.sd2e.org/user/%7buser%7d/edit/sd2e-projects/sd2e-project-43/reactor_outputs/complete/NovelChassis-Bacillus-WT-Characterization/20201029172044/NovelChassis-Bacillus-WT-Characterization__platereader.csv):
```bash
$ csvcut -c 'sample_id,experiment_id,replicate,replicate_group,strain' data/20201029172044/NovelChassis-Bacillus-WT-Characterization__platereader.csv | csvlook --max-rows 2
| sample_id                                      | experiment_id           | replicate | replicate_group                          | strain       |
| ---------------------------------------------- | ----------------------- | --------- | ---------------------------------------- | ------------ |
| sample.ginkgo.22181358.experiment.ginkgo.29649 | experiment.ginkgo.29649 |         1 | ee9a613bc9af7a4e5adaab1fff7a39292ba6efea | MediaControl |
| sample.ginkgo.22181358.experiment.ginkgo.29649 | experiment.ginkgo.29649 |         1 | ee9a613bc9af7a4e5adaab1fff7a39292ba6efea | MediaControl |
| ...  
```

## Downstream Propagation

Unfortunately but predictably, these issues propagate to the [downstream PDT analyses](https://jupyter.sd2e.org/user/%7buser%7d/edit/sd2e-projects/sd2e-project-48/complete/NovelChassis-Bacillus-WT-Characterization/20201106211408/xplan-od-growth-analysis/pdt_NovelChassis-Bacillus-WT-Characterization__pr_growth_analysis.csv):
```bash
$ csvcut -c 'sample_id,experiment_id,replicate,replicate_group' data/20201029172044/pdt_NovelChassis-Bacillus-WT-Characterization__pr_growth_analysis_head.csv | csvlook
| sample_id                                      | experiment_id           | replicate | replicate_group                          |
| ---------------------------------------------- | ----------------------- | --------- | ---------------------------------------- |
| sample.ginkgo.22181362.experiment.ginkgo.29649 | experiment.ginkgo.29649 |      True | 2d5da74bcfff4e714ab3b89b541d790bc20b3db2 |
| sample.ginkgo.22181362.experiment.ginkgo.29649 | experiment.ginkgo.29649 |      True | 2d5da74bcfff4e714ab3b89b541d790bc20b3db2 |
```

## More Pain Points

[Field duplication due to Python float precision](https://jupyter.sd2e.org/user/%7buser%7d/edit/sd2e-projects/sd2e-project-43/reactor_outputs/preview/NovelChassis-Endogenous-Promoter/20210318183648/NovelChassis-Endogenous-Promoter__fc_etl_stats.csv):
```bash
$ csvcut -n ./data/20210318183648/NovelChassis-Endogenous-Promoter__fc_etl_stats.csv
# shows columns in CSV
  1: sample_id
...
 10: bin_-0.9499999999999998
 11: bin_-0.8500000000000001
 12: bin_-0.7499999999999999
 13: bin_-0.6499999999999998
 14: bin_-0.5499999999999999
 15: bin_-0.4499999999999999
 16: bin_-0.34999999999999987
 17: bin_-0.24999999999999992
 18: bin_-0.15
 19: bin_-0.04999999999999998
...
```

## Pain Points Continued

- These are fixable! 
- No workaround I know of besides:
    - Fix normalization error ≥twice, in both upstream (DC) and downstream (PDT)
    - Fix upstream and rerun all downstream
- But it leaves one to wonder what the depth and breadth of these normalization issues are
- This makes it difficult to assess cost of paying off the technical debt

## Long story short...

In order to transition the entire corpus of DC or PDT outputs to a small number of schemas...

...one must first pay the **data normalization toll**

![Troll bridge](https://external-preview.redd.it/1XFssZg0rSpHWBC04TIC2XIHk-R9Up8-pj2UGDoSUk0.jpg?width=960&crop=smart&auto=webp&s=4b13d2187dd8c26e2c0375e34e212fd9ed7dd300)

## CLI Ideas

- Going through this process twice sparked some ideas about how we could harden this workflow.
- In the case of DC, the goal is essentially to glob for `*.csv` in a set of `archivePath`s, and include these paths in the field `resources[].path`
- If the files are local, Frictionless makes this easy thanks to globbing:
```jsonc
"resources": [
    {
        "path": "data/**/*platereader.csv",
        "name": "platereader"
        // ...
```

## CLI Ideas

- If we want to integrate with remote files via Tapis, we could assume the directory structure from `builder init`
```bash
datasets builder add agave://data-sd2e-projects.sd2e-project-43/path/to/rnaseq.csv
```
1. Pull using `tapis files download`
2. Infer schema
3. Add a new Resource to the `datapackage.json`

## Next Steps

- Starting from minimum viable (single experiment result in Package), demo the Frictionless SQL interface.
- SQL toolbox might help with normalization woes, though I'm not optimistic

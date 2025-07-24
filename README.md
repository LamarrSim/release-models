# release-models
Set of actions to train, compile and release Lamarr models

## Prerequisites
This workflow expects a Snakemake workflow ultimately producing a C file with a transpiled model. 

The workflow can be placed in an arbitrary subdirectory of the package, and an arbitrary self-hosted runner can be used for executing the workflow.

This workflow relies on the [action `landerlini/apptainer-gha-runner`](github.com/landerlini/apptainer-gha-runner) to actually execute the workflow, which implies you can 
customize the Snakemake execution by editing the profile. 

Finally, you can pass secrets from GitHub formatted as YAML or JSON file to the configuration key `secrets.SECRETS_YAML`.  Those are installed somewhere in the Snakemake running environment and the file is passed to Snakemake as a configuration.
Which implies you can read the secrets from Snakemake by issuing

``` py
import yaml
with open(config['secrets_file']) as secrets_file:
    secrets = yaml.safe_load(secrets_file)
```

If replicating the environment locally, don't forget to add the `secrets_file` to 
your `.gitignore` or place it outside of version control.



## Example
``` yaml
name: Train models

on:
  pull_request:
    branches:
      - main

jobs:
    trk-2016MagUp-Sim10b:
        uses: LamarrSim/release-models/.github/workflows/release.yaml
        permissions:
          contents: write
        with:
            name: pp-2016-MU-Sim10b
            snakemake_dir: notebooks
            target: deploy 
            additional_config: |
                training_data:
                    - training-data/2016MU-sim10b/LamarrTraining.root
            additional_profile: |
                rerun-triggers: mtime
            generated_model_path: /tmp/s3models/lamarr-train/models/compiled_model.c
        secrets:
            SECRETS_YAML: ${{ secrets.STORAGE_SECRETS }}

```





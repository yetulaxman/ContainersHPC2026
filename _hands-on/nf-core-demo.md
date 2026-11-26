---
topic: containers
title: Tutorial6 - Running an nf-core Pipeline with multiple containers
---
In this exercise, you will run a simplified [nf-core-style](https://nf-co.re/docs/) workflow using **Nextflow** and **Apptainer** on the CSC Roihu supercomputer. The objective is to learn how Nextflow executes containerized workflows and automatically manages software dependencies through containers.

## Login to Roihu

Connect to the Roihu login node using SSH:

```bash
ssh <username>@roihu.csc.fi
```

Replace `<username>` with your CSC username.

## Download the demo workflow

Create a working directory in your project's scratch area and clone the repository:

```bash
mkdir -p /scratch/<project>/$USER  # replace <project> with your project name
cd /scratch/<project>/$USER  # replace <project> with your project name

git clone https://github.com/yetulaxman/demo-nf-core.git
cd demo-nf-core
```

## Run the pipeline

Launch the workflow using the Apptainer profile:

```bash
module load bio-apps
module load nextflow/25.10.2-standalone # Load nextflow module

nextflow run main.nf \
    -profile apptainer \
    --input samplesheet_test_illumina_amplicon.csv \
    --outdir results
```

During the first run, Nextflow will automatically download the required container images. This may take a few minutes depending on network speed and image size.

## Check workflow log files once execution is finished

Open a  terminal and explore the workflow log:

```bash
tail -f .nextflow.log # check if all processes are finished
grep "Submitted process" .nextflow.log   # find all processes that are run with containers
```

## Examine the results

Once the workflow is completed, inspect the output directory for the results:

```bash
tree results
```
Identify (optional):
- Quality control reports
- Summary files
- Workflow outputs

### Questions and hints 

#### 1. Which container images are used by the workflow?

**Hint:** After the workflow starts, Apptainer images are typically cached in the workflow directory under:

```bash
work/singularity/
```
List the downloaded container images:
```bash
find work/singularity -name "*.img"
```

Can you identify which bioinformatics tools these images correspond to?

#### 2. Are the images pulled from Docker Hub, Quay.io, BioContainers, or another registry?
***Hint:** Container URIs are usually defined in the process or module definitions.

Search for container *definitions* which are usually in main.nf files (under modules/nf-core/ ...)

```bash
cd ./modules/nf-core
```
Based on the image URI, determine the source registry:
- `docker.io/...` → Docker Hub
- `quay.io/...` → Quay.io
- `https://depot.galaxyproject.org/...` → Galaxy Singularity repository

#### 3. Does every process use the same container, or are multiple containers used?

**Hint:** Examine the container definitions discovered in the previous step.

Many nf-core workflows use different containers for different processes because each tool is packaged independently (modularity!).

#### 4. How is reproducibility ensured?

**Hint:** Reproducibility is achieved through versioning of both the workflow (here it is a toy example) and container images.

Questions to consider:
- Are the container images versioned?
- Are exact tags used, or is the `latest` tag used?

#### 5. How can CSC disk spaces be mounted when using the 'Apptainer' profile?

**Hint:** Additional bind mounts can be configured via `container.runOptions` in the 'Apptainer' profile in  nextflow.config file

Add the following bind mount configuration to your nextflow.config file:

```bash
apptainer.runOptions = '-B /scratch -B /projappl'
```

## Key takeaways

- Nextflow can automatically download and run container images.
- Apptainer enables secure container execution on HPC systems.
- Software dependencies are defined within the workflow rather than installed manually.
- Different workflow processes can use different containers.
- Containers improve portability, reproducibility, and ease of deployment.
- Understanding where workflow containers come from and what they contain is an important skill for HPC users.

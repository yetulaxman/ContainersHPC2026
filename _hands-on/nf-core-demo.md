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
mkdir -p /scratch/<project>/$USER  # replace with your project
cd /scratch/<project>/$USER  # replace with your project

git clone https://github.com/yetulaxman/demo-nf-core.git
cd demo-nf-core
```

Take a moment to explore the repository structure:

```bash
tree -L 2
```

Can you identify:

- The main Nextflow workflow file?
- Input data files?
- Configuration files?
- Any references to container images?

## Run the pipeline

Launch the workflow using the Apptainer profile:

```bash
nextflow run main.nf \
    -profile apptainer \
    --input samplesheet_test_illumina_amplicon.csv \
    --outdir results
```

During the first run, Nextflow will automatically download the required container images. This may take a few minutes depending on network speed and image size.

## check workflow execution once finished

Open a  terminal and check the workflow log:

```bash
tail -f .nextflow.log # check if all processes are finished
Submitted process .nextflow.log   # find all processes that are run with containers
```

## Examine the results

Once the workflow completes, inspect the output directory:

```bash
tree results
```
Identify:
- Quality control reports
- Summary files
- Workflow outputs
- Any generated statistics

Consider how Nextflow organizes outputs compared to running the tools manually.

### Questions and hints (Optional)

#### 1. Which container images are used by the workflow?

**Hint:** After the workflow starts, Apptainer images are typically cached in the workflow directory under:

```bash
work/singularity/
```

List the downloaded container images:

```bash
find work/singularity -name "*.img"
```

Can*you identify which bio*nformatics tools these images corr*spond* to?

#### 2. Are the images*pulled from Docker Hub, Quay.io, B*oContainers, or another registry?
***Hint:** Container URIs are usual*y defined in the process or module*definitions.

Search for container *definitions* in main.nf files (under modules/nf-core/ ...)

```bash
grep -R "container" ./modules/nf-core/*/main.nf
```
Based on the image URI, determine the source registry:
- `docker.io/...` → Docker Hub
- `quay.io/...` → Quay.io
- `quay.io/biocontainers/...` → BioContainers
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

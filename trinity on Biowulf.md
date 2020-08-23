# Reference: 
*From: https://hpc.nih.gov/apps/trinity.html

# Interactive job

```sh
[user@biowulf]$ sinteractive --cpus-per-task=6 --gres=lscratch:150 --mem=20g
salloc.exe: Pending job allocation 46116226
salloc.exe: job 46116226 queued and waiting for resources
salloc.exe: job 46116226 has been allocated resources
salloc.exe: Granted job allocation 46116226
salloc.exe: Waiting for resource configuration
salloc.exe: Nodes cn3144 are ready for job

[user@cn3144]$ module load trinity
[user@cn3144]$ cd /lscratch/$SLURM_JOB_ID
[user@cn3144]$ cp -pr $TRINITY_ROOT/sample_data .
[user@cn3144]$ cd sample_data/test_Trinity_Assembly
[user@cn3144]$ ./runMe.sh
#######################################################
##  Run Trinity to Generate Transcriptome Assemblies ##
#######################################################

${TRINITY_HOME}/Trinity --seqType fq --max_memory 2G \
              --left reads.left.fq.gz \
              --right reads.right.fq.gz \
              --SS_lib_type RF \
              --CPU 4 
[...snip...]

[user@cn3144]$ exit
salloc.exe: Relinquishing job allocation 46116226
[user@biowulf]$
```

# To run Trinity on a single node, create a batch script similar to the following example.
```sh
#! /bin/bash
# this file is trinity.sh
function die() {
    echo "$@" >&2
    exit 1
}
module load trinity/2.6.5 || die "Could not load trinity module"
[[ -d /lscratch/$SLURM_JOB_ID ]] || die "no lscratch allocated"

inbam=$1
mkdir /lscratch/$SLURM_JOB_ID/in
mkdir /lscratch/$SLURM_JOB_ID/out
cp $inbam /lscratch/$SLURM_JOB_ID/in
bam=/lscratch/$SLURM_JOB_ID/in/$(basename $inbam)
out=/lscratch/$SLURM_JOB_ID/out

Trinity --genome_guided_bam $bam \
    --SS_lib_type RF \
    --output  $out \
    --genome_guided_max_intron 10000 \
    --max_memory 28G \
    --CPU 12
mv $out/Trinity-GG.fasta /data/$USER/trinity_out/$(basename $inbam .bam)-Trinity-GG.fasta

```

## Submit this job using the Slurm sbatch command.
```sh
biowulf$ sbatch --mem=30g --cpus-per-task=12 --gres=lscratch:150 trinity.sh /data/$USER/trinity_in/sample.bam
```



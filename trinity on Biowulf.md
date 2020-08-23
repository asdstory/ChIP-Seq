* From: https://hpc.nih.gov/apps/trinity.html
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


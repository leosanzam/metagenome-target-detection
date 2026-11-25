# Metagenome Target Detection

**Status:** Research prototype. Pipeline implementation and validation are in progress.

A planned reusable workflow for detecting a user-selected taxonomic target in paired-end Illumina shotgun metagenomic FASTQ data. The first validation case is the bee gut-associated parasite *Crithidia bombi*.

## Research question

How does the number of *C. bombi* sequencing fragments affect the sensitivity and specificity of target detection in bee gut metagenomic backgrounds?

## Intended inputs and outputs

**Inputs (planned):** paired-end FASTQ (`R1`, `R2`), target taxonomic identifier or database entry, an explicitly built Kraken2 database, and an optional host reference genome.

**Outputs (planned):** per-sample TSV/CSV with target name, target taxid, supporting classified read pairs, fraction of reads classified to the target, detection call and threshold, and run provenance. The program will also preserve quality-control and classifier reports.

## Planned workflow

1. Inspect and optionally trim paired reads using `fastp`.
2. Optionally remove host reads with `bowtie2`/`samtools` (requires appropriate reference).
3. Classify remaining reads with `Kraken2` using a documented, taxonomically labeled database containing the target and near neighbors.
4. Parse the classifier output in Python and build tabular detection summaries.
5. Evaluate performance on simulated negative/positive controls and a real metagenomic background with injected simulated parasite reads.

**Note:** A small custom database supports detection benchmarking of included taxa, **not** unbiased identification of every organism in a microbiome. Classification is not proof of infection.

## Data and reference sequences

| Purpose | Source | Identifier | Status |
| --- | --- | --- | --- |
| Real bee gut shotgun background | NCBI SRA, *Bombus* gut | `PRJNA833889`, run `SRR19044773` | Identified, download pending |
| Target parasite | NCBI Assembly, *Crithidia bombi* | `GCA_900240985.1` | Identified, download pending |
| Closely related negative control | NCBI Assembly, *Crithidia expoeki* | `GCA_900240875.1` | Identified, download pending |

See [`metadata/data_sources.tsv`](metadata/data_sources.tsv) for source links. Sequence data will be downloaded from its original repositories rather than committed to GitHub.

## Initial validation design

- Fixed background of 100,000 paired-end fragments.
- Add 0, 10, 100, or 1,000 separately simulated *C. bombi* paired-end fragments.
- Add a related-species negative control containing *C. expoeki* but no *C. bombi*.
- Measure read-pair recovery, detection sensitivity, and false-positive detections across replicates.
- Extend to a small real *Bombus* FASTQ subset after the synthetic control works.

In a real background, an unspiked sample is **not** guaranteed biologically negative; only the added spike-in count is known.

## Repository layout

```text
metadata/    Public accession manifest and data provenance
scripts/     Bash, Python, and (when needed) SLURM scripts
references/  Downloaded reference FASTA files (not tracked)
reads/       Downloaded original FASTQ files (not tracked)
simulated/   Generated synthetic FASTQ files (not tracked)
results/     Generated outputs (not tracked)
logs/        Execution logs (not tracked)
docs/        Methods and usage documentation
tests/       Small tests and validation checks
examples/    Lightweight example outputs (when available)
```

## Installation and usage

**Not yet implemented.** Commands, dependencies, reference/database build instructions, and example execution will be added as the pipeline is developed. Do not treat any planned capabilities above as currently working.

## Reproducibility commitments

Document reference assembly versions and accessions, the Kraken2 taxonomy/database composition, software versions, random seeds for simulation, read-pair counts, commands, parameters, and checksums for downloaded input files. Keep substantial FASTQ/FASTA files and secrets out of Git.

## Limitations

Simulated reads do not reproduce all real-library biases. Classification depends on reference coverage, database composition and similarity between species. Host depletion can alter the denominator used for abundance. Detection of sequence evidence is not a validated diagnosis of biological infection.

## Course context

Initial implementation for UCR GEN 220 (Fall 2026), with a goal of generalizing the workflow for other shotgun metagenomic projects after validation.

## License

To be determined before releasing this repository for public reuse.

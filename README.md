# Fusarium Pan-annotation

This repository contains example scripts, information and data for the manuscript "Leveraging existing data to maximise quality and consistency across gene model annotations: a Fusarium pan-annotation". Hill,R. Reynolds,G.,Hall,N & Swarbreck,D..

Final Fusarium reannoations are available via this [link](https://zenodo.org/records/13829922). Zenodo doi:10.5281/zenodo.13829922.


## Protein sequence extraction

```
gffread -g <genome.fa> -o <output.gff3> --keep-genes -E -y <output.proteins.fasta> <original.gff3>
```

## BUSCO
###  Genome

```
busco -i /<genome.fasta> -m genome -o <out.genome.busco> -l hypocreales_odb10 -c 4 --download_path <download_path> --offline
```
###  Protein
```
busco -i /<genome.fasta> -m protein -o <out.genome.busco> -l hypocreales_odb10 -c 4 --download_path <download_path> --offline
```

## Compleasm

```
compleasm run -a <genome.fa> -o <out_dir> -l hypocreales_odb10 -t <threads> -L hypocreales_odb10
```

## Repeat-masking

Repeat masking was performed by [EI repeat](https://github.com/EI-CoreBioinformatics/eirepeat/)

```
eirepeat configure --species sordariomyceta --run_red_repeats --close_reference <refeerence genome> --organellar_fasta <fusarium_organellese.fasta> -o <eirepeat_output> -f <genome.fasta>
```
## Galba

```
galba.pl --workingdir=<working_directory> --genome <softmasked_genome> -prot_seq=<extracted_protein_sequences> --gff3 --verbosity=4 -- threads <thread_num> --skipAllTraining --species=fusarium_ei --AUGUSTUS_CONFIG_PATH <config_path>
```

## Helixer

```
Helixer.py --subsequence-length 21384 --overlap-offset 10692 --overlap-core-length 16038 --model-filepath fungi_v0.3_a_0100.h5 --temporary-dir <tmp.directory> --fasta-path <fasta_file> --gff-output-path <output_file>
```

## LiftOff

```
liftoff -polish -cds -g <reference_annotation> -o <ourput_dir> {target_genome> <reference_genome>
```


## Multi-genome compare


## Minos

Using [Minos](https://github.com/EI-CoreBioinformatics/minos)

### Configure

```
minos configure --mikado- container <mikado_container> -o <output_directory> --external-metrics external_metrics.txt --external <external_config_mod.yaml> --genus-identifier <accession> --annotation-version EIv1.0 --busco-level p --busco-genome-run <link_to_busco_genome_run> --busco-lineage hypocreales_odb10 --busco-scoring 10 --use-diamond --config-file <minos_config.yaml> <transcript_model_list.txt> <scoring_template.yaml> <genome.fasta> 
```

### Run

```
minos run --mikado-container <mikado_container> -o <output_directory> --hpc_config <hpc_config_mod.json>
```

## Functional Annotation

Using [EI Funanno](https://github.com/EI-CoreBioinformatics/eifunannot)

```
eifunannot run --config run_config.yaml --ahrd_config ahrd_input_go_prediction.yaml --hpc_config cluster_config.json
```



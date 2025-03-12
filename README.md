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

The below commands are ran for all comparisons. For example, original annotations vs liftoff round1 or minos-selected models vs liftoff round2. 

"Reference" is the older set of annoations, "Target" is the newer. 

```
gffread --keep-genes -C -o Reference.coding.gff3 Reference.gff3" 

gffread -g Genome.fna -T -w Reference.coding.cdna.fa -o Reference.coding.gtf Reference.coding.gff3

gffread --keep-genes -C -o Target.coding.gff3 Target.gff3" 

gffread -g Genome.fna -T -w Target.coding.cdna.fa -o Target.coding.gtf Target.coding.gff3

```

Run Mikado original and liftoff 
```
 mikado util convert -if gtf -of bed12 Reference.coding.gff3 Reference.coding.bed12

 mikado util convert -if gtf -of bed12 Target.coding.gff3 Target.coding.bed12

```

Format files

```
# for i in file*; do echo $i; cd $i; sed -i 's/>/>ORI_/g' *coding.cdna.fa; sed -i 's/>/>NEW_/g' *transfer.cdna.fa; sed -i 's/ID=/ID=ORI_/g' *coding.bed12; sed -i 's/ID=/ID=NEW_/g' *transfer.bed12; cat *transfer.bed12 | cut -f4 | cut - d ";" -f1 | awk '{print $1"\t"$1}' | sed 's/^ID=//g' | sed 's/ID=NEW_//g' > tmp_group.txt; cat tmp_group.txt | sed 's/NEW_/ORI_/g' > tmp2_group.txt; cat tmp2_group.txt tmp_group.txt > group.txt; cat *coding.cdna.fa *transfer.cdna.fa > all.cdna.fa; cat *coding.bed12 *transfer.bed12 > all.bed12; myhist; rm tmp*; cd ..; done;

```

Run MultiGenomeCompare.py from eiliftover

```
multi_genome_compare.py -v -t 8 --groups group.txt --bed12 all.bed12 --cdnas all.cdna.fa -d out_pairwise -o out_summary
```


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



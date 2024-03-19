# Flagellar and Motility Gene Analysis in Vibrio cholerae

module load Anaconda3
conda config –add channels conda-forge
conda config –add channels bioconda 
conda config –show channels
conda create –n project
conda activate project – using env called project

## Genome sequence retreival 
conda create -n fastq-dl -c conda-forge -c bioconda fastq-dl
conda activate fastq-dl 
fastq-dl --accession ERR025381 –provider ENA 

Repeat with:
ERR018167 ERR018168 ERR018177 ERR018192 ERR025385 ERR025383 ERR018176 ERR018181 ERR018122 ERR019883 ERR018120 ERR018179 ERR025392 ERR018114 ERR019884 ERR018113 ERR018115 ERR025373 ERR018121 ERR018128 ERR025393 ERR018182 ERR025395 ERR018160 SRR308665 SRR308690 SRR308691 SRR308692 SRR308693 SRR308703 SRR308704 SRR308705 SRR308707 SRR308709 SRR308713 SRR308715 SRR308716 SRR308717 SRR308721 SRR308722 SRR308723 SRR19164894 SRR19164900 SRR19164904 SRR19164906 SRR19164909 SRR19164910 SRR19164911 SRR19164913 SRR19173479 SRR19173482 SRR19173483 SRR19173489 SRR19173493 SRR19173498 SRR19173501 SRR19173510 SRR19164888 SRR19164889 SRR19164890 ERR2760849 DRR394994 DRR394996 DRR395000 DRR395002 DRR395004 ERR1880763 ERR3039950


## Unzip FASTQ files
for file in *.fastq.gz; do 
gunzip -c "$file" > "${file%.fastq.gz}.fastq" 
done


## SPAdes genome assembly

input_dir="/home/msc20321183/research/fastq/fastq.gz"
output_dir="/home/msc20321183/research/fastq/ena_annotation"
cd "$input_dir" || exit

find . -maxdepth 1 -type f -name "*_1.fastq" -print0 | while IFS= read -r -d $'\0' forward_reads; do
    filename=$(basename -- "$forward_reads")
    filename_no_ext="${filename%_*}"
    reverse_reads="${filename_no_ext}_2.fastq"
    spades.py --pe1-1 "$forward_reads" --pe1-2 "$reverse_reads" -o "$output_dir/$filename_no_ext"
done


## Prokka genome annotation
for dir in ena_annotation/*/; do
    echo "Running Prokka on ${dir}..."
    prokka --outdir "${dir}prokka" "${dir}contigs.fasta"
    echo "Prokka annotation for ${dir} completed."
done


## Blastn alignment
assembly_dir="/home/msc20321183/research/fastq/ena_annotation"
for assembly_subdir in "$assembly_dir"/*; do 
if [ -d "$assembly_subdir" ]; then 
echo "Running blastn on assemblies in $assembly_subdir"
cd "$assembly_subdir" || exit
blastn -query contigs.fasta -db ena -out blast_results.txt -outfmt 6
echo "Blastn completed for $assembly_subdir"
cd "$assembly_dir" || exit 
fi
 done


## Presence absence matrix



Heatamp python script: 



## Roary pangenome analysis
conda create -n roary -c bioconda -c conda-forge roary
wget https://raw.githubusercontent.com/sanger-pathogens/Roary/master/contrib/roary_plots/roary_plots.py
cp prokka/*/*.gff gff
roary -p 10 -e --mafft -f pangenome -o roary gff/*.gff


## IQTREE phylogenetic analysis 
conda install -c bioconda iqtree
iqtree -s core_gene_alignment.aln -m TEST -alrt 1000 -nt AUTO








    

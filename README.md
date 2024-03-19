# Flagellar-and-Motility-Gene-Analysis-in-Vibrio-cholerae

Module load Anaconda3
Conda config –add channels conda-forge
Conda config –add channels bioconda 
Conda config –show channels
Conda create –n project
Conda activate project – using env called project

## Genome sequence retreival 
conda create -n fastq-dl -c conda-forge -c bioconda fastq-dl
Conda activate fastq-dil 
fastq-dl --accession SRX477044 –provider ENA 



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


## Blastn 
assembly_dir="/home/msc20321183/research /fastq/ena_annotation"
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







## Roary pangenome analysis
conda create -n roary -c bioconda -c conda-forge roary
wget https://raw.githubusercontent.com/sanger-pathogens/Roary/master/contrib/roary_plots/roary_plots.py
cp prokka/*/*.gff gff
roary -p 10 -e --mafft -f pangenome -o roary gff/*.gff


## IQTREE phylogenetic analysis 
conda install -c bioconda iqtree
iqtree -s core_gene_alignment.aln -m TEST -alrt 1000 -nt AUTO








    

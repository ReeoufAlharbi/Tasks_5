# Tasks_5

### Problem 1

```shell
python .\problem1.py > problem1_output.txt
```

```text
30
93
```

### Problem 2

```shell
module load prodigal
wget https://github.com/hyattpd/Prodigal/releases/download/v2.6.3/prodigal.linux
chmod +x prodigal.linux
mkdir -p ~/bin
mv prodigal.linux ~/bin/prodigal
prodigal -v
```

```text
> Prodigal V2.6.3: February, 2016
```

```shell
prodigal -i Genome.fna -o genes.gbk -a proteins.faa -p meta
echo "File Name: $(basename genes.gbk)"
echo "File Path: $(pwd)/genes.gbk"
grep -c "CDS" genes.gbk
```

```text
> File Name: genes.gbk
File Path: /home/alharbrn/Task_week_4/Task5/genes.gbk
1060
```

### Problem 3

```shell
# Load the prodigal module (if required)
module load prodigal

# Get the current directory
current_dir=$(pwd)

# Temporary file to store all results
temp_results=$(mktemp)

# Find all .fna files and loop through them
find "$current_dir" -name "*.fna" -type f | while read -r fna_file; do
    # Generate the output .gff file path
    gff_file="${fna_file%.fna}.gff"
    
    # Run prodigal to create the .gff file from the .fna file
    prodigal -i "$fna_file" -o "$gff_file"
    
    # Count the number of CDS in the .gff file
    cds_count=$(grep -c CDS "$gff_file")
    
    # Print the .gff file and the CDS count
    echo "$gff_file $cds_count"
    
    # Save the results to the temporary file
    echo "$gff_file $cds_count" >> "$temp_results"
done

# Now sort by CDS count and show the highest one
echo "File with the highest CDS count:"
sort -k2 -n "$temp_results" | tail -n 1

# Remove the temporary file
rm "$temp_results"
```

```text
> PRODIGAL v2.6.3 [February, 2016]
Univ of Tenn / Oak Ridge National Lab
Doug Hyatt, Loren Hauser, et al.
-------------------------------------
Request:  Single Genome, Phase:  Training
Reading in the sequence(s) to train...4033488 bp seq created, 47.49 pct GC
Locating all potential starts and stops...219838 nodes
Looking for GC bias in different frames...frame bias scores: 2.21 0.16 0.62
Building initial set of genes to train from...done!
Creating coding model and scoring nodes...done!
Examining upstream regions and training starts...done!
-------------------------------------
Request:  Single Genome, Phase:  Gene Finding
Finding genes in sequence #1 (2961149 bp)...done!
Finding genes in sequence #2 (1072315 bp)...done!
/home/alharbrn/Problem_3/GCA_000006745.1_ASM674v1_genomic.gff 3594
-------------------------------------
PRODIGAL v2.6.3 [February, 2016]
Univ of Tenn / Oak Ridge National Lab
Doug Hyatt, Loren Hauser, et al.
-------------------------------------
Request:  Single Genome, Phase:  Training
Reading in the sequence(s) to train...2257487 bp seq created, 40.40 pct GC
Locating all potential starts and stops...96476 nodes
Looking for GC bias in different frames...frame bias scores: 2.65 0.14 0.21
Building initial set of genes to train from...done!
Creating coding model and scoring nodes...done!
Examining upstream regions and training starts...done!
-------------------------------------
Request:  Single Genome, Phase:  Gene Finding
Finding genes in sequence #1 (2257487 bp)...done!
/home/alharbrn/Problem_3/GCA_000006825.1_ASM682v1_genomic.gff 2032
......


File with the highest CDS count:
/home/alharbrn/Problem_3/GCA_000006745.1_ASM674v1_genomic.gff 3594
```

### Problem 4

```shell
module load prodigal

# Find all .fna files in the current directory and its subdirectories
find . -name "*.fna" -type f | while read fna_file; do
    # Generate the corresponding .gff file path by replacing .fna with .gff
    gff_file="${fna_file%.fna}.gff"

    # Run Prodigal to generate the .gff file
    prodigal -i "$fna_file" -o "$gff_file" -q

    # Count the number of CDS in the .gff file
    cds_count=$(grep -c CDS "$gff_file")

    # Print the result
    echo "File: $gff_file, CDS Count: $cds_count"
done
```

```text
> File: ./GCA_000006745.1_ASM674v1_genomic.gff, CDS Count: 3594
File: ./GCA_000006825.1_ASM682v1_genomic.gff, CDS Count: 2032
File: ./GCA_000006865.1_ASM686v1_genomic.gff, CDS Count: 2383
File: ./GCA_000007125.1_ASM712v1_genomic.gff, CDS Count: 3152
File: ./GCA_000008525.1_ASM852v1_genomic.gff, CDS Count: 1579
File: ./GCA_000008545.1_ASM854v1_genomic.gff, CDS Count: 1866
File: ./GCA_000008565.1_ASM856v1_genomic.gff, CDS Count: 3248
File: ./GCA_000008605.1_ASM860v1_genomic.gff, CDS Count: 1009
File: ./GCA_000008625.1_ASM862v1_genomic.gff, CDS Count: 1776
File: ./GCA_000008725.1_ASM872v1_genomic.gff, CDS Count: 897
File: ./GCA_000008745.1_ASM874v1_genomic.gff, CDS Count: 1063
File: ./GCA_000008785.1_ASM878v1_genomic.gff, CDS Count: 1505
File: ./GCA_000027305.1_ASM2730v1_genomic.gff, CDS Count: 1748
File: ./GCA_000091085.2_ASM9108v2_genomic.gff, CDS Count: 1063
```

```shell
# Create a temporary directory for output files
mkdir -p tmp

# Load the Prokka module
module load prokka

# Find all .fna files in the current directory and its subdirectories
find . -name "*.fna" -type f | while read fna_file; do
    # Extract the file name without the path (basename)
    filename=$(basename "$fna_file")

    # Generate the corresponding .gff file path and place it in the tmp directory
    gff_file="tmp/${filename%.fna}.gff"

    # Run Prokka to generate the .gff file
    prokka --outdir tmp --force --prefix "${filename%.fna}" "$fna_file" > /dev/null 2>&1

    # Count the number of CDS in the .gff file
    cds_count=$(grep -c CDS "$gff_file")

    # Print the result
    echo "File: $gff_file, CDS Count: $cds_count"
done
```

```text
> File: tmp/GCA_000006745.1_ASM674v1_genomic.gff, CDS Count: 3589
File: tmp/GCA_000006825.1_ASM682v1_genomic.gff, CDS Count: 2028
File: tmp/GCA_000006865.1_ASM686v1_genomic.gff, CDS Count: 2383
File: tmp/GCA_000007125.1_ASM712v1_genomic.gff, CDS Count: 3150
File: tmp/GCA_000008525.1_ASM852v1_genomic.gff, CDS Count: 1577
File: tmp/GCA_000008545.1_ASM854v1_genomic.gff, CDS Count: 1861
File: tmp/GCA_000008565.1_ASM856v1_genomic.gff, CDS Count: 3245
File: tmp/GCA_000008605.1_ASM860v1_genomic.gff, CDS Count: 1001
File: tmp/GCA_000008625.1_ASM862v1_genomic.gff, CDS Count: 1771
File: tmp/GCA_000008725.1_ASM872v1_genomic.gff, CDS Count: 892
File: tmp/GCA_000008745.1_ASM874v1_genomic.gff, CDS Count: 1058
File: tmp/GCA_000008785.1_ASM878v1_genomic.gff, CDS Count: 1504
File: tmp/GCA_000027305.1_ASM2730v1_genomic.gff, CDS Count: 1748
File: tmp/GCA_000091085.2_ASM9108v2_genomic.gff, CDS Count: 1056
```

### Problem 5

```shell
# Search for lines containing "gene=" in all .gff files in the tmp directory
grep "gene=" tmp/*.gff | 

# Use awk to split the line by 'gene=' and print the part after 'gene='
awk -F 'gene=' '{if ($2) print $2}' | 

# Use awk again to split by ';' and print only the gene name (the part before the semicolon)
awk -F ';' '{print $1}' | 

# Sort the gene names alphabetically
sort | 

# Remove duplicate gene names
uniq | 

# Display the first 5 unique gene names
head -n 5
```

```text
>aaaT
aaeA
aaeA_1
aaeA_2
aaeB
```



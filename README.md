# Avian Influenza
Shortcuts for Avian Influenza Genomics using Unix.

1. Easily manipulate GISAID downloaded fasta sequences to use in UShER or Nextclade tools.
2. Quickly analyze multiple genomes using Genoflu
   


1.
Easily manipulate GISAID downloaded fasta sequences to use in UShER or Nextclade tools.
As GISAID Influenza sequences are downloaded per segment in one file,
the below code can parse out the different sequences into their individual file.
When downloading the sequences, ensure that the unique GISAID ID and the Segment are in the fasta header.
Download the list of GISAID IDs to copy and paste into the for loop.
```
#Parse GISAID downloaded influenza genomes by GISAID ID
#Download all influenza sequences including all segments
#unwrap each fasta seq in the multifasta file; seqtk tool needed
seqtk seq -l 0 allseqs.fasta > singleline.fasta

#for loop to parse sequences by GISAID ID
# $.fasta will contain one segmented genome per GISAID ID
# The $ will be replaced with the GISAID ID
for i in  EPI_ISL_xx EPI_ISL_xx  ;
do
grep $i -A 1 singleline.fasta >> $i.fasta;
done

#Order the segments from large to small and concat influenza
#These concat_$i.fasta files can be used in UShER or Nextclade to look at whole genome views
for i in  EPI_ISL_xx EPI_ISL_xx ;
do
grep ""PB2"" -A 1 $i.fasta >>concat_$i.fasta;
grep ""PB1"" -A 1 $i.fasta >> concat_$i.fasta;
grep ""PA"" -A 1 $i.fasta >> concat_$i.fasta;
grep ""HA"" -A 1 $i.fasta >> concat_$i.fasta;
grep ""NP"" -A 1 $i.fasta >> concat_$i.fasta;
grep ""NA"" -A 1 $i.fasta >> concat_$i.fasta;
grep ""MP"" -A 1 $i.fasta >> concat_$i.fasta;
grep ""NS"" -A 1 $i.fasta >> concat_$i.fasta;
#replace the carrot with the GISAID ID and combine all of the unique sequences into one large file
grep -v ""^>"" concat_$i.fasta | awk 'BEGIN { ORS=""""; print "">'$i'\n"" } { print }' > final_concat_$i.fasta;
done

#Concat all files with line inbetween
awk 'FNR==1{print ""}1' final_concat_EPI_ISL_19* > finalfile.fasta
```
2. Quickly analyze multiple genomes using Genoflu
```
#Activate genoflu
conda activate genoflu
#The code from section one creates individualized fasta file per GISAID ID and labels them EPI_ISL_unique#.fasta
#These files can be looped through genoflu
for i in EPI_ISL_xx.fasta EPI_ISL_xx.fasta;
do
genoflu.py -f $i>> test.txt;
done

#see all genoflu results in one file
cat *.tsv >> all_genoflu_Results.tsv

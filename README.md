# BIO312- Extra Credit

## _This is a complilation of all the commands I used to do my analysis of NP_008999.2 aka DNA meiotic recombinase 1 (DMC1)_

### Finding homologs with BLAST KEY ; this was done in Lab 3 for me
_the main goal of this lab is to find and align gene family homologs using NP_008999.2_ 
to make a new directory in lab03-s2-clee: 
> cd labs/lab03-s2-clee/

> mkdir ~/labs/lab03-s2-clee/recombinases
; to make new directory folder
> cd ~/labs/lab03-s2-clee/recombinases/

to download my gene NP_008999.2
> ncbi-acc-download -F fasta -m protein NP_008999.2
; download NP_008999.2 from NCBI
> ls
; check the downloaded NCBI page is in the "recombinases" folder
> -db ../allprotein.fas -query NP_008999.2.fa -outfmt 0 -max_hsps 1 -out recombinases.blastp.typical.out
; to BLAST the NCBI file and create a new file called "recombinases.blastp.typical.out"
> less recombinases.blastp.typical.out

> blastp -db ../allprotein.fas -query NP_008999.2.fa  -outfmt "6 sseqid pident length mismatch gapopen evalue bitscore pident stitle"  -max_hsps 1 -out re
combinases.blastp.detail.out
; to create a new file "recombinases.blastp.detail.out" that tables the information from "recombinases.blastp.typical.out"
> less -S recombinases.blastp.detail.out
; to open and check the file was a table	

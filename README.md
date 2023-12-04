# `#0969DA`BIO312- Extra Credit

## _This is a complilation of all the commands I used to do my analysis of NP_008999.2 aka DNA meiotic recombinase 1 (DMC1)_

### Finding homologs with BLAST KEY ; "Lab 3"
_the main goal of this lab is to find and align gene family homologs using NP_008999.2_ 
to make a new directory in lab03-s2-clee: 
```
cd labs/lab03-s2-clee/

mkdir ~/labs/lab03-s2-clee/recombinases
```
to download my gene NP_008999.2 from NCBI and check the downloaded NCBI page is in the "recombinases" folder
```
cd ~/labs/lab03-s2-clee/recombinases/

ncbi-acc-download -F fasta -m protein NP_008999.2

ls
```
to BLAST the NCBI file and create a new file called "recombinases.blastp.typical.out"
```
-db ../allprotein.fas -query NP_008999.2.fa -outfmt 0 -max_hsps 1 -out recombinases.blastp.typical.out
less recombinases.blastp.typical.out
```
to create a new file "recombinases.blastp.detail.out" that tables the information from "recombinases.blastp.typical.out"
```
blastp -db ../allprotein.fas -query NP_008999.2.fa  -outfmt "6 sseqid pident length mismatch gapopen evalue bitscore pident stitle"  -max_hsps 1 -out recombinases.blastp.detail.out
```
to open and check the file was a table	
```
less -S recombinases.blastp.detail.out
```
Sorting
to filter out e values less than 1e-35 and puts information in a new folder called "recombinases.blastp.detail.filtered.out"
```
awk '{if ($6< 1e-35)print $1 }' recombinases.blastp.detail.out > recombinases.blastp.detail.filtered.out
```
to find how many lines are in the filtered file and the unfiltered file
```
wc -l recombinases.blastp.detail.filtered.out

wc -l recombinases.blastp.detail.out  
```
to alphabetasize the filtered file and find how many hits were found for each organism
```
grep -o -E "^[A-Z]\.[a-z]+" recombinases.blastp.detail.filtered.out  | sort | uniq -c 
```
Save & Quit
```
history > lab3.commandhistory.txt

cd ~/labs/lab03-$MYGIT`

find . -size +5M | sed 's|^\./||g' | cat >> .gitignore; awk '!NF || !seen[$0]++' .gitignore

git add .

git commit -a -m "[YOUR DESCRIPTION]"

git pull --no-edit

git push
```

### Gene family sequence alignment; "Lab 4"
_the main goal of this lab is to perform a global multiple sequence alignment in MUSCLE and then calculate the length and the average percent identity among all sequences in the alignment_
to make a new directory in lab03-s2-clee: 
```
mkdir ~/labs/lab04-$MYGIT/recombinases

cd ~/labs/lab04-$MYGIT/recombinases
```
Here is the seqkit command to obtain the sequences that are in the BLAST output file:
```
seqkit grep --pattern-file ~/labs/lab03-$MYGIT/recombinases/recombinases.blastp.detail.filtered.out ~/labs/lab03-$MYGIT/allprotein.fas > ~/labs/lab04-$MYGIT/recombinases/recombinases.homologs.fas
```
To make a multiple sequence alignment using MUSCLE:
```
muscle -in ~/labs/lab04-$MYGIT/recombinases/recombinases.homologs.fas -out ~/labs/lab04-$MYGIT/recombinases/recombinases.homologs.al.fas

muscle -in ~/labs/lab04-$MYGIT/recombinases/recombinases.homologs.fas -html -out ~/labs/lab04-$MYGIT/recombinases/recombinases.homologs.al.html
```
To view and save the alignment as a pdf:
```
alv -kli  ~/labs/lab04-$MYGIT/recombinases/recombinases.homologs.al.fas | less -RS

alv -kli --majority ~/labs/lab04-$MYGIT/recombinases/recombinases.homologs.al.fas | less -RS

alv -ki -w 100 ~/labs/lab04-$MYGIT/recombinases/recombinases.homologs.al.fas | aha > ~/labs/lab04-$MYGIT/recombinases/recombinases.homologs.al.html

a2ps -r --columns=1 ~/labs/lab04-$MYGIT/recombinases/recombinases.homologs.al.html -o ~/labs/lab04-$MYGIT/recombinases/recombinases.homologs.al.ps

ps2pdf ~/labs/lab04-$MYGIT/recombinases/recombinases.homologs.al.ps ~/labs/lab04-$MYGIT/recombinases/recombinases.homologs.al.pdf
```
Here is how to calculate the width (length) of the alignment:
```
alignbuddy  -al  ~/labs/lab04-$MYGIT/recombinases/recombinases.homologs.al.fas
```
Here is how to calculate the length of the alignment after removing any column with gaps:
```
alignbuddy -trm all  ~/labs/lab04-$MYGIT/recombinases/recombinases.homologs.al.fas | alignbuddy  -al
```
Here is how to calculate the length of the alignment after removing invariant (completely conserved) positions:
```
alignbuddy -dinv 'ambig' ~/labs/lab04-$MYGIT/recombinases/recombinases.homologs.al.fas | alignbuddy  -al
```
Calculate the average percent identity using t_coffee:
```
t_coffee -other_pg seq_reformat -in ~/labs/lab04-$MYGIT/recombinases/recombinases.homologs.al.fas -output sim
```
Repeat calculating the average percent identity using alignbuddy.
```
alignbuddy -pi ~/labs/lab04-$MYGIT/recombinases/recombinases.homologs.al.fas | awk ' (NR>2)  { for (i=2;i<=NF  ;i++){ sum+=$i;num++} }
     END{ print(100*sum/num) } '
```
Save & Quit
```
history > lab4.commandhistory.txt
cd ~/labs/lab04-$MYGIT`
find . -size +5M | sed 's|^\./||g' | cat >> .gitignore; awk '!NF || !seen[$0]++' .gitignore
git add .
git commit -a -m "[YOUR DESCRIPTION]"
git pull --no-edit
git push
```

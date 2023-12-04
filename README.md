# BIO312- Extra Credit

## _This is a complilation of all the commands I used to do my analysis of NP_008999.2 aka DNA meiotic recombinase 1 (DMC1)_

### Finding homologs with BLAST KEY ; "Lab 3"
_the purpose of this lab is to find and align gene family homologs using NP_008999.2_ 
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
_the purpose of this lab is to perform a global multiple sequence alignment in MUSCLE and then calculate the length and the average percent identity among all sequences in the alignment_
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

### Gene family phylogeny using IQ-TREE; "Lab 5"
_the purpose of this lab is to estimate a maximum-likelihood, midpoint rooted phylogeny for the gene family_
to make a new directory in lab05-s2-clee: 
```
mkdir ~/labs/lab05-$MYGIT/recombinases

cd ~/labs/lab05-$MYGIT/recombinases

cp ~/labs/lab04-$MYGIT/recombinases/recombinases.homologs.al.fas ~/labs/lab05-$MYGIT/recombinases/recombinases.homologs.al.fas   
```
To use IQ-TREE to calculate the optimal amino acid substitution model and amino acid frequencies and then tree search, estimating branch lengths as it goes:
```
iqtree -s ~/labs/lab05-$MYGIT/recombinases/recombinases.homologs.al.fas -bb 1000 -nt 2 
```
Read as newick formatted:
```
nw_display ~/labs/lab05-$MYGIT/recombinases/recombinases.homologs.al.fas.treefile
```
Looking at the tree unrooted with small text labels:
```
Rscript --vanilla ~/labs/lab05-$MYGIT/plotUnrooted.R  ~/labs/lab05-$MYGIT/recombinases/recombinases.homologs.al.fas.treefile ~/labs/lab05-$MYGIT/recombinases/recombinases.homologs.al.fas.treefile.pdf 0.4 15
```
Midpoint rooting and viewing it in newick formatting and as a graphic:
```
gotree reroot midpoint -i ~/labs/lab05-$MYGIT/recombinases/recombinases.homologs.al.fas.treefile -o ~/labs/lab05-$MYGIT/recombinases/recombinases.homologs.al.mid.treefile

nw_order -c n ~/labs/lab05-$MYGIT/recombinases/recombinases.homologs.al.mid.treefile  | nw_display -

nw_order -c n ~/labs/lab05-$MYGIT/recombinases/recombinases.homologs.al.mid.treefile | nw_display -w 1000 -b 'opacity:0' -s  >  ~/labs/lab05-$MYGIT/recombinases/recombinases.homologs.al.mid.treefile.svg -
```
Viewing as a cladogram:
```
nw_order -c n ~/labs/lab05-$MYGIT/recombinases/recombinases.homologs.al.mid.treefile | nw_topology - |

nw_display -s  -w 1000 > ~/labs/lab05-$MYGIT/recombinases/recombinases.homologs.al.midCl.treefile.svg -
```
Printing alignment into pdf; used iTOL tree to create this as an svg:
```
Rscript --vanilla ~/labs/lab05-$MYGIT/plotMSA.R  ~/labs/lab05-$MYGIT/recombinases/recombinases.homologs.al.fas ~/labs/lab05-$MYGIT/recombinases/recombinases.homologs.al.fas.pdf
```
Save & Quit:
```
history > lab5.commandhistory.txt

cd ~/labs/lab05-$MYGIT

find . -size +5M | sed 's|^\./||g' | cat >> .gitignore; awk '!NF || !seen[$0]++' .gitignore

git add .

git commit -a -m "Adding all new data files I generated in AWS to the repository."

git pull --no-edit

git push
```

### Reconciling a gene and species tree; "Lab 6"
_the purpose of this lab is to reconcile in Notung the gene family tree based on the midpoint rooted tree from lab 5_
to make a new directory in lab06-s2-clee: 
```
mkdir ~/labs/lab06-$MYGIT/mygenefamily

cp ~/labs/lab05-$MYGIT/mygenefamily/mygenefamily.homologs.al.mid.treefile ~/labs/lab06-$MYGIT/mygenefamily/mygenefamily.homologs.al.mid.treefile
```
Reconciling the gene and species tree using Notung with names assigned to the internal nodes:
```
java -jar ~/tools/Notung-3.0-beta/Notung-3.0-beta.jar -s ~/labs/lab06-$MYGIT/species.tre -g ~/labs/lab06-$MYGIT/recombinases/recombinases.homologs.al.mid.treefile --reconcile --speciestag prefix --savepng --events --outputdir ~/labs/lab06-$MYGIT/recombinases/

grep NOTUNG-SPECIES-TREE ~/labs/lab06-$MYGIT/recombinases/recombinases.homologs.al.mid.treefile.reconciled | sed -e "s/^\[&&NOTUNG-SPECIES-TREE//" -e "s/\]/;/" | nw_display -
```
generate a RecPhyloXML object and the gene-within-species tree via thirdkind:
```
python2.7 ~/tools/recPhyloXML/python/NOTUNGtoRecPhyloXML.py -g ~/labs/lab06-$MYGIT/recombinases/recombinases.homologs.al.mid.treefile.reconciled --include.species

thirdkind -Iie -D 40 -f ~/labs/lab06-$MYGIT/recombinases/recombinases.homologs.al.mid.treefile.reconciled.xml -o  ~/labs/lab06-$MYGIT/recombinases/recombinases.homologs.al.mid.treefile.reconciled.svg
```
Save & Quit
```
history > lab6.commandhistory.txt

cd ~/labs/lab06-$MYGIT`

find . -size +5M | sed 's|^\./||g' | cat >> .gitignore; awk '!NF || !seen[$0]++' .gitignore

git add .

git commit -a -m "[YOUR DESCRIPTION]"

git pull --no-edit

git push
```

### Protein Domain Prediction; "Lab 8"
_the purpose of this lab is to identify gene motifs and domains using RPS-BLAST and Pfam_
to make a new directory in lab08-s2-clee: 
```
mkdir ~/labs/lab08-$MYGIT/recombinases && cd ~/labs/lab08-$MYGIT/recombinases

cp ~/labs/lab05-$MYGIT/recombinases/recombinases.homologs.al.mid.treefile ~/labs/lab08-$MYGIT/recombinases
```
make a copy of our raw unaligned sequence, removing the asterisk (stop codon) in the process. To do this, we will use sed's substitute command to substitute any instance of an asterisk with nothing.
```
sed 's/*//' ~/labs/lab04-$MYGIT/recombinases/recombinases.homologs.fas > ~/labs/lab08-$MYGIT/recombinases/recombinases.homologs.fas
 ```
enable functional analysis of proteins by classifying them into families and predicting domains and important sites:
```
wget -O ~/data/Pfam_LE.tar.gz ftp://ftp.ncbi.nih.gov/pub/mmdb/cdd/little_endian/Pfam_LE.tar.gz && tar xfvz ~/data/Pfam_LE.tar.gz  -C ~/data
```
To alphabetically list the species in a table with a 1x10^-10 e-threshold for saving hits. In the table, there is the query sequence length, start and end of alignment of each subject, the e value, and subject title. This was also repeated with a different e-value (0.1) to see if it will fix the missing domains 
```
rpsblast -query ~/labs/lab08-$MYGIT/recombinases/recombinases.homologs.fas -db ~/data/Pfam -out ~/labs/lab08-$MYGIT/recombinases/recombinases.rps-blast.out  -outfmt "6 qseqid qlen qstart qend evalue stitle" -evalue .0000000001

rpsblast -query ~/labs/lab08-$MYGIT/recombinases/recombinases.homologs.fas -db ~/data/Pfam -out ~/labs/lab08-$MYGIT/recombinases/recombinases.rps-blast.out-e.1  -outfmt "6 qseqid qlen qstart qend evalue stitle" -evalue .1
```
Plot the predicted Pfam domains on the phylogenetic tree:
```
sudo /usr/local/bin/Rscript  --vanilla ~/labs/lab08-$MYGIT/plotTreeAndDomains.r ~/labs/lab08-$MYGIT/recombinases/recombinases.homologs.al.mid.treefile ~/labs/lab08-$MYGIT/recombinases/recombinases.rps-blast.out ~/labs/lab08-$MYGIT/recombinases/recombinases.tree.rps.pdf
```
Looking at the predicted domains with more details:
```
mlr --inidx --ifs "\t" --opprint  cat ~/labs/lab08-$MYGIT/recombinases/recombinases.rps-blast.out | tail -n +2 | less -S
```
Finding which proteins have more than one annotation, which Pfam domain annotation is most commonly found, the shortest/longest annotated protein domain, and the best e-value:
```
cut -f 1 ~/labs/lab08-$MYGIT/recombinases/recombinases.rps-blast.out | sort | uniq -c

cut -f 6 ~/labs/lab08-$MYGIT/recombinases/recombinases.rps-blast.out | sort | uniq -c

awk '{a=$4-$3;print $1,'\t',a;}' ~/labs/lab08-$MYGIT/recombinases/recombinases.rps-blast.out |  sort  -k2nr

cut -f 1,5 -d $'\t' ~/labs/lab08-$MYGIT/recombinases/recombinases.rps-blast.out | sort -k2,2rn -t $'\t' 
```
Save & Quit
```
history > lab8.commandhistory.txt

cd ~/labs/lab08-$MYGIT`

find . -size +5M | sed 's|^\./||g' | cat >> .gitignore; awk '!NF || !seen[$0]++' .gitignore

git add .

git commit -a -m "[YOUR DESCRIPTION]"

git pull --no-edit

git push
```

One last command... 
```
cowsay -f milk "Tada! You did it! Great Job"
```


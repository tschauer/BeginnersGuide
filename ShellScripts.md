---
title: "Organize code in Shell Scripts"
author: Tamas Schauer
date: '21 August, 2020'
output: 
    html_document:
        toc: true
        toc_depth: 3
        keep_md: yes
---
<style>
pre {
  overflow-x: auto;
}
pre code {
  word-wrap: normal;
  white-space: pre;
}
</style>



## Use of Shell Scripts

* running scripts makes code reproducible, reusable and shareable
* loops help to iterate through all input files
* using variable names avoids mistakes of typing file names one-by-one

## Install Prerequisites

Check installation of these software in previous tutorials:

* JE demultiplexer
* Bowtie2 aligner
* Homer

Install Atom editor from https://atom.io

## Test Shell Script

### Setup shell script file


```bash
# change directory to your Software directory
cd ~/Software/

# create new file
touch my_first_shell_script.sh

# change mode to be executable
chmod 744 my_first_shell_script.sh

# open file in Atom and start edit from there
open -a Atom my_first_shell_script.sh

# Check which software is already installed
ls -l
```

```
## total 122704
## -rw-r--r--    1 tschauer  staff   8710496 Jul 13 12:25 2.7.4a.zip
## drwxr-xr-x   15 tschauer  staff       480 Jul 14 10:40 Homer
## drwxr-xr-x   17 tschauer  staff       544 Jun  1 21:08 STAR-2.7.4a
## drwxr-xr-x   28 tschauer  staff       896 Feb 28 23:43 bowtie2-2.4.1-macos-x86_64
## -rw-r--r--    1 tschauer  staff  16048820 Jul 13 11:00 bowtie2-2.4.1-macos-x86_64.zip
## drwxr-xr-x    5 tschauer  staff       160 Jun  2 13:50 je_2.0.RC
## -rw-r--r--    1 tschauer  staff  31469100 Jun  2 13:50 je_2.0.RC.tar.gz
## -rwxr--r--@   1 tschauer  staff        82 Aug 21 15:04 my_first_shell_script.sh
## drwxr-xr-x  135 tschauer  staff      4320 Jul 13 11:20 samtools-1.10
## -rw-r--r--    1 tschauer  staff   4721173 Jul 13 11:13 samtools-1.10.tar.bz2
```

### Edit in Atom

* Add the following code in the editor


```bash
#!/bin/sh

# this is a for loop
for (( i = 0; i < 10; i++ )); do
  echo ${i}
done
```

### Execute Script

* run from the command line


```bash
# from the current directory
cd ~/Software/
./my_first_shell_script.sh 
```

```
## 0
## 1
## 2
## 3
## 4
## 5
## 6
## 7
## 8
## 9
```


```bash
# or from somewhere else
~/Software/my_first_shell_script.sh 
```

```
## 0
## 1
## 2
## 3
## 4
## 5
## 6
## 7
## 8
## 9
```


## Demultiplexing Script

### Reminder: Je demultiplexer has to be in PATH


```bash
# start from the home directory
cd ~   

# open .bash_profile in Atom (or which ever text editor)
open -a Atom .bash_profile

# add the following line to the .bash_profile file
PATH=/Users/tschauer/Software//je_2.0.RC:$PATH
# save and close window

# source .bash_profile
source .bash_profile
```

### Setup shell script file


```bash
# change to the folder where your FastQ files are
cd ~/Projects/Project01/FastQ 

# create new shell script file
touch JE_demultiplex_SR.sh

# change mode to be executable
chmod 744 JE_demultiplex_SR.sh

# open file in Atom and start edit from there
open -a Atom JE_demultiplex_SR.sh
```


### Edit in Atom

* Add the following code in the editor


```shell
#!/bin/sh

for FILE in *R1.fastq.gz; do 

    # extract base of file name 
    FILEBASE=`echo $FILE| sed -e "s/_R1.fastq.gz//g"` 
    
    # all files have to have the same base name 
    F1=${FILEBASE}_R1.fastq.gz 
    BF=${FILEBASE}_Barcodes.txt 
    I1=${FILEBASE}_R2.fastq.gz 
    M=${FILEBASE}_JEstats.txt 
    
    je demultiplex-illu F1=$F1 BF=$BF I1=$I1 M=$M 
    
done
```

### Execute Script

* R1 and R2 FastQ files and barcode txt file have to be in the same folder
* run from the command line


```bash
# change to the folder where your FastQ files are
cd ~/Projects/Project01/FastQ 

# run demultiplexing in the FastQ directory
./JE_demultiplex_SR.sh
```




## Aligner Script


### Setup shell script file


```bash
# change to the folder where your FastQ files are
cd ~/Projects/Project02/FastQ 

# create new shell script file
touch bowtie2_SR.sh

# change mode to be executable
chmod 744 bowtie2_SR.sh

# open file in Atom and start edit from there
open -a Atom bowtie2_SR.sh
```


### Edit in Atom

* Add the following code in the editor


```bash
#!/bin/sh

BOWTIE_INDEX="/Users/tschauer/Genomes/Dmelanogaster/dmel_genome"

for FILE in *.txt.gz; do

    # extract base name from file name
	BASE=`echo ${FILE} | sed -e "s/.txt.gz//g"`

    # run bowtie2
	bowtie2 -p 2 -x $BOWTIE_INDEX -U ${BASE}.txt.gz  > ${BASE}.sam 2> ${BASE}.txt
    
    # run samtools
	samtools view -bS -@ 2 ${BASE}.sam | samtools sort - -@ 12 | tee ${BASE}.bam | samtools index - ${BASE}.bam.bai

    # remove sam file
	rm ${BASE}.sam

done
```

### Execute Script

* see how to setup bowtie index in the previous tutorial (match the path)
* FastQ files with *.txt.gz extension have to be in the same directory
* run from the command line


```bash
# change to the folder where your FastQ files are
cd ~/Projects/Project02/FastQ 

# run demultiplexing in the FastQ directory
./bowtie2_SR.sh
```



# Table of Contents
0. #Abstract
1. [#What is FASTQ used for?]
2. [#Why is FASTQ only "weakly specified"?](#Graphical%20Overview%20of%20the%20FAIRification%20Recipe%20Objectives)
3. [#What are the variants of FASTQ to expect?](#FAIRification%20Objectives,%20Inputs%20and%20Outputs)
4. [#References](#Capability%20&%20Maturity%20Table)
5. [#Authors](#Authors)
5. [#License](#License)

---

## Abstract

The FASTQ format is a popular format for storing sequences (i.e. letters representing nucleotides in a piece of DNA) and their corresponding quality scores. FASTQ is a text-based data format with weak specification. This ingredient description will exemplify how a FASTQ file can look like, what variants=dialects are commonly found, and will list some sources for weak=pseudo specifications of this important de facto standard file format.

___

## What is FASTQ used for?

The FASTQ format is a popular format for storing sequences (i.e. letters representing nucleotides in a piece of DNA) and their corresponding quality scores. It is used in most genomics research projects, and is a format of choice e.g. for EMBL-EBI's ENA (European Nucleotide Archive) [https://ena-docs.readthedocs.io/en/latest/faq/archive-generated-files.html?highlight=fastq#fastq-file-format], or for NCBI's SRA (Sequencing Read Archive) [https://www.ncbi.nlm.nih.gov/sra/docs/submitformats/#fastq-files]. It also sits in trillions of files on individual researcher's hard disks / drives, in academia as well as in the industry.

The FASTQ format transports not only the sequence as determined by the sequencing machine, but also the quality (=trustworthiness) of each letter.

For more information on the FASTQ format, the authors recommend the wikipedia article as a start: <https://en.wikipedia.org/wiki/FASTQ_format>

___

## Why is FASTQ only "weakly specified"?

Unfortunately, FASTQ is a "flexible" format. There are no strict rules on how to write it, respectively how to interpret it correctly.
There is no universal validator, nor a true specification (in the sense of a technical specification giving accepted values for each field; see e.g. any [https://www.w3.org/StyleSheets/TR/2016/readme.html](W3C recommendation) or [https://tools.ietf.org/rfc/index](IETF RFC) to get a sense what "technical specification" might mean).

There are however de facto rules, mainly generated by voluntary, non-authoritative adherence of the main manufacturers of sequencing instruments. The manufacturers can change the format as will, and did that in the past, too.

Nevertheless, FASTQ files exhibit a distinct kind of "flavor", and most bioinformaticians will recognize a FASTQ file when they see it. Let's have a look at some examples of de facto standard FASTQ formats to get a better gist of what all of this means in practice.

___

## What are variants of FASTQ to expect?

Direct quotation from Wikipedia (<https://en.wikipedia.org/wiki/FASTQ_format>):

> "A FASTQ file normally uses four lines per sequence.
>
>     Line 1 begins with a '@' character and is followed by a sequence identifier and an optional description (like a FASTA title line).
>     Line 2 is the raw sequence letters.
>     Line 3 begins with a '+' character and is optionally followed by the same sequence identifier (and any description) again.
>     Line 4 encodes the quality values for the sequence in Line 2, and must contain the same number of symbols as letters in the sequence.
>
> A FASTQ file containing a single sequence might look like this:
>
>     @SEQ_ID
>     GATTTGGGGTTCAAAGCAGTATCGATCAAATAGTAAATCCATTTGTTCAACTCACAGTTT
>     +
>     !''*((((***+))%%%++)(%%%%).1***-+*''))**55CCF>>>>>>CCCCCCC65
>
> The byte representing quality runs from 0x21 (lowest quality; '!' in ASCII) to 0x7e (highest quality; '~' in ASCII). Here are the quality value characters in left-to-right increasing order of quality (ASCII):
>
>      !"#$%&'()*+,-./0123456789:;<=>?@ABCDEFGHIJKLMNOPQRSTUVWXYZ[\]^_`abcdefghijklmnopqrstuvwxyz{|}~
>
> The original Sanger FASTQ files also allowed the sequence and quality strings to be wrapped (split over multiple lines), but this is generally
> discouraged as it can make parsing complicated due to the unfortunate choice of "@" and "+" as markers (these characters can also occur in the quality
> string)."
(end of direct quotation)



An excellent resource for understanding the FASTQ format is this publication: <https://doi.org/10.1093/nar/gkp1137>. It contains in the Supplementary Material a collection of valid FASTQ files of different formats, as well as invalid files. Variants defined in this publication are called e.g. "fastq-sanger" and "fastq-illumina", which differ only in the way they encode PHRED scores (i.e. the quality scores):

>     PHRED score:     0  1  2  3  4  5  6  7  8  9  10 11 12 13 14 15 16 17 18 19 20 21 22 23 24 25 26 27 28 29 30 31 32 33 34 35 36 37 38 39 40 41 42 43 44 45 46 47 48 49 50 51 52 53 54 55 56 57 58 59 60 61 62 63 64 65 66 67 68 69 70 71 72 73 74 75 76 77 78 79 80 81 82 83 84 85 86 87 88 89 90 91 92 93
>     fastq-sanger:    !  "  #  $  %  &  '  (  )  *   +  ,  -  .  /  0  1  2  3  4  5  6  7  8  9  :  ;  <  =  >  ?  @  A  B  C  D  E  F  G  H  I  J  K  L  M  N  O  P  Q  R  S  T  U  V  W  X  Y  Z  [  \  ]  ^  _  `  a  b  c  d  e  f  g  h  i  j  k  l  m  n  o  p  q  r  s  t  u  v  w  x  y  z  {  |  }  ~
>     fastq-illumina:  A  B  C  D  E  F  G  H  I  J   K  L  M  N  O  P  Q  R  S  T  U  V  W  X  Y  Z  [  \  ]  ^  _  `  a  b  c  d  e  f  g  h  i  j  k  l  m  n  o  p  q  r  s  t  u  v  w  x  y  z  {  |  }  ~  ~  ~  ~  ~  ~  ~  ~  ~  ~  ~  ~  ~  ~  ~  ~  ~  ~  ~  ~  ~  ~  ~  ~  ~  ~  ~  ~  ~  ~  ~  ~  ~


Note how the "illumina" encoding can only hold the PHRED scores 0--61, all the scores above are represented by the same character. In contrast, the "illumina" encoding supports PHRED scores from 0 to 93. Also, the "illumina" encoding is actually a subset of the "sanger" encoding (the characters of "illumina" encoding start at PHRED score of 32 in the "sanger" encoding).

There is no way to tell from the FASTQ file itself, which encoding of the PHRED scores is used; whether it is the "sanger" system or the "illumina" system.
In the worst case, you might mistake the quality scores for being all excellent or all terrible when you or your program is expecting just the opposite encoding of the PHRED scores.


## Authors:

| Name | Affiliation  | orcid | CrediT role  |
| :------------- | :------------- | :------------- |:------------- |
| Robert T. Giessmann |  Bayer AG | [0000-0002-0254-1500](https://http://orcid.org/0000-0002-0254-1500) | Writing - Original Draft |

___


## License:

<a href="https://creativecommons.org/licenses/by/4.0/"><img src="https://mirrors.creativecommons.org/presskit/buttons/80x15/png/by-sa.png" height="20"/></a>
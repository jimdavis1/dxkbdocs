# Viral Annotation
One of the goals of dxkb is to build tools that can accellerate immunogen design.  In order to do this, either at the bench or through the use of advance AI-based modeling, it is necessary to have accurate and consistent genome annotations.  For this project, we have developed a simple low-tech viral annotation system that scales well.  It currently covers the *Paramyxoviridae*, *Bunyavirales*, *Filoviridae*, and *Pneumoviridae*.  This document is intended to describe how the annotation system currently works. The GitHub repo for this project can be currently found here: https://github.com/jimdavis1/Viral_Annotation.git.

## Covered viral taxa

### Bunyavirales:
Arenaviridae<br>
Fimoviridae<br>
Hantaviridae<br>
Nairoviridae<br>
Peribunyaviridae<br>
Phasmaviridae<br>
Phenuiviridae<br>
Tospoviridae<br>

### Filoviridae:
Orthoebolavirus<br>
Orthomarburgvirus<br>

### Paramyxoviridae:
Aquaparamyxovirus<br>
Ferlavirus<br>
Henipavirus<br>
Jeilongvirus<br>
Metaavulavirus<br>
Morbillivirus<br>
Narmovirus<br>
Orthoavulavirus<br>
Orthorubulavirus<br>
Paraavulavirus<br>
Pararubulavirus<br>
Respirovirus<br>

### Pneumoviridae:
Orthopneumovirus<br>
Metapneumovirus<br>
Orthopneumovirus muris<br>



## Step 1.  Calling features based on PSSMs

The code is currently designed to work on the *Paramyxoviridae*, *Bunyavirales*, *Filoviridae*, and *Pneumoviridae* although more taxa are planned.  As depicted in the image below, the program performs a BLASTn against a small set of representative genomes for each taxon.  Then it sorts the results by bit score and chooses the best match.<br><br>

For each given taxon, there is a directory of PSSMs corresponding to each known protein for that taxon. The PSSMs are derived from a set of hand curated alignments. <br><br>

In the next step, it cycles through each directory of PSSMs (there may be more than one PSSM per protein), choosing the best match using tBLASTn. <br>

Note that it assumes your genome will have the same set of proteins as the nearest match. This is why it is not intended to be used as a discovery tool.  In the event that a new protein is found, a new PSSM must be added to the system.  <br><br>

![Anno-Strategy](https://github.com/jimdavis1/dxkbdocs/blob/main/flow_chart.jpg)


## Step 2.  Resolving special features
There are currently two types of special features that the annotation system calls.  The first are location-based features.  These are proteins or RNAs that are called based on location.  The second are transcript-edited features.  Transcript editing is a phenomenon that occurs in the phosphoproteins of the *Paramyxoviridae* and the glycoproteins of the *Filoviridae*.  It occurs when the RNA-dependent RNA polymerase encounters a region of low complexity and pauses.  The pause allows for the insertion of one or more new nucleotides into the transcript, which causes a frame shift. Thus, the amino acid sequence is not a direct translation of what is encoded in the genome.  We solve this problem by hand-curating a set of transcripts in their post-editing state. We then BLAST these against the contig, and for BLASTn matches with high enough scores and no additional gaps, the alignment gap is filled in using the nucleotide sequence of closest curated transcript.<br>



## Step 3.  Assessing genome quality 
Genome quality is assessed by computing:<br>

1.  The number of ambiguous bases per contig
2.  The number of segments versus what is expected 
3.  The length of each segment versus what is expected
3.  The number of occurrences of each non-variable feature versus what is expected
4.  The length of each non-variable feature versus what is expected<br><br>

Each segment is defined based on the proteins that it should encode.  Expected segment and feature lengths are defined empirically based on what has been seen before in that taxon.  Note that it is possible for a rare, but high quality, genome to deviate from expectation in terms of genome organization, segment length, or feature length, and could be assigned a "poor" genome quality.  In cases where segmented viruses have uncharacterized or variable gene content in their smaller segments, such as Fimoviridae, these smaller segments are not accounted for by the quality checker.  


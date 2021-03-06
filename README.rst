iAnnotateSV: Annotation of structural variants detected from NGS
================================================================

:Author: Ronak H Shah
:Contact: rons.shah@gmail.com
:Source code: http://github.com/rhshah/iAnnotateSV
:License: `Apache License 2.0 <http://www.apache.org/licenses/LICENSE-2.0>`_

iAnnotateSV is a Python library and command-line software toolkit to annotate and
visualize structural variants detected from Next Generation DNA sequencing data. This works for majority is just re-writing of a tool called dRanger_annotate written in matlab by Mike Lawrence at Broad Institue. 
But it also has some additional functionality and control over the annotation w.r.t the what transcripts to be used for annotation.
It is designed for use with hybrid capture, including both whole-exome and custom target panels, and
short-read sequencing platforms such as Illumina.

Citation
========

We are in the process of publishing a manuscript describing iAnnotateSV as part of the Structural Variant Detection framework.
If you use this software in a publication, for now, please cite our website `iAnnotateSV <http://github.com/rhshah/iAnnotateSV>`_.

Acknowledgements
================

I would like to thanks Mike Lawrence from Braod Institue for sharing his code and Michael Berger for his insights into the dRanger_annotate tool.

Quick Usage
===========

If you know python I have created a small test script in /iAnnotateSV/test directory it runs a test on existing code and compares the result with the output file.

Else To Run:
            * If you want to run with default options:
            
            ``python iAnnotateSV.py -i svFile.txt -o outputfile.txt -r hg19 -d 3000``
            
            * If you want to run with your own transcripts:
            
            ``python iAnnotateSV.py -i svFile.txt -o outputfile.txt -r hg19 -d 3000 -c canonicalTranscripts.txt``

**usage: iAnnotateSV.py [options]**

**Annotate SV based on a specific human reference**

**optional arguments:**
  -h, --help            show this help message and exit
  -v, --verbose         make lots of noise [default]
  -r hg19, --refFileVersion hg19
                        Which human reference file to be used, hg18,hg19 or
                        hg38
  -o outfile, --outputFile out.txt
                        Full path with for the output file
  -i inputSVfile, --svFile svfile.txt
                        Location of the structural variants file to annotate
  -d distance, --distance 3000
                        Distance used to extend the promoter region
  -a, --autoSelect      Auto Select which transcript to be used[default]
  -c canonicalExonsFile, --canonicalTranscripts canonicalExons.txt
                        Location of canonical transcript list for each gene.
                        Use only if you want the output for specific
                        transcripts for each gene.

Input file format is a tab-delimited file containing:

chr1  pos1  str1  chr2  pos2  str2

as the header and where:

* **chr1:** Its the chromosome name for first break point [1,2,3,4,5,6,7 etc..],
* **pos1:** Its the chromosome loaction for first break point [1-based],
* **str1:** Its the read direction for the first break point [0=top/plus/reference, 1=bottom/minus/complement],
* **chr2:** Its the chromosome name for second break point [1,2,3,4,5,6,7 etc..],
* **pos2:** Its the chromosome loaction for second break point [1-based],
* **str2:** Its the read direction for the second break point [0=top/plus/reference, 1=bottom/minus/complement], 

Output file will is a tab-delimited file containing:

chr1  pos1  str1  chr2  pos2  str2  gene1 transcript1 site1 gene2 transcript2 site2 fusion

as the header and where:

* **chr1** : Its the chromosome name for first break point [1,2,3,4,5,6,7 etc..],
* **pos1** : Its the chromosome loaction for first break point [1-based],
* **str1** : Its the read direction for the first break point [0=top/plus/reference, 1=bottom/minus/complement],
* **chr2** : Its the chromosome name for second break point [1,2,3,4,5,6,7 etc..],
* **pos2** : Its the chromosome loaction for second break point [1-based],
* **str2** : Its the read direction for the second break point [0=top/plus/reference, 1=bottom/minus/complement],
* **gene1** : Gene for the first break point,
* **transcript1** : Transcript used for the first breakpoint,
* **site1** : Explanation of the site where the first breakpoint occurs [Example=>Intron of EWSR1(+):126bp after exon 10],
* **gene2** : Gene for the second break point,
* **transcript2** : Transcript used for the second breakpoint,
* **site2** : Explanation of the site where the second breakpoint occurs [Example=>Intron of ERG(-):393bp after exon 4],
* **fusion** : Explanation if the evnet leads to fusion or not. [Example=>Protein Fusion: in frame  {EWSR1:ERG}]

Please look at examples of input  and output files in /data/test directory where:
/data/test/testData.txt is the input file
/data/test/testResult.txt is the output file

The refFileVersion are automaticslly chosen from /data/references. **But caution this is only tested on hg19**. All these files are created using UCSC table browser.

The example for canonical transcripts can be also found in /data/canonicalInfo. In general the file is tab-delimited containing:

Gene  Transcripts

as the headers where:

* **Gene** : Gene symobol should match the gene name from  /data/references file.
* **Transcripts** : Transcripts is a particular transcript that you are interested in using instead of auto-selection.


iAnnotateSV package
===================

Module ``iAnnotateSV`` contents
-------------------------------


.. automodule:: iAnnotateSV
    :members:
    :undoc-members:
    :show-inheritance:
 
Submodules
----------

``AnnotateEachBreakpoint`` module
---------------------------------

.. automodule:: iAnnotateSV.AnnotateEachBreakpoint
    :members: FindATranscript, FindAllTranscript
    :undoc-members:
    :show-inheritance:
- This module will annotate each breakpoint taking in:
   * **chr** : chromosome for the event,
   * **pos** : position in the chromosome for the event,
   * **str** : direction of the reads for the event[either 0 or 1],
   * **referenceDataframe** : a pandas data-frame that will store reference information
   :Example:
   
        ``AnnotateEachBreakpoint(chr1,pos1,str1,refDF)``

``FindATranscript`` function
----------------------------

.. automodule:: iAnnotateSV.FindTranscrpit.FindATranscript
    :members: 
    :undoc-members:
    :show-inheritance:
- This module will automatically find the highest preference transcript based on input:
   * **queryDF** : Its a dataframe with 
            * **c** = zone: 1=exon, 2=intron, 3=3'-UTR, 4=5'-UTR, 5=promoter
            * **d,e** = for exons: which one, and how far
            * **d1,d2,e1,e2** = for introns: between which exons and how far?
            * **f** = for introns: how many bases in the partially completed codon?,
   * **referenceDataframe** : a pandas data-frame that will store reference information
   :Example:
   
        ``FindATranscript(queryDF,refDF)``

``FindAllTranscripts`` funtion
------------------------------

.. automodule:: iAnnotateSV.FindTranscrpit.FindAllTranscripts
    :members: 
    :undoc-members:
    :show-inheritance:
- This module will find all transcripts based on input:
   * **queryDF** : Its a dataframe with 
            * **c** = zone: 1=exon, 2=intron, 3=3'-UTR, 4=5'-UTR, 5=promoter
            * **d,e** = for exons: which one, and how far
            * **d1,d2,e1,e2** = for introns: between which exons and how far?
            * **f** = for introns: how many bases in the partially completed codon?,
   * **referenceDataframe** : a pandas data-frame that will store reference information
   :Example:
   
        ``FindAllTranscripts(queryDF,refDF)``

``FindCanonicalTranscript`` module
----------------------------------

.. automodule:: iAnnotateSV.FindCanonicalTranscript
    :members:
    :undoc-members:
    :show-inheritance:
- This module will Finad a canonical transcript based on the input for main function and output of FindAllTranscripts:
   * **geneList** : List of genes [this will normally be list with same names] for the Structural Variant in question,
   * **transcriptList** : List of transcripts for the Structural Variant in question,
   * **siteList** : direction of the site for the event[either 0 or 1],
   * **zoneList** : different zones in which the event can occur [zone: 1=exon, 2=intron, 3=3'-UTR, 4=5'-UTR, 5=promoter]
   * **strandList** : direction of the read for the event[either 0 or 1],
   * **intronnumList** : Which intron the event occurs if the event is in intron for each transcript,
   * **intronframeList** : What is the frame of the intron where the event is occuring for each transcript. 
   * **ctDict** : a dictionary containing the canonical transcript information for each gene. [Gene=>Transcript]
   :Example:
   
        ``FindCT(geneList,transcriptList,siteList,zoneList,strandList,intronnumList,intronframeList,ctDict)``

``PredictFunction`` module
--------------------------

.. automodule:: iAnnotateSV.PredictFunction
    :members:
    :undoc-members:
    :show-inheritance:
- This module will predict the function of each annotated breakpoint
- It takes two pandas series which has following information:
      * **gene** : Gene for the event,
      * **transcript** : Transcript used for the event,
      * **site** : Explanation for site where the event occurs,
      * **zone** : Where does the event occur [ 1=exon, 2=intron, 3=3'-UTR, 4=5'-UTR, 5=promoter ],
      * **strand** : Direction of the transcript,
      * **str** : Direction of the read,
      * **intronnum** : Which intron the event occurs if the event is in intron,
      * **intronframe** : What is the frame of the intron where the event is occuring. 
      :Example:
      
        ``ann1S = pandas.Series([gene1,transcript1,site1,zone1,strand1,str1,intronnum1,intronframe1],index=['gene1', 'transcript1', 'site1', 'zone1', 'txstrand1', 'readstrand1', 'intronnum1','intronframe1'])``
        
        ``ann2S = pandas.Series([gene2,transcript2,site2,zone2,strand2,str2,intronnum2,intronframe2],index=['gene2', 'transcript2', 'site2', 'zone2', 'txstrand2', 'readstrand2', 'intronnum2','intronframe2'])``
        
        So **ann1S** & **ann2S** are series that will go to PredictFuntionForSV()
        
        ``PredictFunctionForSV(ann1S,ann2S)``

``helper`` module
-----------------

.. automodule:: iAnnotateSV.helper
    :members: ReadFile,ExtendPromoterRegion,bp2str
    :undoc-members:
    :show-inheritance:
- This module has multiple funtions
   1. **ReadFile()**
   
      * This will read a tab-delimited file into a panadas dataframe
   2. **ExtendPromoterRegion()**
   
      * This will extend the promoter region to a given length
   3. **bp2str()**
   
      * This will convert base pair information to string information
      
``iAnnotateSV`` main funtion
----------------------------

.. automodule:: iAnnotateSV.iAnnotateSV
    :members: processSV
    :undoc-members:
    :show-inheritance:
- This module is the driver module, it takes user information and runs all other module to produce proper structural variant annotation 

**usage: iAnnotateSV.py [options]**

**Annotate SV based on a specific human reference**

**optional arguments:**
  -h, --help            show this help message and exit
  -v, --verbose         make lots of noise [default]
  -r hg19, --refFileVersion hg19
                        Which human reference file to be used, hg18,hg19 or
                        hg38
  -o outfile, --outputFile out.txt
                        Full path with for the output file
  -i inputSVfile, --svFile svfile.txt
                        Location of the structural variants file to annotate
  -d distance, --distance 3000
                        Distance used to extend the promoter region
  -a, --autoSelect      Auto Select which transcript to be used[default]
  -c canonicalExonsFile, --canonicalTranscripts canonicalExons.txt
                        Location of canonical transcript list for each gene.
                        Use only if you want the output for specific
                        transcripts for each gene.

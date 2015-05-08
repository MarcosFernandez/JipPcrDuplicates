.. JIP PCR Duplicates Stats documentation master file, created by
   sphinx-quickstart on Tue May  5 17:10:19 2015.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

==================
JIP PCR Duplicates
==================

-----------------------------------------------------------
Script for JIP pipeline system to get Duplicates statistics 
-----------------------------------------------------------

Runs `MarkDuplicates`_ from PicardTools to calculate Duplicates statistics. Statistics are calculated per sample and library. All bam alignment files comming from the same library are previously merged to later 
perfom MarkDuplicates and get the stats.

.. note::
    No bam alignment file is generated from MarkDuplicates, its output is written to * /dev/null *

This script clusters input bam files according to the sample and library name. Then, Input bam alignment files must follow the next pattern:

    **Sample_Library_Flowcell_Lane[_Index_].bam**

The output file generated follows next name pattern:

    **Sample.Library.rmdup.stats**

.. hint::

    If you are using ``lims`` from CNAG cluster it is recommended to run next command in order to create soft links to fastq files belonging the right name. ::

        lims -s PROJECT_NAME  --status pass --link '${sample}_${library}_${flowcell}_${lane}_${index}_${pair}.fastq.gz' 


.. _MarkDuplicates: http://broadinstitute.github.io/picard/command-line-overview.html#MarkDuplicates


--------
Download
--------

Download the script from `https://github.com/MarcosFernandez/JipPcrDuplicates`_ and just run it.

.. _https://github.com/MarcosFernandez/JipPcrDuplicates: https://github.com/MarcosFernandez/JipPcrDuplicates

-----
Usage
-----

::

    duplicates_stats.jip --bam *.bam -- submit -C 5


.. warning::

    By default, the script is going to create a JIP pipeline system job which uses 25Gb of main memory. Modify it in case is not suitable for your computing resources.

    By default, the script is going to look for jar PicardTools files at ``/apps/PICARD/1.95/`` . Modify it in case is not a valid path for you system.

-------
Outputs
-------

Files generated:

+----------------------------+-----------------------------------------+
| File                       | Description                             |
+============================+=========================================+
|sample.library.rmdup.stats  |MarkDuplicates statistics file, one per  |
|                            |each sample and library.                 |
+----------------------------+-----------------------------------------+
|duplicates.html             |HTML document with a summary of PCR      | 
|                            |Duplicates statistics.                   |
+----------------------------+-----------------------------------------+
|duplicates.json             |JSON document with a summary of PCR      |
|                            |Duplicates statistics.                   |
+----------------------------+-----------------------------------------+


-----
Notes
-----

1) The script is going to generate a job which calls `MergeSamFiles`_ and `MarkDuplicates`_ tools of the Package PicardTools. If you do not have PicardTools already installed in your 
system get the last one at `https://github.com/broadinstitute/picard/releases`_

 

.. _https://github.com/broadinstitute/picard/releases: https://github.com/broadinstitute/picard/releases

.. _MergeSamFiles: http://broadinstitute.github.io/picard/command-line-overview.html#MergeSamFiles


2) PicardTools is a set of applications which runs on the Java's virtual machine. To perform the Bam Merging and the duplicates filtering the scripts are configured to use 25Gb of Java Memory Heap. You can increase or decrease
this value just editing the `-Xmx25g` value.


.. code-block:: python

            #Merging job
            merged = job("merge." + sample + "." + library, time="12h",threads="5",\
            log=directory + "/" +  "merge." + sample + "." + library + ".err", \
            out=directory + "/" +  "merge." + sample + "." + library + ".out").\
            bash(''' java -Xmx25g -Djava.io.tmpdir=$TMPDIR  -jar /apps/PICARD/1.95/MergeSamFiles.jar \
                     TMP_DIR=$TMPDIR \
                     MAX_RECORDS_IN_RAM=1500000 \
                     SORT_ORDER=coordinate \
                     USE_THREADING=true \
                     ASSUME_SORTED=true  \
                     VALIDATION_STRINGENCY=SILENT \
                     ''' + bams + ''' \
                     OUTPUT=''' + bamToCheck,
                     outfile=bamToCheck)
        
            #MarkDuplicates
            dups = job("rmDup", time="10h",threads="5",\
            log=directory + "/" +  "rmdup." + sample + "." + library + ".err", \
            out=directory + "/" +  "rmdup." + sample + "." + library + ".out").\
            bash(''' java -Xmx25g -Djava.io.tmpdir=$TMPDIR  -jar /apps/PICARD/1.95/MarkDuplicates.jar \
                     MAX_FILE_HANDLES_FOR_READ_ENDS_MAP=1000 \
                     MAX_RECORDS_IN_RAM=1500000 \
                     METRICS_FILE=''' + metrics +''' \
                     REMOVE_DUPLICATES=false \
                     ASSUME_SORTED=true  \
                     VALIDATION_STRINGENCY=SILENT \
                     CREATE_INDEX=false\
                     INPUT=''' + bamToCheck + '''\
                     COMPRESSION_LEVEL=9 \
                     OUTPUT=/dev/null ''') 



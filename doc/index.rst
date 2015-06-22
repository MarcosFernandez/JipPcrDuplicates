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

Runs `markdup`_ from sambamba to calculate Duplicates statistics. Statistics are calculated per sample and library. All bam alignment files comming from the same library are previously merged to later 
perfom `markdup`_ and get the stats.

This script clusters input bam files according to the sample and library name. Then, Input bam alignment files must follow the next pattern:

    **Sample_Library_Flowcell_Lane[_Index_].bam**

The output file generated follows next name pattern:

    **SampleName.Library.rmdup.sample.libs.flagstat**

.. hint::

    If you are using ``lims`` from CNAG cluster it is recommended to run next command in order to create soft links to fastq files belonging the right name. ::

        lims -s PROJECT_NAME  --status pass --link '${sample}_${library}_${flowcell}_${lane}_${index}_${pair}.fastq.gz' 

.. _markdup: http://lomereiter.github.io/sambamba/


--------
Download
--------

Download the script from `https://github.com/MarcosFernandez/JipPcrDuplicates`_ and just run it.

.. _https://github.com/MarcosFernandez/JipPcrDuplicates: https://github.com/MarcosFernandez/JipPcrDuplicates

-----
Usage
-----

::

    duplicates_stats.jip --bam *[00-99].bam -- submit -C 5


-------
Outputs
-------

Files generated:

+----------------------------------------------------+-----------------------------------------------------+
| File                                               | Description                                         |
+====================================================+=====================================================+
|sample.library.flowcell.lane.rmdup.lane.flagstat    |sambamba flagstats for a given alignment file.       |
+----------------------------------------------------+-----------------------------------------------------+
|sample.library.rmdup.sample.libs.flagstat           |sambamba rmdup statistics file, one per              |
|                                                    |each sample and library.                             |
+----------------------------------------------------+-----------------------------------------------------+
|duplicates.html                                     |HTML document with a summary of PCR                  | 
|                                                    |Duplicates statistics.                               |
+----------------------------------------------------+-----------------------------------------------------+
|duplicates.json                                     |JSON document with a summary of PCR                  |
|                                                    |Duplicates statistics.                               |
+----------------------------------------------------+-----------------------------------------------------+

-----
Notes
-----

1) The script is going to generate a job which calls `sambamba merge`_ , `sambamba markdup`_ and `sambamba flagstat`_ tools of the package sambamba. If you do not have sambamba already installed in your 
system get the last one at `https://github.com/lomereiter/sambamba/releases`_

 

.. _https://github.com/lomereiter/sambamba/releases: https://github.com/lomereiter/sambamba/releases

.. _sambamba merge: http://lomereiter.github.io/sambamba/docs/sambamba-merge.html

.. _sambamba markdup: http://lomereiter.github.io/sambamba/docs/sambamba-markdup.html

.. _sambamba flagstat: http://lomereiter.github.io/sambamba/docs/sambamba-flagstat.html


============
LIMS RNA SEQ
============

------------------------------------------------------------------
Script to upload RNA Seq pipeline statistics to Lims CNAG Database 
------------------------------------------------------------------

Reads alignment and library statistics files to upload a set of values to CNAG Lims Database using REST interface.

It automatically looks for **gtf lane stats** (all files with *filtered.gtf.stats.json* suffix),
**gene lane counts** (all files with *filtered.gtf.counts.txt* suffix) and **alignment lane stats** (all files with *rmdup.lane.flagstat* suffix) to upload values per mapping file.

It also looks for *JSON Duplicates statistics file* (file *duplicates.json*) to upload values per library.

--------
Download
--------

Download the script from `https://github.com/MarcosFernandez/JipPcrDuplicates/limsRnaSeq/limsRnaSeq.py`_ and just run it.

.. _https://github.com/MarcosFernandez/JipPcrDuplicates/limsRnaSeq/limsRnaSeq.py: https://github.com/MarcosFernandez/JipPcrDuplicates/limsRnaSeq/limsRnaSeq.py

-----
Usage
-----

::

    ./limsRnaSeq.py -d RnaSeqDirectory

``RnaSeqDirectory``: Directory where are located the files generated by the `gemtools rnaseq`_ pipeline.

.. _gemtools rnaseq: http://gemtools.github.io/docs/index.html

.. warning::
    **limsRnaSeq.py** works just in the acces login nodes of the cnag cluster.






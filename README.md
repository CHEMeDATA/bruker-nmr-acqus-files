# bruker-nmr-acqus-files

Please [raise an issue](https://github.com/CHEMeDATA/bruker-nmr-acqus-files/issues/new/choose) if you want to comment add information, complain, etc...

For corrections, please edit the file and make a pull request.

## Context

We should consider here, the NMR experiments, not the NMR spectra. NMR spectra are processed NMR experiments, usually by Fourier transformation. You may have multiple spectra for a single experiment even if usually, you have only one (located in the /pdata/1/ folder). NMR spectra could/should/are discussed in a different project.

In order to archive NMR experiments, it is good to be able to extract information from the parameter files, *i.e.* the `acqus` and, for 2d also from the `acqu2s` files... acqu3s for 3D. Note that the last letter "s" stands for status, it is the parameters when the experiment was started. The acqus file contatains the current parameters - they may not correspond to the recorded experiment.

When editing a `acqus` file with the Bruker software, the files may be changed slightly (space - end-of-line located at a diffent place, different zero padding of some parameters, etc.). This makes strict comparison of acqus files a bad criterion for searching for duplicates, or unique identification. Using the `ser` or the `fid` file (with md5, for example) is better to have a unique identifier. Note that you may find md5 corresponding to only zeroes (probably if the experiment is stopped before completion - when running zg, (it seems that the file is filled with zeroes to have no surprise with full disks? and then replace with real data). 

## MD5 of files

The md5 is a hash code that can be used as uniq identifier of the any file, in particular `fid` (1D experiments) and `ser` (2D experiments) files. 

On mac and linux systems
```
find "./" -name fid -exec md5 -r {} \; > listmd5.txt
```
list the md5 of all fid's located in the folders where the command is run.

## Descrition of acqus files

Obviously, depending on file systems, the end-of-line may differs... 

Bruker `acqus` files are code with the ["jcamp" format](http://jcamp-dx.org/). 

One can use exiting librairies to read jcamp files and extract important information. But some very interesting information are located in the comments associated to private LDR. We will therefore discuss this in some details here to make sure we don't miss important information.

### jcamp content

Data in the jcamp format start with `##`, before the "labelled data records" called LDR. The *label* and the *record* are separated by `=`. 
Example:
```
##TITLE= Parameter file, XWIN-NMR		Version 3.6
##JCAMPDX= 5.0
##DATATYPE= Parameter Values
##ORIGIN= UXNMR, Bruker Analytische Messtechnik GmbH
##OWNER= root
$$ Tue Jun 17 17:27:20 2008 CEST (UT+2h)  guest@nmrge400
$$ /u/data/zumbuehl/nmr/zum1-2/2/acqus
```

### jcamp comments

Note, in the example above, the comments starting with `$$` appears after the data. 

other exemples of comment found in `acqus` files:

```
...
##NPOINTS= 35	$$ modification sequence number
##ORIGIN= Bruker BioSpin GmbH
##OWNER= nmr
$$ 2017-07-04 00:22:15.088 +0200  nmr@CZC60678L0
$$ /opt/topspin3.5pl5/data/brucka/data/nmr/nmr/mb_enrichedchol/32/acqus
$$ process /opt/topspin3.5pl5/prog/mod/go4
...
```

In all cases, some comment lines are found between the non-private and private LDR. These comments are quite interesting as metadata. They allow to recover the date of acquisition, the original file name and location, and in many cases, the xwinnmr/topspin user and the system's login name and the code of the computer.

Bruker use mostly *private* LDR. They start with `##$` in instead of `##`. This gives them more freedom with respect to the standard.

Examples of private LDR
```
...
##$AMP= (0..31)
100 100 100 100 100 100 100 100 100 100 100 100 100 100 100 100 100 100 
100 100 100 100 100 100 100 100 100 100 100 100 100 100 
##$AQSEQ= 0
##$AQ_mod= 3
##$AUNM= <au_zg>
##$AUTOPOS= <>
##$BF1= 400.13
...
##$P= (0..31)
10.2 9.4 18.8 0 0 52.8 80 160 0 0 0 80000 0 0 0 200000 1500 2500 0 1500 
26 0 0 0 0 200 0 9.4 0.1 0 0 0 
...
##$TD= 65536
```
The number of points of an experiment `td` is 65536.
**Note:** Some are data are arrays. The index starts at zero. For example the value of "p1", the pulse duration, is 9.4 us.

The private content follows the non-private and seem to appear in alphabetical order. We are not aware of comments in the private LDR.

The files ends with
```
##END=
```

### Content of acqus files

### Line 1 
Examples of first line:
```
##TITLE= Parameter file,  UXNMR		Version 930301
##TITLE= Parameter file,  Version xwin-nmr1.3
##TITLE= Parameter file, TOPSPIN		Version 1.2.b.15
##TITLE= Parameter file, TopSpin 3.5 pl 2
##TITLE= Parameter file, TopSpin 4.0.b.20161214 pl 0
##TITLE= Parameter file, XWIN-NMR		Version 2.0
##TITLE= Parameter file, XWIN-NMR		Version 3.6
```
This allows to determine the name and version of the sofware.

**Note:** Two tabulations separate "UXNMR" from "Version".

### Line 2 

```
##JCAMPDX= 5.0
```
Only this version of jcamp seems to be used.

### Line 3 

```
##DATATYPE= Parameter Values
##DATATYPE= Parameter Values
##DATATYPE= UXNMR Parameter Values
```
### Lines 4 
In most cases, but not always, NPOINTS follows:
```
##NPOINTS= 1	$$ modification sequence number
##NPOINTS= 2	$$ modification sequence number
##NPOINTS= 101	$$ modification sequence number
##NPOINTS= 99	$$ modification sequence number
```
Note the presence of a comment (following the "$$")
### Lines 5 
In most cases, but not always, ORIGIN follows. 
Examples:
```
##ORIGIN= Bruker Analytik GmbH
##ORIGIN= Bruker Analytische Messtechnik GmbH
##ORIGIN= Bruker BioSpin GmbH
##ORIGIN= UXNMR, Bruker Analytische Messtechnik GmbH
```

Here is an [example](acqus_file_few_non_private_LDR.txt) without NPOINTS and ORIGIN.

One cannot consider that the comments are associated to ORIGIN, they may be associated to NPOINTS or DATATYPE. In fact they are between the non-private and private LDR.

### Comment lines
 
Most of the time two comment lines are found, sometime three.

Most of the time, the first comment line includes the date. Sometimes also the login name of the user and the name of the computer.
```
$$ 2016-08-10 16:32:25.891 +0200  nmr@CZC30384W2
$$ 2005-07-22 09:28:04.785 +0200  demo@linux500
$$ Sat Nov 11 11:11:11 1995
$$ Wed Feb 12 08:36:35 1997 MEZ (UT+1h)  guest
$$ Fri Jul 10 03:37:00 1998
$$ Fri May  3 16:56:03 1996
$$ Wed Feb 25 21:26:44 1998
$$ Wed Jun 18 05:31:06 1997
$$ 2008-10-28 07:56:23.744 +0100  guest@myspectro
$$ 2012-03-30 13:17:11.343 +0200  Administrator@AD6645B7AEF24AA
$$ 2012-07-14 08:44:36.879 +0200  guest@nmr1516
$$ Fri Apr 12 15:45:30 2013 CEST (UT+2h)  nmr@nmrge300
$$ Tue Feb 17 18:37:38 2004 "MET (UT+1h)  guest@nmrge300

```

Most of the time, the second comment line includes the location of the file. Sometimes also the login name of the user and the name of the computer.
```
$$ /opt/topspin3.5pl5/data/brucka/data/nmr/nmr/mb_enrichedchol/32/acqus
$$ /opt/xwinnmr36/data/matile/nmr/dad_5085/10/acqus
$$ /beta/data/guest/nmr/exam1d/1/acqus
```

But sometimes it is [different](acqus_file_with_no_origin_and_special_comments.txt) and the two lines are:
```
$$ $Source: /sc/CvsTree/sc/gen/src/par/par.npt/NPT_prep_inno_sino_shim/acqu,v $
$$ $Id: acqu,v 1.3 2012/11/29 09:02:01 miw Exp $
```

Example of the [some time seen](acqus_file_with_three_comment_lines.txt) third comment line
```
$$ process /opt/topspin3.5pl5/prog/mod/go4
$$ process /opt/topspin3.2/prog/mod/shimcntl

```

### typical acqus file

```
##TITLE= Parameter file, XWIN-NMR		Version 3.6
##JCAMPDX= 5.0
##DATATYPE= Parameter Values
##ORIGIN= UXNMR, Bruker Analytische Messtechnik GmbH
##OWNER= root
$$ Tue Jun 17 17:27:20 2008 CEST (UT+2h)  guest@nmrge400
$$ /u/data/zumbuehl/nmr/zum1-2/2/acqus
##$AMP= (0..31)
100 100 100 100 100 100 100 100 100 100 100 100 100 100 100 100 100 100 
100 100 100 100 100 100 100 100 100 100 100 100 100 100 
##$AQSEQ= 0
##$AQ_mod= 3
##$AUNM= <au_zg>
##$AUTOPOS= <>
##$BF1= 400.13
##$BF2= 400.13
##$BF3= 400.13
##$BF4= 400.13
##$BF5= 500.13
##$BF6= 500.13

...


##$YMAX_a= 1138137
##$YMIN_a= -1196661
##$ZGOPTNS= <>
##$ZL1= 120
##$ZL2= 120
##$ZL3= 120
##$ZL4= 120
##END=
```
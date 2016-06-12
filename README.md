## Gray-matter Based Spatial Statistics (NODDI-GBSS)
[![License](http://mirrors.creativecommons.org/presskit/buttons/88x31/svg/by-nc.svg)](LICENSE.md)
####Introduction
Gray matter-based spatial statistics (NODDI-GBSS) is a pipeline to perform voxel-wise statistical analysis on gray matter microstructure. Our method is based on GBSS method primarily introduced by Ball <i>et. al</i><sup>1</sup> and discribed elswhere in full details<sup>2,3</sup>. Unlike the original GBSS method, NODDI-GBSS only uses multi-shell diffusion-weighted images for tissue segmentation and registration<sup>2,3</sup>. NODDI-GBSS requires NODDI (http://mig.cs.ucl.ac.uk/index.php?n=Tutorial.NODDImatlab) and DTI images and depends on FSL (http://fsl.fmrib.ox.ac.uk/fsl/fslwiki/) and ANTs (http://stnava.github.io/ANTs/).

####Overview
######<i>gbss_1_reg.sh</i>	
Gray matter fraction maps are estimated in the native diffusion space by subtracting CSF fraction (fCSF maps from NODDI) and white matter fraction (estimated by two-tissue class segmentation of FA images using <i>Atropos</i>) from 1 in each voxel. To increase tissue contrasts and enhance between-subject registration steps, partial volume estimation maps for each tissue class are multiplied by their corresponding contrast (0 for CSF, 1 for gray matter, and 2 for white matter) and summed together to generate images with similar contrast to T1-weighted images. The resulting images are then used to build a study-specific template using the <i>buildtemplateparallel.sh</i> script in the <b>Advanced Normalization Tools</b> (ANTs). Gray matter fraction, ODI, and NDI images are warped to the template space using the warp fields estimated during the previous step. 

######<i>gbss_2_skell.sh</i>	
To enhance between-subject alignment of gray matter voxels, GBSS adopts the tract-based spatial statistics (TBSS) algorithm. The average gray matter fraction map was skeletonized and for each individual, diffusion metrics (i.e., ODI and NDI) and gray matter fraction were projected from local voxels with greatest gray matter fraction in the template space onto the skeleton. The final skeleton is generated by keeping only voxels with a gray matter fraction greater than a given threshold (default: 0.65) in more than a given percentage (default: 75%) of the subjects. 

######<i>gbss_3_fill.sh</i>	
The remaining voxels on the subjects’ skeletons with non-satisfactory gray matter fraction (e.g. below 0.65) are filled with the average of the surrounding satisfactory voxels on the skeleton (e.g. gray matter fraction>0.65) weighted by their closeness with a Gaussian kernel (default: σ=2 mm).

####Installation:
NODDI-GBSS scripts rely on FSL (4.1.9 or higher) and ANTS (v2.1). Simply clone the scripts as follows:
```bash
git clone https://github.com/arash-n/GBSS
```
Add the GBSS/NODDI folder to the $PATH. 

####Usage:
Run the scripts sequentially (from NODDI folder).

######First Step:
For <i>gbss_1_reg.sh</i>, outputs from the NODDI and DTI models should be already available.This script works as follows:

a) The input older containing the following subdirectories: FA, CSF, ODI, fIC.
b) Each Folder should contain corresponding image files with the same subject name in all folders.

<i>NOTE</i>: Remove any underline (_) from your filenames.
```bash
gbss_1_reg.sh [options] output_directory
```
######Second Step:
<i>NOTE</i>: cd to output_directory
```bash
gbss_2_skel.sh
```
######Third Step:
<i>NOTE</i>: cd to stats folder
```bash
gbss_3_fill.sh
```

####Citations:
1. Ball G, Srinivasan L, Aljabar P, Counsell SJ, Durighel G, Hajnal JV et al. Development of cortical microstructure in the preterm human brain. <i>PNAS</i>; 110(23): 9541-9546.
2. Nazeri A, Chakravarty MM, Rotenberg DJ, Rajji TK, Rathi Y, Michailovich OV et al. Functional Consequences of Neurite Orientation Dispersion and Density in Humans across the Adult Lifespan. <i>J Neurosci</i> 2015; 35(4): 1753-1762.
3. Nazeri A, Mulsant BH, Rajji TK, Levesque ML, Pipitone J, Stefanik L, Roostaei T et al. Gray matter neuritic microstructure deficits in schizophrenia and bipolar disorder. <i>Submitted Manuscript</i>.



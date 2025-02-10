# Pretrained network for 9 macaca fasicularis Brain extraction from MRI

Use the pretrained network 'MonkeyMRIWeigths_PostTraining.hdf5' with BEN  (paper: https://elifesciences.org/articles/81217)

**Diffusion MRI Paper Under Review title "In vivo submillimeter diffusion MRI dataset of 9 macaque brains curated for tractography"**


# Retraining the Network Workflow: DICOM to NIfTI, Denoising, Mask Generation, and Training
This guide outlines the steps for processing MRI data, including converting DICOM files into NIfTI format, denoising MRI images, generating brain masks using BEN (Brain Extraction Net), training the network, and refining segmentation results. The tools used include ITK SNAP, Matlab, Python, and BEN.
## Steps:
1. Convert DICOM Files to NIfTI Format
* Open MRI DICOM images using ITK SNAP.
* Save the image as a .nii file:
** Go to the File menu → Save Image → Choose Main Image.
* Move the resulting .nii images to the Input_Raw folder for further processing.
2. Denoise the NIfTI Files
* Use the MRIDenoisingPackage_MATLAB to denoise the NIfTI files:
** Open MainMRIDenoising.m from the package.
** Select the .nii files and choose AOLMN as the denoising method.
** After denoising, move the denoised images to the Input_Raw_Denoised folder.
3. Run BEN Inference to Get Initial Masks
* Execute the following Python command to run the BEN inference:
bash
CopyEdit
python BEN_infer.py -i Data_Complete\Input_RAW_Denoised -o Data_Complete\Masks_NetworkOutput -weight Data_Complete\Network\MonkeyMRIWeights_untrained.hdf5 -check RPI

* The initial segmentation masks will be outputted to the Masks_NetworkOutput folder.
4. Check the Masks
* Go to the Masks_NetworkOutput folder and check the segmentations:
** Load the segmentations onto the Raw_Denoised images in ITK SNAP to visually inspect the masks.
5. Refine the Segmentation (If Needed)
* If the segmentation is not optimal, manually refine the segmentation using ITK SNAP.
6. Save Refined Segmentation and Denoised Files
* Save the refined segmentation in the Training_Label folder.
* Move the corresponding denoised files to the Training_Dataset folder.
7. Train the Network
* Execute the following command to train the network:
bash
CopyEdit
python BEN_DA.py -t Data_Complete\Training_Dataset -l Data_Complete\Training_Label -r Data_Complete\Input_Raw_Denoised -weight Data\DataNew\Network\MonkeyMRIWeights_untrained.hdf5 -prefix TrainedWeights -check RPI

8. Rename the Newly Trained Weights
* After training, locate the newly trained weights in the BEN\weights\TrainedWeights folder.
* Rename the file to MonkeyMRIWeights_PostTraining.hdf5.
9. Run Inference with New Weights
* Rerun the inference with the newly trained weights using the following command:
bash
CopyEdit
python BEN_infer.py -i Data_Complete\Input_RAW_Denoised -o Data_Complete\Masks_NetworkOutput -weight Data_Complete\Network\MonkeyMRIWeights_PostTraining.hdf5 -check RPI

10. Check the New Segmentation Masks
* Recheck the segmentations as you did in Step 4.
11. Refine and Smooth the Masks
* If manual refinements are needed, perform them in ITK SNAP.
* To smooth the segmentations:
** Go to the Tools menu → Smooth Labels.
** Set the standard deviation to 1, 1, 1 mm.
** Check Smooth All Frames to apply the smoothing to all slices.
12. Final Refinements and Consistency Check
* After smoothing, recheck the segmentations:
** If the masks are acceptable, you can skip further refinement.
** If needed, perform the refinement steps again (Repeat Step 10 if necessary to ensure consistency).

## Tools Used:
1. MRIDenoisingPackage_MATLAB
A Matlab-based tool for denoising MRI data. 
MRIDenoisingPackage_MATLAB (https://sites.google.com/site/pierrickcoupe/softwares/denoising/mri-denoising/mri-denoising-software?authuser=0)

2. ITK SNAP
A tool for segmentation and visualization of 3D medical images.
ITK SNAP (http://www.itksnap.org/pmwiki/pmwiki.php)

3. BEN (Brain Extraction Net)
A generalized brain extraction network for multimodal MRI data from rodents, nonhuman primates, and humans.
BEN GitHub Repository (https://github.com/yu02019/BEN)

4. Anaconda Backup Environment
The environment backup for the BEN system is saved in the BEN_Environment_Backup.yaml file.




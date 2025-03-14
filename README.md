# ph3-analysis
Analyse data for ph3 experiments.

# Requirements
You will need to have the cp-utils-hmv toolkit to use the notebook:
```
pip install cp-utils-hmv
```

# Workflow for doing the entire analysis of PH3 data
0. This assumes an acquisition of data with 4 channels in the slide scanner at 20x.
  - Make sure the name of the .czi files is 'Animal-X-Y-Z_Experimental-Procedure_slide-X.czi
  - The underscores are very important. Don't have spaces. The last part (e.g. '..._slide-1') is also crucial.

1. Use https://github.com/HernandoMV/czi-extract-slices in Fiji to export slices. (Using channel 4 and 10um/px)

2. Register using ABBA and save transformation field and atlas annotations: https://abba-documentation.readthedocs.io/en/latest/contents.html#
  - Create a folder called 'QuPath' inside the 'Registration' folder just created by the previous script
  - Open QuPath, and drag the folder to create a new project. Add the images created by the script, selecting BioFormats-builder. Close QuPath.
  - Open ABBA in Fiji and import the QuPath project.
  - Flip the axis as the ABBA atlas is the other way around
  - Set slice thickness to 1 micrometer!
  - Register once with affine, using channel 1 of the atlas (autofluorescence). Set the proper background value!
  - Register with spline with 10 landmarks, also correcting background and correct registration
  - Export regions to file, and export atlas coordinates to imageJ.
  - Save inside the same folder as the original images

USEFUL: A similar workflow as the described above is shown in the following Abba Tutorial Youtube Video: https://www.youtube.com/watch?v=sERGONVw4zE&t=1s

3. Use https://github.com/HernandoMV/czi-roisplitter to generate the ROIs, loading the region of interest (e.g. Caudoputamen)

4. Use 'Group_convert_and_enhace.py' in https://github.com/HernandoMV/Fiji_Custom, which transforms the images into 8-bit and normalizes the intensities by channel and by animal

5. Use 'ImageSequence_Downsampler.ijm' in the Fiji_Custom repo to make a copy of both the DARPP-32 channels and the tdTomato (d2) channels in different directories (needed for cellpose). Do not downsample as we are acquiring at 20x.
  - Alternatively, just copy both channels to separate folders so you don't have to duplicate data

6. Run cellpose (https://github.com/MouseLand/cellpose) on both directories: e.g. python -m cellpose --dir /home/hernandom/data/Microscopy_Data/Plasticity/PH3_inmuno/Processed_data/PH308/ROIs--Gce_processed--downsized-1_fileend-2.tif/ --save_tif --no_npy --diameter 38 --pretrained_model cyto --chan 0 --use_gpu

7. Run Inmuno_4channels_XXXXXX.cpproj (https://github.com/HernandoMV/CellProfiler_AnalysisPipelines) in CellProfiler. Modify saving paths accordingly.

8. Run the notebook PH3_Analysis.ipynb, for each mouse.


TODO: Create a google colab notebook for testing.

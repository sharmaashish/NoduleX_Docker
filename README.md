# NoduleX_Docker
Containerize the process from https://github.com/jcausey-astate/NoduleX_code

# Step 1: Build dataset
will create dir in /data/ called "built_dataset"  
/NoduleX_Docker/dicom_and_image_tools/simplify-doi-structure.sh -s /NoduleX_Docker/data/DOI /NoduleX_Docker/data/built_dataset  
"DOI" contains the proginal data downloaded from https://wiki.cancerimagingarchive.net/display/Public/LIDC-IDRI  
"built_dataset" contains dataset built to a flattened directory structure  

# Step 2: Create Segmentation Masks(DICOM format)
(temporarily upload zip file from host to container)  
will create dir in /data/ called "binary_dicom"  
process in batch: create_seg_masks.sh  
'''  
for n in `ls -d data/built_dataset/*` ; do \
    echo "Converting nodule $n" ; \
    python dicom_and_image_tools/segment_to_binary_image.py --candidates data/nodule_lists/S1vS45_TRAIN_candidates.txt --segmented-only \
        "$n" \
        "data/binary_dicom/$(basename $n)" \
        && echo "OK" \
        || echo "FAILED converting nodule $n" \
;done  
’‘’  
To build the docker image, run  
docker build -t segmask .  

# Step 3: Convert DICOM to Analyze format
will create dir in /data/ called "binary_analyze"  
run: dicom_to_analyze.sh  
'''  
p=LIDC-IDRI-0011; \
for n in `ls -d data/binary_dicom/$p/*` ; do \
    echo "Converting nodule $n" ; \
    python dicom_and_image_tools/dicom_to_analyze.py \
        "$n" \
        "data/binary_analyze/$p/$(basename $n)" \
        && echo "OK" \
        || echo "FAILED converting nodule $n" \
;done  
'''  
To build docker image, run  
docker build -t analyze .  
 
# Step 4: QIF feature extraction
This part involves dockerizing Feature Extraction process.  
TODO: wirte a script to change directory structure and naming convention in order to run the code properly.  
Docker image already built, run  
docker build -t octave .  

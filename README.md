# ULS23 challenge model container

## Introduction
This repository is a ready-to-submit template for the ULS (Universal Lesion Segmentation) challenge on Grand Challenge. It packages your trained nnUNet model for automatic evaluation. Specifically, this is for models trained with a smaller input size (64×128×128). The code automatically crops challenge images to fit, runs your model, and pads results back so that they can be evaluated on the original data. This is useful, because training on smaller images is much faster (: 

## Data and other resources
Everything else to get started can be found here: https://doi.org/10.5281/zenodo.15226100. This zenodo link has three things: 
- fully_annotated_data.zip: The original challenge data has already been cropped for you, you can find that in this zip file. Along with being cropped, we left out the semi-annotated part of the original dataset. This is also for extra speed. You can expect a slight drop in performance because of the smaller and fewer images, but this will NOT affect your grade. We far prefer a well-explained model that doesn't do much better than the baseline, than a model that does far better but has no proper documentation/explanations. 
- nnUNet_results.zip: These are the model weights of the baseline model that was trained on this cropped data. It's also slightly more shallow than the original ULS baseline model. You can use this to test how this repo works, and to see what format we expect. Your goal in essence is to improve on this model. On the challenge leaderboard, this model is named 'ULS_fast_for_challenge'.
- stacked_voi_sample.mha and stacked-3d-volumetric-spacings.json: This is a test image and its corresponding spacing. If you want to try out your model, you can use these to do it. The ULS23 challenge works with these stacked mha files and spacings jsons, so that's why you need this specific image and spacing to see if your model works properly on GC. More info on this below. 

## How to use this repo
You can use this repo in two ways:

**For submission**: You upload this repo, along with a tar.gz file containing your model weights, to Grand Challenge to evaluate your model on that platform.

**For trying out**: You have this repo on your local computer, you add your weights to this repo, and see if everything runs properly locally by running it on one image. This is useful for debugging, because on Grand Challenge it might take a while to see your errors. It is however also possible to do this on GC, you can decide this for yourself.

## Quick Start: Submit to Grand Challenge
### Prerequisites
- A trained nnUNet model (your `nnUNet_results` folder). If you're not familiar with nnUNet, please have a look at their documentation: https://github.com/MIC-DKFZ/nnUNet/tree/master
- A Grand Challenge account (free, you can sign up at [grand-challenge.org](https://grand-challenge.org)). 
- An algorithm page on Grand Challenge (which you can make by clicking on 'Add a new Algorithm' on the Algorithm tab)

### Steps to Submit
1. Zip your nnUNet model into a `.tar.gz`.
2. Fork this repo so you have your own version.
3. Make sure the model dataset name is correct in the config.json file. Automatically, when building the docker container, nnunet_results is copied to opt/ml/model/, so you can keep this part.
4. Go to the page of your algorithm.
5. Upload your model tar.gz under 'Model'.
6. Connect your version of this repo under 'Containers'.
7. Tag your repo (meaning make a release version. Grand Challenge sees these releases automatically).
8. Go to 'Try out algorithm', and see if this all works by uploading the stacked_voi_sample.mha that's on the zenodo page, along with the stacked_spacing_sample.json that's in this repo under architecture/input/. You can use this image as a debugging case.
9. If you aren't getting any errors, your model is ready to be submitted to the ULS23 challenge! That can be done on the challenge page: https://uls23.grand-challenge.org/

## Optional: Local Testing with Docker
If you want to test locally before submitting, you have to use Docker locally. As mentioned before, this can also be done on GC, but locally might go a bit faster in terms of debugging. You will have to work a bit with Docker with this, so it might also be good practice (: 

### Prerequisites for Testing
- A trained nnUNet model.
- Docker: See [docker.com](https://www.docker.com/get-started). We recommend using Docker Desktop in combination with the VSCode extension of Docker. If you have this, you just need to right click on a dockerfile to build the container instead of using the terminal. In Docker Desktop, you can then see your builds and interact with them.

### Setup for local testing
1. Clone this repo locally.
2. Set LOCAL_BUILD in the dockerfile to "true".
3. Put your nnUNet results in `architecture/nnUNet_results/`. The folder structure should look like this:
   ```
   architecture/
   └── nnUNet_results/
       └── Dataset000_Name/
           └── ...
   ```

4. Make sure the model dataset name is correct in the config.json file. Automatically, when building the docker container, nnunet_results is copied to opt/ml/model/, so you can keep this part.
5. Put the sample input image `stacked_voi_sample.mha` from the zenodo page in `architecture/input/images/stacked-3d-ct-lesion-volumes/` (the `stacked_3d-volumetric-spacings.json` is already in the right place).
6. If you want to do CPU testing instead of GPU, change `device` in `process.py` from `"cuda"` to `"cpu"`.
7. Build the container using Docker.
8. Run the container using Docker.

## More about this repo
This section is just some more info on what this repo actually does. Not necessary to know, but for your information. 

1. **Load Input**: Reads `.mha` images and spacing JSON.
2. **Crop**: Crops to 64×128×128.
3. **Predict**: Runs your nnUNet model.
4. **Pad**: Adds back to original size.
5. **Save**: Outputs stacked masks.

### Customization
The `config.json` contains all the cropping and padding settings. You technically don't have to change anything here except the path to your model, but it's good to know.

### File Structure
- `process.py`: Inference script.
- `config.json`: Settings.
- `architecture/`: Your weights and test data, along with some nnunetv2 extensions the baseline model uses.
- `Dockerfile`: Main file that you run to build the container.
- `scripts/build.sh`: Build script.

## Important!
It's always possible to get some versioning errors with nnunetv2 or python, since packages are constantly being updated. Generally, it's smart to use the same package versions as this repo uses. We highly recommend trying to upload your model weights early in the process, before having done all the training, just to be sure everything works. You could for example just take your second epoch weights and try it. This way, you prevent errors later on! If you use the baseline weights to test it, you might get version warnings (but it should still run). This is expected behavior, so nothing to worry about (:

Good luck!

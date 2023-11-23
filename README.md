# Repository to create spinal cord template of dogs
The dog templates are created by using the [Template](https://github.com/neuropoly/template) framework.


## Dog Data Overview:
- Data from VirginiaTech by Dr. Richard Shinn.
- The dataset has 5 subjects in total.
- T1w and T2w contrast images for all the subjects.
The data is stored at:
~~~
data.neuro.polymtl.ca:datasets/template_dog_virginiatech
~~~


## Dog Template Generation Pipeline Overview:
The raw dog data was first organised into the [BIDS](https://bids-specification.readthedocs.io/en/stable/) dataset format, which was then followed by the first pipline: `Data Pre-processing`. The segmentation masks/centerlines and disc labels were obtained for each subject in the dataset. These derivatives were then provided to the [`preprocess_normalize.py`](https://github.com/neuropoly/template/blob/a7915f4ccfa075a5d31f4ea84bb9761d42710e9e/preprocess_normalize.py) pipeline which generated more derivatives such as the straightened spinal cord, template space etc. Finally, these derivatives were provided as input to the second pipline `Template Creation`. The script [`generate_template.py`](https://github.com/neuropoly/template/blob/a7915f4ccfa075a5d31f4ea84bb9761d42710e9e/generate_template.py) ran for the requested number of iterations and generated the final template. Both of the mentioned pipleine were run for both contrast - T1w and T2w separately.

![Screen Shot 2023-09-26 at 2 45 47 PM](https://github.com/spinalcordtoolbox/template-dog/assets/25586344/3f87b720-f1b8-498c-a80f-b641b79c33f8)

## Reproduce the T1w and T2w templates:

### 1. Clone this repository:
~~~
git clone https://github.com/spinalcordtoolbox/template-dog
cd template-dog
~~~

### 2. Download the data (internal)
> [!NOTE]  
> If you are an external user, please open an issue regarding data access and we will get back to you.
~~~
git clone git@data.neuro.polymtl.ca:datasets/template_dog_virginiatech
cd template_dog_virginiatech
git checkout 0db2a22ebbabd8aaa907563419accec118f12963
git annex get .
cd ..
~~~

### 3. Clone the template repository:
~~~
git clone https://github.com/neuropoly/template
cd template
~~~
> [!NOTE]  
> Depencies like `Spinal Cord Toolbox`, `Scoop` installation have not been mentioned here, please go through the [template](https://github.com/neuropoly/template) repository for the setup.

### 4. Running the `preprocessing` pipeline:
Once the `template` repository is setup, you are ready to the preprocessing pipeline in order to get the different derivatives such as `straightened_spinalcord` and `template space` which will be used for generating the templates in the next steps. 

~~~
mv ./configuration_T1w.json .
mv ./configuration_T2w.json .
python preprocess_normalize.py configuration_T1w.json
python preprocess_normalize.py configuration_T2w.json
~~~

### 5. Setup data on Digital Research Alliance of Canada (DRAC):
The above step should have generated all the required derivatives. 
~~~
Login into the Digital Research Alliance of Canada (the Alliance) High-Performance Computer (HCP)
5.1. cd scratch
5.2. mkdir template-dog
5.3. cd template-dog
5.4. git clone https://github.com/neuropoly/template

Copy derivatives from the template_dog_virginiatech dataset to scratch. You can either use SCP or simply drag and drop.

5.5. cd template
5.6. Make changes to the subjects.csv path in the generate_template.py script. It is expected that to find the subjects.csv file inside derivatives/template
5.7. Make chnages to the path names in the subjects.csv
~~~

### 6. Template generation:
> [!NOTE]  
> Please follow the dependency instructions to setup dependencies on DRAC from the [template](https://github.com/neuropoly/template) repository (Steps 2)

~~~
6.1. Create a template_pipeline.sh file
6.2. Copy paste the following into the above created file

#!/bin/bash
python -m scoop -vvv generate_template.py

6.3. sbatch --time=24:00:00  --mem-per-cpu 4000 template_pipeline.sh
~~~

### 7. Conversion MNC to NII:
Inside the template folder, you are expected to find a `model_n_all` folder in which the tem,plate iterations are saved. After the pipeline has finished running, the `.mnc` file needs to be converted to `.nii` format in order to get the final template. The pipeline would give outputs with the name: avg.XXX.mnc, where `XXX` is the nth iteration. To convert it to the `.nii` format, run the following command:

~~~
mnc2nii PATH_TO/avg.XXX.mnc PATH_TO/template_XXX.nii
~~~



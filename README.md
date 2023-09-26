# template-dog
Repository to create spinal cord template of dogs. Data from VirginiaTech.

## Dog Data Overview:
The dataset has 5 subjects in total. We have the T1w and T2w contrast images for all the subjects.
The data is (currently) stored under:
~~~
duke:temp/rohan/bids_data
~~~

## Dog Template Generation Pipeline Overview:
The raw dog data was first organised into the [BIDS](https://bids-specification.readthedocs.io/en/stable/) dataset format, which was then followed by the first pipline: `Data Pre-processing`. The segmentation masks/centerlines and disc labels were obtained for each subject in the dataset. These derivatives were then provided to the [`preprocess_normalize.py`](https://github.com/neuropoly/template/blob/a7915f4ccfa075a5d31f4ea84bb9761d42710e9e/preprocess_normalize.py) pipeline which generated more derivatives such as the straightened spinal cord, template space etc. Finally, these derivatives were provided as input to the second pipline `Template Creation`. The script [`generate_template.py`](https://github.com/neuropoly/template/blob/a7915f4ccfa075a5d31f4ea84bb9761d42710e9e/generate_template.py) ran for the requested number of iterations and generated the final template. Both of the mentioned pipleine were run for both contrast - T1w and T2w separately.

![Screen Shot 2023-09-26 at 2 45 47 PM](https://github.com/spinalcordtoolbox/template-dog/assets/25586344/3f87b720-f1b8-498c-a80f-b641b79c33f8)

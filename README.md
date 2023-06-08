# Physics-constrained deep learning for climate downscaling

This code belongs to a paper currently under review, a preprint can be found at: https://arxiv.org/pdf/2208.05424.pdf

Abstract: *The availability of reliable, high-resolution climate and weather data is important to inform long-term decisions on climate adaptation and mitigation and to guide rapid responses to extreme events. Forecasting models are limited by computational costs and, therefore, often generate coarse-resolution predictions. Statistical downscaling can provide an efficient method of upsampling low-resolution data. In this field, deep learning has been applied successfully, often using image super-resolution methods from computer vision. However, despite achieving visually compelling results in some cases, such models frequently violate conservation laws when predicting physical variables. In order to conserve physical quantities, we develop methods that guarantee physical constraints are satisfied by a deep learning downscaling model while also improving their performance according to traditional metrics. We compare different constraining approaches and demonstrate their applicability across different neural architectures as well as a variety of climate and weather data sets. While our novel methodologies enable faster and more accurate climate predictions, we also show how they can improve super-resolution for satellite data and standard data sets.*

## Setup

Clone the repository and install the requirements
```sh
$ git clone https://github.com/RolnickLab/constrained-downscaling.git
$ cd constrained-downscaling
$ conda env create -f requirements.yml
$ conda activate constrained-ds
```

## Get the data

One of our main data sets, ERA5 total columnt water, 4x upsampling, can be downloaded in a ML-ready form at: https://drive.google.com/file/d/1IENhP1-aTYyqOkRcnmCIvxXkvUW2Qbdx/view?usp=sharing

You can use wget:
```sh
$ wget --load-cookies /tmp/cookies.txt "https://docs.google.com/uc?export=download&confirm=$(wget --quiet --save-cookies /tmp/cookies.txt --keep-session-cookies --no-check-certificate 'https://docs.google.com/uc?export=download&id=1IENhP1-aTYyqOkRcnmCIvxXkvUW2Qbdx' -O- | sed -rn 's/.*confirm=([0-9A-Za-z_]+).*/\1\n/p')&id=1IENhP1-aTYyqOkRcnmCIvxXkvUW2Qbdx" -O era5_sr_data.zip && rm -rf /tmp/cookies.txt
```

then unzip
```sh
$ mkdir data/
$ unzip -o era5_sr_data.zip -d data/
$ rm era5_sr_data.zip 
```

Other data sets are available upon request from the author or can be generated by using public sources for ERA5 (https://cds.climate.copernicus.eu/cdsapp#!/dataset/reanalysis-era5-single-levels?tab=form.) and NorESM (https://esgf-index1.ceda.ac.uk/search/cmip6-ceda/) data.


## Run training 

To run our standard CNN withour constrained run

```sh
$ python main.py --dataset era5_sr_data --model cnn --model_id twc_cnn_noconstraints --constraints none
```

to run with softmax constraining (hard constraining) run

```sh
$ python main.py --dataset era5_sr_data --model cnn --model_id twc_cnn_softmaxconstraints --constraints softmax
```

to run with soft constraining run, with a factor of alpha run

```sh
$ python main.py --dataset era5_sr_data --model cnn --model_id twc_cnn_softconstraints --constraints soft --loss mass_constraints --alpha 0.99
```

For other setups: 
--model can be either cnn, gan, convgru, flowconvgru (last two require different data sets)
--constraints can be none, softmax, scadd, mult, add, soft
other arguents are --epochs, --lr (learning rate), --number_residual_blocks, --weight_decay

## Run inference

An example evaluation for the unconstrained model:

```sh
$ python main.py --training_evalonly evalonly --dataset era5_sr_data --model cnn --model_id twc_cnn_noconstraints --constraints none
```

It produces a csv file with all metrics on either validation or test set.

## Citation

If you find this repository helpful please consider to cite our work

    @misc{harder2022,
    author = {Harder, Paula and Yang, Qidong and Ramesh, Venkatesh and Sattigeri, Prasanna and Hernandez-Garcia, Alex and Watson, Campbell and Szwarcman, Daniela and Rolnick, David},
      title = {Generating physically-consistent high-resolution climate data with hard-constrained neural networks},
      publisher = {arXiv}, 
      year = {2022}
    }
    




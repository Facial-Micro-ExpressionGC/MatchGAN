# MatchGAN: A Self-supervised Semi-supervised Conditional Generative Adversarial Network
This repository is the official implementation of "MatchGAN: A Self-supervised Semi-supervised Conditional Generative Adversarial Network".

![alt text](https://github.com/justin941208/MatchGAN/blob/master/images/qualitative.png?raw=true)

This repository is built upon the framework of [StarGAN](https://github.com/yunjey/stargan).

## 1. Cloning the repository
Clone the repository and navigate to it.
```bash
$ git clone https://github.com/justin941208/MatchGAN.git
$ cd MatchGAN/
```

## 2. Installing requirements
The following libraries should be separately installed. Instructions are available on their respective websites:
* Python 3.7+ (https://www.python.org/downloads/)
* PyTorch 1.4.0+ with GPU support (https://pytorch.org/)
* TensorFlow 1.14.0+ with GPU support (https://www.tensorflow.org/)

Additional requirements can be installed by running:
```setup
pip install -r requirements.txt
```

To evaluate MatchGAN using GAN-train and GAN-test, the following files should be downloaded and unzipped directly under `MatchGAN/`.
* checkpoints_train_on_real.zip (https://drive.google.com/file/d/1_b7mVdg0RNdGzgSiUus0PaDcBmo6ot1E/view?usp=sharing).
* tfrecords_test.zip (https://drive.google.com/file/d/1x46OAVW9u519mLI_88d-g2ENA5yUmVaz/view?usp=sharing).


## 2. Downloading the datasets
To download the CelebA dataset:
```bash
$ bash download.sh
```
In addition, the partition file `list_eval_partition.txt` should be downloaded from the [official CelebA google drive](https://drive.google.com/file/d/0B7EVK8r0v71pY0NSMzRuSXJEVkk/view?usp=sharing) and placed immediately under the directory `./data/celeba/`.

To download the RaFD dataset, one must request access to the dataset from [the Radboud Faces Database website](http://www.socsci.ru.nl:8180/RaFD2/RaFD?p=main). Once all the image files are obtained, they need to be placed under the subdirectory `./data/RaFD/data`. To preprocess the dataset, run the following command:
```bash
$ python preprocess_rafd.py
```
This will crop all images to 256x256 (centred on face) and split the data into 90% for training and 10% for testing.

## 3. Training
The command format for training MatchGAN is given by:
```bash
$ ./run [dataset] [mode] [labelled percentage] [device]
```
For example, to train MatchGAN on CelebA with 5% of the training examples labelled on GPU 0, run the following command:
```bash
$ ./run celeba train 5 0
```
To train on RaFD, simply replace "celeba" by "rafd".

## 4. Testing and evaluating

To test MatchGAN following the above example on CelebA, run the command
```bash
$ ./run celeba test 5 0
```
This will generate synthetic images from the test set and save them to the directory `./matchgan_celeba/results`.

To evaluate the model using Frechet Inception Distance (FID), Inception Score (IS), and GAN-test, run the following command:
```bash
$ ./run celeba eval 5 0
```
The following commands trains an external classifier using the synthetic images generated by MatchGAN and then evaluates GAN-train.
```bash
$ ./run celeba synth 5 0
$ ./run celeba synth_test 5 0
```


## 5. Pretrained model
Pretrained models of MatchGAN (generator only) can be downloaded from this [link](https://drive.google.com/file/d/1UyXLOusxjCJCRy7CcrNrRXbOIo-ZFVGs/view?usp=sharing). To test or evaluate these models, the checkpoint file `200000-G.ckpt` should be placed under the directory `./matchgan_celeba/models` (for CelebA) or `./matchgan_rafd/models` (for RaFD) before running the relevant commands detailed above.

## 6. Results
Here are some of the results of our pre-trained model from the previous section.
### FID
| Percentage of training data labelled | 1%    | 5%   | 10%   | 20%  | 50%  | 100% |
| ---                                  | ---   | ---  | ---   | ---  | ---  | ---  |
| CelebA                               | 12.31 | 9.34 | 8.81  | 6.34 | -    | 5.58 |
| RaFD                                 | -     | -    | 22.75 | 9.94 | 6.65 | 5.06 |

### IS
| Percentage of training data labelled | 1%    | 5%   | 10%   | 20%  | 50%  | 100% |
| ---                                  | ---   | ---  | ---   | ---  | ---  | ---  |
| CelebA                               | 2.95  | 2.95 | 2.99  | 3.03 | -    | 3.07 |
| RaFD                                 | -     | -    | 1.64  | 1.61 | 1.59 | 1.58 |

### GAN-train and GAN-test
These numbers are obtained under the 100% setup.
|        | GAN-train | GAN-test |
| ---    | ---       | ---      |
| CelebA | 87.43%    | 82.26%   |
| RaFD   | 97.78%    | 75.95%   |

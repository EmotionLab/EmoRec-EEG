---
title: Challenge
weight: 5
omit_header_text: true
description: We'd love to hear from you
featured_image: '/images/Dalle4.webp'
type: page
menu: main

---

## Challenge rules and instructions 
The aim of the challenge is to foster generalizability of EEG-based emotion-recognition approaches. To this end, the challenge uses the four most common datasets in the field of EEG-based emotion recognition (see table below). **The framework supports dataset uploading in one line of code, but you need to have downloaded the datasets first.** All of these sets are freely available from the respective authors. We are in contact with the authors of all datasets. If you have trouble to gain access to a specific dataset, please let us know.

---
| Dataset | Electrodes | No of Channels | Sampling Rate | N Subjects | N Trials | Trial Duration |   Labels   | Label Scale |
|---------|:----------:|:--------------:|:-------------:|:----------:|:--------:|:--------------:|:----------:|:-----------:|
| MAHNOB  |     Wet    |       32       |      256      |     27     |    20    |     1-2 min    | 1-9+verbal |   discrete  |
| SEED    |     Wet    |       62       |      1000     |     15     |    15    |    ca. 4 min   |   verbal   |   discrete  |
| SEED IV |     Wet    |       62       |      200      |     15     |    24    |    ca. 2 min   |   verbal   |   discrete  |
| DREAMER |     Dry    |       14       |      128      |     23     |    18    |    ca. 3 min   |     1-5    |   discrete  |
---
The challenge contains two tasks. Submissions to the Challenge Track of the workshop have to address at least one of those two challenges:

1. Person-dependent task: In this task, data from the same participant is used to train and test the models. The specific rules for this task are:
   - Custom EEG pre-processing steps (e.g., filtering, downsampling, windowing) may be used, but these must be consistent accross datasets, meticulously documented and thus reproducible.
   - The leave-one-trial out (LOTO) cross-validation needs to be used for training/testing.
   - Importantly, the same procedure must be used for all of the participants contained in the four datasets.
   - For each participant in the four datasets, weighted F1 must be calculated, separately for valence and arousal. Subsequently weighted F1-scores must be averaged across participants (separately for valence and arousal).
   - The mean of weighted F1 for valence and arousal will be the critical measure in order to rank the submissions and to declare the winner.
   - The code used to train the models needs to be part of the submssion in order to rule out adjustment of training procedures on the individual test sets.

2. Person-independent task: The task is to create an EEG-based emotion recognition approach that generalizes across subjects from different datasets. The specific rules for this challenge are:
   - Custom EEG pre-processing steps (e.g., filtering, downsampling, windowing) may be used, but these must be consistent accross datasets, meticulously documented and thus reproducible.
   - A fixed train-test split has to be used and it is defined in the framework. The split contains 75% of participants for the training and 25% for the test set. This means that data from a given participant are either part of the training or the test set - never of both sets. The models can be tested on mixed participants from all datasets and/or on participants from the same dataset. 
   - During training, all datasets may be used jointly to improve performance.
   - As evaluation metrics, accuracy, weighted F1, and confusion matrizes will be available in the framework. The mean of the weighted F1 for valence and arousal will be the critical measure to rank the submissions and declare the winner.
   - The code used to train the models needs to be part of the submssion in order to rule out optimization on the test set.

**The first three ranking papers on each challenge will receive winner's certificates.**

## Hyper-parameter tuning

Hyper-parameters may not be tuned to the test split of each individual dataset because this strategy does not allow for generalization across datasets. Instead, one of the following strategies must be applied:

1. Fixed hyper-parameters are used across the test splits of the different datasets.
2. From the training split of each dataset, a specific proportion is retained as validation set in order to tune hyper-parameters for each dataset.

## EEGAIN framework

We provide the EEGAIN framework [here](https://github.com/EmotionLab/EEGain) for participants to use in the challenge. The EEGAIN framework substantially facilitates the programming effort it takes to run your own experiments. It includes standardized methods for data pre-processing, data splitting, evaluation metrics, and the ability to
load the four datasets used in the challenge (see below) with only a single line of code. Here is a short introduction on how to use it:

1. Clone the github repository and install all libraries contained in requirements.txt.
2. Run the ssh file: 
```bash
run_cli.sh
```

3. Within the ssh-file, arguments are given to 
```bash
run_cli.py <arguments>
```
You can adapt these arguments within the sh file according to your specific intentions:

* `--model_name` Selects a custom or pre-trained model. The pretrained implemented pre-trained models are: 
    - `TSception`
    - `EEGNet`
    - `DeepconvNet`
    - `Shallow`
* `--data_name` Chooses a custom name or a name of predefined datasets. The predefined datasets are:
    - `DEAP`
    - `MAHNOB`
    - `AMIGOS`
    - `DREAMER`
	- `SEED`
	- `SEEDIV`
* `--data_path` Specifies the directory where the data files are saved.
* `--log_dir` Specifies the directory where the log files will be saved.
* `--overal_log_file` Specifies the name of the log file that will be created.
* `--label_type` Specifies whether data is separated into classes based on valence or arousal. This argument has no effect on the Seed and SEED IV dataset because these datasets have fixed splits based on categorical labels. You can choose the following options:
    - `V`: Valence
    - `A`: Arousal
* `--num_epochs` Sets the number of epochs for training.
* `--batch_size` Specifies the batch size for training.
* `--lr` Specifies the learning rate for training.
* `--sampling_r` Specifies the sampling rate of the EEG data.
* `--window` Specifies the length of the EEG segments (in seconds).
* `--weight_decay` Specifies the weight decay ratio for regularization.
* `--label_smoothing` Smoothing factor applied to the labels to make them less extreme.
* `--dropout_rate` Probability at which outputs of the layer are dropped out. 
* `--num_classes` Specifies the number of classes of the classification problem. Please leave this argument at a value of 2 because we conceptualized the task as a binary classification problem.
* `--channels` Specifies the number of channels for the dataset. Set this argument to 14 for AMIGOS and DREAMER, 32 for MAHNOB and DEAP, and to 62 for SEED and SEED IV.
* `--split_type` Specifies the type of train-test splitting. Please use only the following two splitting types as any other splits will disqualify your submission:
    - `LOTO`: Leave one trial out. Use this split for the person-dependent task.
    - `LOSO_Fixed`: Creates a fixed 75/25 train-test split that is mandatory for the person-independent task (see below).

## Baseline 

**Person-dependent**

The following table shows baselines for the person-dependent task. We provide two types of baselines here: Random baselines and results that were obtained with an out-of-the-box Tsception approach. The Tsception approach was trained for 500 epochs with a learning rate of 0.001 and no weight decay.


---
| Dataset |  Measure | Tsception Accuracy (SD) | Random Accuracy (SD) | Tsception Weighted F1 (SD) | Random Weighted F1 (SD) |
|---------|:--------:|:-----------------------:|:--------------------:|:--------------------------:|:-----------------------:|
| MAHNOB  |  valence |       0.62 (0.10)       |      0.53 (0.05)     |         0.62 (0.10)        |       0.53 (0.05)       |
| MAHNOB  |  arousal |       0.61 (0.13)       |      0.57 (0.13)     |         0.6 (0.13)         |       0.57 (0.13)       |
| DREAMER |  valence |       0.72 (0.14)       |      0.71 (0.10)     |         0.72 (0.14)        |       0.71 (0.10)       |
| DREAMER |  arousal |       0.79 (0.16)       |      0.78 (0.15)     |         0.79 (0.16)        |       0.78 (0.15)       |
| SEED    | discrete |       0.42 (0.12)       |      0.28 (0.02)     |         0.41 (0.12)        |       0.28 (0.02)       |
| SEED-IV | discrete |       0.34 (0.08)       |      0.20 (0.02)     |         0.33 (0.10)        |       0.20 (0.02)       |
---

**Person-independent**

The following table shows baselines for the person-independent task. We provide two types of baselines here: Random baselines and results that were obtained with an out-of-the-box Tsception approach. The Tsception approach was trained for 500 epochs with a learning rate of 0.001 and no weight decay. As can be seen, the out-of-the box Tsception approach only performs substantially above the random baseline for the DREAMER dataset.

---
| Dataset |  Measure | Tsception Accuracy | Random Accuracy | Tsception Weighted F1 | Random Weighted F1 |
|---------|:--------:|:------------------:|:---------------:|:---------------------:|:------------------:|
| MAHNOB  |  valence |        0.52        |       0.50      |          0.52         |        0.50        |
| MAHNOB  |  arousal |        0.54        |       0.49      |          0.54         |        0.50        |
| DREAMER |  valence |        0.76        |       0.72      |          0.73         |        0.72        |
| DREAMER |  arousal |        0.84        |       0.77      |          0.79         |        0.77        |
| SEED    | discrete |        0.47        |       0.32      |          0.47         |        0.32        |
| SEED-IV | discrete |        0.37        |       0.24      |          0.35         |        0.24        |
---

The following prompts were used to obtain the baselines reported above

```
python3 run_cli.py \
--model_name=TSception \
--data_name=SeedIV \
--data_path='path_to/eeg_raw_data' \
--log_dir="logsso1/" \
--overal_log_file="loso_seedIV.txt" \
--label_type="A" \
--num_epochs=500 \
--batch_size=64 \
--lr=0.001 \
--sampling_r=128 \
--window=2 \
--weight_decay=0 \
--label_smoothing=0.01 \
--dropout_rate=0.5 \
--num_classes=4 \
--channels=62 \
--split_type="LOSO_Fixed"

python3 run_cli.py \
--model_name=TSception \
--data_name=Seed \
--data_path='path_to/Preprocessed_EEG' \
--log_dir="logss1/" \
--overal_log_file="loso_seed.txt" \
--label_type="A" \
--num_epochs=500 \
--batch_size=64 \
--lr=0.001 \
--sampling_r=128 \
--window=2 \
--weight_decay=0 \
--label_smoothing=0.01 \
--dropout_rate=0.5 \
--num_classes=3 \
--channels=62 \
--split_type="LOSO_Fixed"

python run_cli.py \
--model_name=TSception \
--data_name=MAHNOB \
--data_path='path_to/Sessions' \
--log_dir="logs/" \
--overal_log_file="loso_mahnob_arousal.txt" \
--label_type="A" \
--num_epochs=500 \
--batch_size=32 \
--lr=0.0001 \
--sampling_r=128 \
--window=2 \
--weight_decay=0.01 \
--label_smoothing=0.01 \
--dropout_rate=0.5 \
--num_classes=2 \
--channels=32 \
--split_type="LOSO_Fixed"

python run_cli.py \
--model_name=TSception \
--data_name=MAHNOB \
--data_path='path_to/Sessions' \
--log_dir="logs/" \
--overal_log_file="loso_mahnob_valence.txt" \
--label_type="V" \
--num_epochs=500 \
--batch_size=32 \
--lr=0.0001 \
--sampling_r=128 \
--window=2 \
--weight_decay=0.01 \
--label_smoothing=0.01 \
--dropout_rate=0.5 \
--num_classes=2 \
--channels=32 \
--split_type="LOSO_Fixed"

python run_cli.py \
--model_name=TSception \
--data_name=DREAMER \
--data_path='path_to/DREAMER.mat' \
--log_dir="logs/" \
--overal_log_file="loso_dreamer_arousal.txt" \
--label_type="A" \
--num_epochs=500 \
--batch_size=32 \
--lr=0.001 \
--sampling_r=128 \
--window=4 \
--weight_decay=0 \
--label_smoothing=0.01 \
--dropout_rate=0.5 \
--num_classes=2 \
--channels=14 \
--split_type="LOSO_Fixed"

python run_cli.py \
--model_name=TSception \
--data_name=DREAMER \
--data_path='path_to/DREAMER.mat' \
--log_dir="logs/" \
--overal_log_file="loso_dreamer_valence.txt" \
--label_type="V" \
--num_epochs=500 \
--batch_size=32 \
--lr=0.001 \
--sampling_r=128 \
--window=4 \
--weight_decay=0 \
--label_smoothing=0.01 \
--dropout_rate=0.5 \
--num_classes=2 \
--channels=14 \
--split_type="LOSO_Fixed"

python3 run_cli.py \
--model_name=TSception \
--data_name=SeedIV \
--data_path='path_to/eeg_raw_data' \
--log_dir="logsso1/" \
--overal_log_file="loto_seedIV.txt" \
--label_type="A" \
--num_epochs=500 \
--batch_size=64 \
--lr=0.001 \
--sampling_r=128 \
--window=2 \
--weight_decay=0 \
--label_smoothing=0.01 \
--dropout_rate=0.5 \
--num_classes=4 \
--channels=62 \
--split_type="LOTO"

python3 run_cli.py \
--model_name=TSception \
--data_name=Seed \
--data_path='path_to/Preprocessed_EEG' \
--log_dir="logss1/" \
--overal_log_file="loto_seed.txt" \
--label_type="A" \
--num_epochs=500 \
--batch_size=64 \
--lr=0.001 \
--sampling_r=128 \
--window=2 \
--weight_decay=0 \
--label_smoothing=0.01 \
--dropout_rate=0.5 \
--num_classes=3 \
--channels=62 \
--split_type="LOTO"

python run_cli.py \
--model_name=TSception \
--data_name=MAHNOB \
--data_path='path_to/Sessions' \
--log_dir="logs/" \
--overal_log_file="loto_mahnob_arousal.txt" \
--label_type="A" \
--num_epochs=500 \
--batch_size=32 \
--lr=0.0001 \
--sampling_r=128 \
--window=2 \
--weight_decay=0.01 \
--label_smoothing=0.01 \
--dropout_rate=0.5 \
--num_classes=2 \
--channels=32 \
--split_type="LOTO"

python run_cli.py \
--model_name=TSception \
--data_name=MAHNOB \
--data_path='path_to/Sessions' \
--log_dir="logs/" \
--overal_log_file="loto_mahnob_valence.txt" \
--label_type="V" \
--num_epochs=500 \
--batch_size=32 \
--lr=0.0001 \
--sampling_r=128 \
--window=2 \
--weight_decay=0.01 \
--label_smoothing=0.01 \
--dropout_rate=0.5 \
--num_classes=2 \
--channels=32 \
--split_type="LOTO"

python run_cli.py \
--model_name=TSception \
--data_name=DREAMER \
--data_path='path_to/DREAMER.mat' \
--log_dir="logs/" \
--overal_log_file="loto_dreamer_arousal.txt" \
--label_type="A" \
--num_epochs=500 \
--batch_size=32 \
--lr=0.001 \
--sampling_r=128 \
--window=4 \
--weight_decay=0 \
--label_smoothing=0.01 \
--dropout_rate=0.5 \
--num_classes=2 \
--channels=14 \
--split_type="LOTO"

python run_cli.py \
--model_name=TSception \
--data_name=DREAMER \
--data_path='path_to/DREAMER.mat' \
--log_dir="logs/" \
--overal_log_file="loto_dreamer_valence.txt" \
--label_type="V" \
--num_epochs=500 \
--batch_size=32 \
--lr=0.001 \
--sampling_r=128 \
--window=4 \
--weight_decay=0 \
--label_smoothing=0.01 \
--dropout_rate=0.5 \
--num_classes=2 \
--channels=14 \
--split_type="LOTO"

python3 run_cli.py \
--model_name=RANDOM \
--data_name=SeedIV \
--data_path='path_to/eeg_raw_data' \
--log_dir="logsso1/" \
--overal_log_file="loso_seedIV_random.txt" \
--label_type="A" \
--num_epochs=1 \
--batch_size=64 \
--lr=0.001 \
--sampling_r=128 \
--window=2 \
--weight_decay=0 \
--label_smoothing=0.01 \
--dropout_rate=0.5 \
--num_classes=4 \
--channels=62 \
--split_type="LOSO_Fixed"

python3 run_cli.py \
--model_name=RANDOM \
--data_name=Seed \
--data_path='path_to/Preprocessed_EEG' \
--log_dir="logss1/" \
--overal_log_file="loso_seed_random.txt" \
--label_type="A" \
--num_epochs=1 \
--batch_size=64 \
--lr=0.001 \
--sampling_r=128 \
--window=2 \
--weight_decay=0 \
--label_smoothing=0.01 \
--dropout_rate=0.5 \
--num_classes=3 \
--channels=62 \
--split_type="LOSO_Fixed"

python run_cli.py \
--model_name=RANDOM \
--data_name=MAHNOB \
--data_path='path_to/Sessions' \
--log_dir="logs/" \
--overal_log_file="loso_mahnob_arousal_random.txt" \
--label_type="A" \
--num_epochs=1 \
--batch_size=32 \
--lr=0.0001 \
--sampling_r=128 \
--window=2 \
--weight_decay=0.01 \
--label_smoothing=0.01 \
--dropout_rate=0.5 \
--num_classes=2 \
--channels=32 \
--split_type="LOSO_Fixed"

python run_cli.py \
--model_name=RANDOM \
--data_name=MAHNOB \
--data_path='path_to/Sessions' \
--log_dir="logs/" \
--overal_log_file="loso_mahnob_valence_random.txt" \
--label_type="V" \
--num_epochs=1 \
--batch_size=32 \
--lr=0.0001 \
--sampling_r=128 \
--window=2 \
--weight_decay=0.01 \
--label_smoothing=0.01 \
--dropout_rate=0.5 \
--num_classes=2 \
--channels=32 \
--split_type="LOSO_Fixed"

python run_cli.py \
--model_name=RANDOM \
--data_name=DREAMER \
--data_path='path_to/DREAMER.mat' \
--log_dir="logs/" \
--overal_log_file="loso_dreamer_arousal_random.txt" \
--label_type="A" \
--num_epochs=1 \
--batch_size=32 \
--lr=0.001 \
--sampling_r=128 \
--window=4 \
--weight_decay=0 \
--label_smoothing=0.01 \
--dropout_rate=0.5 \
--num_classes=2 \
--channels=14 \
--split_type="LOSO_Fixed"

python run_cli.py \
--model_name=RANDOM \
--data_name=DREAMER \
--data_path='path_to/DREAMER.mat' \
--log_dir="logs/" \
--overal_log_file="loso_dreamer_valence_random.txt" \
--label_type="V" \
--num_epochs=1 \
--batch_size=32 \
--lr=0.001 \
--sampling_r=128 \
--window=4 \
--weight_decay=0 \
--label_smoothing=0.01 \
--dropout_rate=0.5 \
--num_classes=2 \
--channels=14 \
--split_type="LOSO_Fixed"

python3 run_cli.py \
--model_name=RANDOM \
--data_name=SeedIV \
--data_path='path_to/eeg_raw_data' \
--log_dir="logsso1/" \
--overal_log_file="loto_seedIV_random.txt" \
--label_type="A" \
--num_epochs=1 \
--batch_size=64 \
--lr=0.001 \
--sampling_r=128 \
--window=2 \
--weight_decay=0 \
--label_smoothing=0.01 \
--dropout_rate=0.5 \
--num_classes=4 \
--channels=62 \
--split_type="LOTO"

python3 run_cli.py \
--model_name=RANDOM \
--data_name=Seed \
--data_path='path_to/Preprocessed_EEG' \
--log_dir="logss1/" \
--overal_log_file="loto_seed_random.txt" \
--label_type="A" \
--num_epochs=1 \
--batch_size=64 \
--lr=0.001 \
--sampling_r=128 \
--window=2 \
--weight_decay=0 \
--label_smoothing=0.01 \
--dropout_rate=0.5 \
--num_classes=3 \
--channels=62 \
--split_type="LOTO"

python run_cli.py \
--model_name=RANDOM \
--data_name=MAHNOB \
--data_path='path_to/Sessions' \
--log_dir="logs/" \
--overal_log_file="loto_mahnob_arousal_random.txt" \
--label_type="A" \
--num_epochs=1 \
--batch_size=32 \
--lr=0.0001 \
--sampling_r=128 \
--window=2 \
--weight_decay=0.01 \
--label_smoothing=0.01 \
--dropout_rate=0.5 \
--num_classes=2 \
--channels=32 \
--split_type="LOTO"

python run_cli.py \
--model_name=RANDOM \
--data_name=MAHNOB \
--data_path='path_to/Sessions' \
--log_dir="logs/" \
--overal_log_file="loto_mahnob_valence_random.txt" \
--label_type="V" \
--num_epochs=1 \
--batch_size=32 \
--lr=0.0001 \
--sampling_r=128 \
--window=2 \
--weight_decay=0.01 \
--label_smoothing=0.01 \
--dropout_rate=0.5 \
--num_classes=2 \
--channels=32 \
--split_type="LOTO"

python run_cli.py \
--model_name=RANDOM \
--data_name=DREAMER \
--data_path='path_to/DREAMER.mat' \
--log_dir="logs/" \
--overal_log_file="loto_dreamer_arousal_random.txt" \
--label_type="A" \
--num_epochs=1 \
--batch_size=32 \
--lr=0.001 \
--sampling_r=128 \
--window=4 \
--weight_decay=0 \
--label_smoothing=0.01 \
--dropout_rate=0.5 \
--num_classes=2 \
--channels=14 \
--split_type="LOTO"

python run_cli.py \
--model_name=RANDOM \
--data_name=DREAMER \
--data_path='path_to/DREAMER.mat' \
--log_dir="logs/" \
--overal_log_file="loto_dreamer_valence_random.txt" \
--label_type="V" \
--num_epochs=1 \
--batch_size=32 \
--lr=0.001 \
--sampling_r=128 \
--window=4 \
--weight_decay=0 \
--label_smoothing=0.01 \
--dropout_rate=0.5 \
--num_classes=2 \
--channels=14 \
--split_type="LOTO"
```
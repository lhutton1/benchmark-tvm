# TVM Tools
Provides a series of tools and experiments to quickly test tuning algorithms in AutoTVM.

## Driver
A command line driver for the set of tools. See `../run/` for convenient scripts that make use of the driver for particular setups.

Examples:
```bash
rl-tuner/tools/driver.py -m=tune -c=../config-example.json
rl-tuner/tools/driver.py -m=benchmark -c=../config-example.json
rl-tuner/tools/driver.py -m=experiment -c=../config-example.json
```

Config parameters:
| Parameter Name                                    | Type     | Details                                                                                                 |
|---------------------------------------------------|----------|---------------------------------------------------------------------------------------------------------|
| models                                            | JSON     | A list of names of the models to use.                                                                   |
| target                                            | String   | The target string specifies how a program should be compiled.                                           |

## Tune
The tuning pipeline is a simple wrapper around AutoTVM. Given a model and a variety of parameters, it extracts tuning tasks, creates the specified tuner and begins the 
tuning process. The output is a JSON file containing a history of the tuning process.

Config parameters:
| Parameter Name                                    | Type     | Details                                                                                                 |
|---------------------------------------------------|----------|---------------------------------------------------------------------------------------------------------|
| early_stopping                                    | Integer  | If the best configuration has not been updated after the specified number of trials, stop tuning early. |
| repeat                                            | Integer  | The number of times to repeat a hardware measurement before taking an average.                          |
| trials                                            | Integer  | The maximum number of trials measurements to run on hardware.                                           |
| tuner                                             | String   | The tuner type to use for tuning. e.g. xgb, random, ga.                                                 |
| tuning_records                                    | Filepath | If records already exist, tuning history can be applied using transfer learning.                        |

Parameters should be added under `autotuner_settings` object. See `config-example.json` for more information.

## Compilation and benchmarking
The compilation process uses the output of the tuning pipeline to produce an optimised program and run it.

Config parameters:
| Parameter Name                                    | Type     | Details                                                                                                 |
|---------------------------------------------------|----------|---------------------------------------------------------------------------------------------------------|
| profile                                           | Boolean  | Output a layer-by-layer breakdown of model execution time.                                              |
| device                                            | String   | Specify the device to run compiled programs on. e.g. gpu, cpu.                                          |
| fill_mode                                         | String   | The input data that is generated.                                                                       |
| repeat                                            | Integer  | The number of times to repeat a hardware measurement before taking an average.                          |

Parameters should be added under `run_settings` object. See `config-example.json` for more information.

## Experiment
The experiment method provides pre-made experiments used during the creation of a tuner that uses reinforcement learning. Such experiments are detailed below.

Config parameters:
| Parameter Name                                    | Type          | Details                                                                                  |
|---------------------------------------------------|---------------|------------------------------------------------------------------------------------------|
| save_name                                         | String        | The file name to save experiments under. Each experiment may add additional information. |
| save_path                                         | Filepath      | A filepath to save the output of the experiments to.                                     |
| names                                             | List[String]  | A list of the name of experiments to run. The names are detailed below.                  |

Parameters should be added under `experiment` object. See `config-example.json` for more information.

Experiment details:
| Experiment Name          | Description                                                                                |
|--------------------------|--------------------------------------------------------------------------------------------|
| `trial_hyperparameters`  | Each RL tuner implementation has a number of hyperparameters to which the optimal settings are not necessarily known. This experiment uses a simple grid search to help find the best hyperparameters. The results are logged to file. |
| `trial_ga`               | Successively runs 10 tuning tasks on the same convolution using the GATuner for bassline results. The results are logged to file. |
| `trial_gadqn`            | Successively runs 10 tuning tasks on the same convolution using the GADQNTuner for bassline results. The results are logged to file. |
| `compare_gadqn_ga`       | Compare the results of tuning runs of both GATuner and GADQNTuner, average these and produce graph.
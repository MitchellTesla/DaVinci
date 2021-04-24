OpenAI Baselines is a set of high-quality implementations of reinforcement learning algorithms.

These algorithms will make it easier for the research community to replicate, refine, and identify new ideas, and will create good baselines to build research on top of. Our DQN implementation and its variants are roughly on par with the scores in published papers. We expect they will be used as a base around which new ideas can be added, and as a tool for comparing a new approach against existing ones.

Prerequisites
Baselines requires python3 (>=3.5) with the development headers. You'll also need system packages CMake, OpenMPI and zlib. Those can be installed as follows

Ubuntu
sudo apt-get update && sudo apt-get install cmake libopenmpi-dev python3-dev zlib1g-dev
Mac OS X
Installation of system packages on Mac requires Homebrew. With Homebrew installed, run the following:

brew install cmake openmpi
Virtual environment
From the general python package sanity perspective, it is a good idea to use virtual environments (virtualenvs) to make sure packages from different projects do not interfere with each other. You can install virtualenv (which is itself a pip package) via

pip install virtualenv
Virtualenvs are essentially folders that have copies of python executable and all python packages. To create a virtualenv called venv with python3, one runs

virtualenv /path/to/venv --python=python3
To activate a virtualenv:

. /path/to/venv/bin/activate
More thorough tutorial on virtualenvs and options can be found here

Tensorflow versions
The master branch supports Tensorflow from version 1.4 to 1.14. For Tensorflow 2.0 support, please use tf2 branch.

Installation
Clone the repo and cd into it:

git clone https://github.com/openai/baselines.git
cd baselines
If you don't have TensorFlow installed already, install your favourite flavor of TensorFlow. In most cases, you may use

pip install tensorflow-gpu==1.14 # if you have a CUDA-compatible gpu and proper drivers
or

pip install tensorflow==1.14
to install Tensorflow 1.14, which is the latest version of Tensorflow supported by the master branch. Refer to TensorFlow installation guide for more details.

Install baselines package

pip install -e .
MuJoCo
Some of the baselines examples use MuJoCo (multi-joint dynamics in contact) physics simulator, which is proprietary and requires binaries and a license (temporary 30-day license can be obtained from www.mujoco.org). Instructions on setting up MuJoCo can be found here

Testing the installation
All unit tests in baselines can be run using pytest runner:

pip install pytest
pytest
Training models
Most of the algorithms in baselines repo are used as follows:

python -m baselines.run --alg=<name of the algorithm> --env=<environment_id> [additional arguments]
Example 1. PPO with MuJoCo Humanoid
For instance, to train a fully-connected network controlling MuJoCo humanoid using PPO2 for 20M timesteps

python -m baselines.run --alg=ppo2 --env=Humanoid-v2 --network=mlp --num_timesteps=2e7
Note that for mujoco environments fully-connected network is default, so we can omit --network=mlp The hyperparameters for both network and the learning algorithm can be controlled via the command line, for instance:

python -m baselines.run --alg=ppo2 --env=Humanoid-v2 --network=mlp --num_timesteps=2e7 --ent_coef=0.1 --num_hidden=32 --num_layers=3 --value_network=copy
will set entropy coefficient to 0.1, and construct fully connected network with 3 layers with 32 hidden units in each, and create a separate network for value function estimation (so that its parameters are not shared with the policy network, but the structure is the same)

See docstrings in common/models.py for description of network parameters for each type of model, and docstring for baselines/ppo2/ppo2.py/learn() for the description of the ppo2 hyperparameters.

Example 2. DQN on Atari
DQN with Atari is at this point a classics of benchmarks. To run the baselines implementation of DQN on Atari Pong:

python -m baselines.run --alg=deepq --env=PongNoFrameskip-v4 --num_timesteps=1e6
Saving, loading and visualizing models
Saving and loading the model
The algorithms serialization API is not properly unified yet; however, there is a simple method to save / restore trained models. --save_path and --load_path command-line option loads the tensorflow state from a given path before training, and saves it after the training, respectively. Let's imagine you'd like to train ppo2 on Atari Pong, save the model and then later visualize what has it learnt.

python -m baselines.run --alg=ppo2 --env=PongNoFrameskip-v4 --num_timesteps=2e7 --save_path=~/models/pong_20M_ppo2
This should get to the mean reward per episode about 20. To load and visualize the model, we'll do the following - load the model, train it for 0 steps, and then visualize:

python -m baselines.run --alg=ppo2 --env=PongNoFrameskip-v4 --num_timesteps=0 --load_path=~/models/pong_20M_ppo2 --play
NOTE: Mujoco environments require normalization to work properly, so we wrap them with VecNormalize wrapper. Currently, to ensure the models are saved with normalization (so that trained models can be restored and run without further training) the normalization coefficients are saved as tensorflow variables. This can decrease the performance somewhat, so if you require high-throughput steps with Mujoco and do not need saving/restoring the models, it may make sense to use numpy normalization instead. To do that, set 'use_tf=False` in baselines/run.py.

Logging and vizualizing learning curves and other training metrics
By default, all summary data, including progress, standard output, is saved to a unique directory in a temp folder, specified by a call to Python's tempfile.gettempdir(). The directory can be changed with the --log_path command-line option.

python -m baselines.run --alg=ppo2 --env=PongNoFrameskip-v4 --num_timesteps=2e7 --save_path=~/models/pong_20M_ppo2 --log_path=~/logs/Pong/
NOTE: Please be aware that the logger will overwrite files of the same name in an existing directory, thus it's recommended that folder names be given a unique timestamp to prevent overwritten logs.

Another way the temp directory can be changed is through the use of the $OPENAI_LOGDIR environment variable.

For examples on how to load and display the training data, see here.

Subpackages
A2C
ACER
ACKTR
DDPG
DQN
GAIL
HER
PPO1 (obsolete version, left here temporarily)
PPO2
TRPO
Benchmarks
Results of benchmarks on Mujoco (1M timesteps) and Atari (10M timesteps) are available here for Mujoco and here for Atari respectively. Note that these results may be not on the latest version of the code, particular commit hash with which results were obtained is specified on the benchmarks page.

To cite this repository in publications:

@misc{baselines,
  author = {Dhariwal, Prafulla and Hesse, Christopher and Klimov, Oleg and Nichol, Alex and Plappert, Matthias and Radford, Alec and Schulman, John and Sidor, Szymon and Wu, Yuhuai and Zhokhov, Peter},
  title = {OpenAI Baselines},
  year = {2017},
  publisher = {GitHub},
  journal = {GitHub repository},
  howpublished = {\url{https://github.com/openai/baselines}},
}

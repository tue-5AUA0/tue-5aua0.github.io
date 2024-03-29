# High Performance Computing (HPC) Tutorial
Training deep neural networks (DNNs) requires high computational resources. In order to train DNNs in a reasonable amount of time and without having direct acces to a powerfull GPU we will make use of a High Perfomance Computing cluster. More specifically, you will get virtual acces to [Snellius](https://servicedesk.surf.nl/wiki/display/WIKI/Snellius), the Dutch National supercomputer. This tutorial will help you understand how the work process on such a supercomputer differs from working on your local machine and how to efficiently make use of this HPC platform for the purposes of training DNNs.

# General information HPC
Different from your own laptop, a supercomputer cluster is a shared resource. That means that you will not have acces to its entire compute resource at will. If you want to train a model for an extended time you will have to submit a batch job, this can be seen as making a reservation for compute resources such as powerfull CPUs and GPUs. Reserving compute resources has a price, [SBUs](https://servicedesk.surf.nl/wiki/display/WIKI/Snellius+usage+and+accounting) are used as a currency and you will get your own limited budget. Therefore you have to make sure you will use your available compute wisely. During many parts of the project however you will not need acces to powerfull compute resources. We will elaborate on this in the Workflow section below.

# Workflow
This section shows you the workflow with a HPC. There will be three phases:
1. **Development** of code locally and on an interactive *login node*
2. **Trial** run code on an interactive *compute node*
3. **Running** a *batch job*

## Development

### Anaconda on local
An important aspect of being a developer/researcher is enabling others to run your code and reproduce your results. Anaconda helps with package or library installation and management. Using Anaconda you can specify an environment with the requirement packages for your code. To setup Anaconda on your local machine do the following:

* Download and install [Anaconda](https://docs.anaconda.com/anaconda/install/)
* Open an Anaconda Prompt via your Windows search bar
* Here you can create a new environment, specify a name and a Python version, e.g. `conda create --name 5aua0 python=3.10`
* Activate the environment with `conda activate 5aua0`
* For first time activation, initialize the environment with `conda init`

Now you can install packages that might be required for your project, such as torch, in your conda environment. Note that if you have a TU/e laptop, you have acces to its built-in Nvidia GPU. It is not powerful enough to train large DNN, but can be used to test run during development. Therefore you should in this case install a GPU version of your desired DNN library. You might already be able to development large parts of your project without acces to Snellius, especially when you have a TU/e laptop or another machine with a GPU. 

### Setup Remote Connection
At some point in your project you will have to use Snellius. Here we will explain how to connect to it. For development you will make use of the Integrated Development Environment (IDE) Visual Studio Code (VSCode). In order to setup VSCode for remote development on the cluster, you will first have to install the "Remote - SSH" extension which can be found in the extensions tab (Ctrl+Shift+X). After installation you can "Open a Remote Window" by clicking at the green arrow symbol at the lower left of the application. Now press "Connect to Host...". You will be prompted to enter your username and hostname i.e. `<username>@snellius.surf.nl`. You will be asked to enter your Snellius password. After succesful connection you will see "SSH: snellius" at the bottom left of the application. Once connected you will have acces to the files in your Snellius home directory and you can open a new terminal (Ctrl+Shift+`). You now have acces to the cluster via a remote SSH connection in your local VSCode.

Upon connection with Snellius you will have acces to an interactive *login node*. A login node can be used for development of code, but is primarily meant for preparing and submitting jobs (not for testing compute jobs). Do not try to run extended scripts on a login node, your runs might get aborted at random times, or when the systems sees you are using too much resources at this type of node. 

### Clone Git Repository
It is advised to now try to clone your GitHub repository used for the course, for this you will have to setup a SSH key for the HPC first, see the Git tutorial for more information if needed. 

### Moving files from and to Snellius
The easiest way to move files from your local to the remote is to drag and drop them into the *explorer* section in the left hand side of your VSCode window. This unfortunately does not work for moving files from the remote to your local machine. For this you can use the `scp` command. The following command will copy a file from the remote to your current directory. 

```bash
scp -r <username>@snellius.surf.nl:<path to file> ~
```

### Anaconda on HPC

When you are ready to use the HPC for development/setting up a job, you might want to re-use the same conda environment as you created before on your local machine. Instead of creating a new environment, you can export an existing environment using 
```
conda env export > environment.yml --from-history
``` 
Now to install this environment on your HPC:

* Make sure you are connected to a Snellius login node (you will see something like `[<username>@int6 ~]$` in your terminal, where *int* stands for CPU interactive node)
* `module load 2022` This will load some core libraries to use. (`module spider avail` will list all modules that can be loaded)
* `module load Anaconda3/2022.05` This will load Anaconda. 
* `conda env create -f environment.yml` For this you will first have to move the *.yml* to the HPC file system.

### Development Workflow
Now you can develop your code from your local machine while being connected to the Snellius supercomputer via VSCode Remote SSH. Everytime you log in to Snellius your environment should be the same as it was when you logged out. Always make sure though to commit and push your code changes often to not lose anything due to unforeseen circumstances. 

<!-- During your development you will often test parts of your code, this can be done mostly done on your *login node*, this node does however not have an attached GPU. If you want to test certain functionalities of your code on a GPU check the next section **Trial**. Learning to use (pdb) debugger and breakpoints can be very helpfull during development, and works very smooth. Remember that you are not supposed to run full training sessions on the *login node* as it is not meant for it and also not very fast. -->

## Trial

When you want to run/test your code on a GPU there are two options. You can submit a batch job or you can request an interactive session. The latter we will explain here. The following code can be ran in the terminal and will allocate (request) one GPU node for 10 minutes and will attach one GPU to that node. 

```
salloc -n 1 -t 00:10:00 -p gpu --gpus-per-node=1
```

After running that command, the system will try to schedule the requested resources. This will usually not take long, especially when not requesting a lot. The following will be printed to the terminal. 

<!-- ![hpc_salloc](assets\img\hpc_salloc.png) -->
```console
[<username>@int5 5aua0-project-template]$ salloc -n 1 -t 00:10:00 -p gpu --gpus-per-node=1
salloc: Single-node jobs run on a shared node by default. Add --exclusive if you want to use a node exclusively.
salloc: A full node consists of 72 CPU cores, 491520 MiB of memory and 4 GPUs and can be shared by up to 4 jobs.
salloc: By default shared jobs get 6826 MiB of memory per CPU core, unless explicitly overridden with --mem-per-cpu, --mem-per-gpu or --mem.
salloc: You will be charged for 0.25 node, based on the number of CPUs, GPUs and the amount memory that you've requested.
salloc: Pending job allocation 2523214
salloc: job 2523214 queued and waiting for resources
salloc: job 2523214 has been allocated resources
salloc: Granted job allocation 2523214
salloc: Waiting for resource configuration
salloc: Nodes gcn35 are ready for job
```

On the last line it says the node *gcn35* is ready for the job (*gcn* stands for graphical compute node). Now we can ssh into that node with `ssh <nodename>`, for example

```console
[<username>@int5 5aua0-project-template-hpc]$ ssh gcn35
[<username>@gcn35 ~]$ 
```

We now have acces to a high tier GPU (NVIDIA A100) to test our code for (a little less than) 10 minutes. Use this time not to run full experiments but to verify that your code runs and a model is training. You can only request up to one hour of compute node time here. Allocating a compute node is not free and thus your budget will decrease from it. If you want to stop using the interactive compute node you can use `scancel <jobid>` to close the connection. You're budget will from that moment not be consumed anymore.

## Running

If you have tested your code using the interactive graphical compute node and it works. You might want to run an extended training session. For this you will have to submit a *batch job*. In this batch job you will specify what resources you want, how long you want them and what needs to be ran exactly. After submitting the job, the system will schedule it. This is often not instanteanous as your job might be ran overnight. 

### example job script

The following job script will request a single GPU node with 1 GPU attached for 1 hour. Once the script has started running and once it ends, you will receive an email.

```bash
#!/bin/bash

# Set job requirements
#SBATCH --nodes=1
#SBATCH --gpus-per-node=1
#SBATCH --partition=gpu
#SBATCH --time=1:00:00
#SBATCH --mail-type=BEGIN,END
#SBATCH --mail-user=a.b.c.smith@student.tue.nl

# activate conda environment
source activate hpc

# go into your project folder
cd $TMPDIR/5aua0-project-template-hpc

# make sure on correct branch
git checkout master

# run your code
python train.py
```

This script can be run using 
```bash
sbatch training_job.sh
```
The outputs of the run i.e. print statements to the terminal will be saved to a slurm-<jobid>.out file.


### usefull comands

* `accinfo` shows your compute budget and other account information

* `squeue -j <jobid> -o "%.18i %.9P %.8j %.8u %.2t %.10M %.6D %R %S"` shows the *estimated* time at which your job will run

* `squeue -u <username>` gives an overview of your currently active jobs

* `scancel <jobid>` cancels a job such that it will no longer use your computational budget

To analyze the system resources that are being used, you can open a new terminal and ssh into the compute node to run:

* `top` gives an overview of CPU and memmory usage

* `nvidia-smi` gives an overview of the GPU usage


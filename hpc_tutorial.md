# High Performance Computing (HPC) Tutorial
Training deep neural networks (DNNs) requires high computational resources. In order to train DNNs in a reasonable amount of time and without having direct acces to a powerfull GPU we will make use of a High Perfomance Computing cluster. More specifically, you will get virtual acces to [Snellius](https://servicedesk.surf.nl/wiki/display/WIKI/Snellius), the Dutch National supercomputer. This tutorial will help you understand how the work process on such a supercomputer differs from working on your local machine and how to efficiently make use of this HPC platform for the purposes of training DNNs.

# General information HPC
Different from your own laptop, a supercomputer cluster is a shared resource. That means that you will not have acces to the entire compute resource the cluster has at will. If you want to train a model for an extended time you will have to submit a batch job, this can be seen as making a reservation for compute resources such as powerfull CPUs and GPUs. Reserving compute resources has a price, [SBUs](https://servicedesk.surf.nl/wiki/display/WIKI/Snellius+usage+and+accounting) are used as a currency and you will get your own limited budget. Therefore you have to make sure you will use your available compute wisely. During many parts of the project however you will not need acces to powerfull compute resources. We will elaborate on this in the Workflow section below.

# Workflow
This section shows you the workflow with a HPC. There will be three phases:
1. **Development** of code on an interactive *login node*
2. **Trial** run code on an interactive *compute node*
3. **Running** a *batch job*

## Development

### Setup Remote Connection
For development you will make use of the Integrated Development Environment (IDE) Visual Studio Code (VSCode). In order to setup VSCode for remote development on the cluster, you will first have to install the "Remote - SSH" extension which can be found in the extensions tab (Ctrl+Shift+X). After installation you can "Open a Remote Window" by clicking at the green arrow symbol at the lower left of the application. Now press "Connect to Host...". You will be prompted to enter your username and hostname i.e. `<username>@snellius.surf.nl`. You will be asked to enter your Snellius password. After succesful connection you will see "SSH: snellius" at the bottom left of the application. Once connected you will have acces to the files in your home directory and you can open a new terminal (Ctrl+Shift+`). You now have acces to the cluster via a remote SSH connection in your local VSCode.

Upon connection with Snellius you will have acces to an interactive *login node*. A login node can be used for development of code, but is primarily meant for preparing and submitting jobs (not for testing compute jobs). Do not try to run extended scripts on a login node, your runs might get aborted at random times, or when the systems sees you are using too much resources at this type of node. 

### Clone Git Repository
It is advised to now try to clone the [example project], for this you will have to setup a SSH key for the HPC first, see the Git tutorial for more information if needed. 

### Anaconda
An important aspect of being a developer/researcher is enabling others to run your code and reproduce your results. Anaconda helps with version management of packages/libraries you might use during your development. Using Anaconda you can specify an environment with the requirement packages for your code. To setup Anaconda on you HPC account do the following:
* Make sure you are connected to Snellius (you will see something like `[<username>@int6 ~]$` in your terminal, where *int* stands for CPU interactive node)
* `module load 2022` This will load some core libraries to use. (`module list avail` will list all modules that can be loaded)
* `module load Anaconda3/2022.05` This will load Anaconda. Now an environment can be created using:
* `conda create -n <env_name> python=3.8`
* `conda activate <env_name>` This is to activate the environment
* `conda init` This only has to be ran once, upon environment creation

Instead of creating an environment from scratch you can also load an existing one that you exported with

`conda env export > environment.yml`

and then recreate with

`conda env create -f environment.yml`

### Moving files from and to Snellius
The easiest ways to move files from your local to the remote is to drag and drop them into the *explorer* section in the left hand side of your VSCode window. This unfortunately does not work for moving files from the remote to your local machine. For this you can use the `scp` command. 

`scp -r <username>@snellius.surf.nl:/home/<username>/documents/5aua0-project-template-hpc/test.txt ~`

### Development Workflow
Now you can develop your code from your local machine while being connected to the Snellius supercomputer via VSCode Remote SSH. Everytime you log in to Snellius your environment should be the same as it was when you logged out. Always make sure though to commit and push your code changes often to not lose anything due to unforeseen circumstances. 

During your development you will often test parts of your code, this can be done mostly done on your *login node*, this node does however not have an attached GPU. If you want to test certain functionalities of your code on a GPU check the next section **Trial**. Learning to use (pdb) debugger and breakpoints can be very helpfull during development, and works very smooth. Remember that you are not supposed to run full training sessions on the *login node* as it is not meant for it and also not very fast.


## Trial

When you want to run your code on a GPU there are two options. You can submit a batch job or you can request an interactive session. The latter we will explain here. The following code can be ran in the terminal and will allocate (request) one GPU node for 10 minutes and will attach one GPU to that node. 

`salloc -n 1 -t 00:10:00 -p gpu --gpus-per-node=1`
After running that command, the system will try to schedule the requested resources. This will usually not take long, especially when not requesting a lot. The following will be printed to the terminal. 

![salloc](salloc.png)

On the last line it says the node *gcn41* is ready for the job (*gcn* stands for graphical compute node). Now we can ssh into that node with

`ssh gcn41`

We now have acces to a high tier GPU (NVIDIA A100) to test our code for (a little less than) 10 minutes. Use this time not to run full experiments but to verify that your code runs and a model is training. You can only request up to one hour of compute node time here. Allocating a compute node is not free and thus your budget will decrease from it.

## Running

If you have tested your code using the interactive graphical compute node and it works. You might want to run an extended training session. For this you will have to submit a *batch job*. In this batch job you will specify what resources you want and what needs to be ran exactly. After submitting the job, the system will schedule you job. This is often not instanteanous as your job might be ran overnight.

### creating a job script
create job script `training_job.sh`

run script `sbatch training_job.sh`

# dry-lab-standard

For datasets and tutorials:
Please visit our  [Wiki](https://github.com/RCHENLAB/dry-lab-standard/wiki)


## 1. Get started

As bioinformatician or computational biologist, sometimes we need to share and create a lot of codes. To organize this, we can use Github. To create an account, Go to https://github.com/join in a web browser. You can use any web browser on your computer, phone, or tablet to join. Now you can add your personal details. In addition to creating your username and entering an email address, you'll also have to create a password. Your password must be at least 15 characters in length or at least 8 characters with at least one number and lowercase letter. Now click in the create account button. Click the Verify email address button in the message from GitHub. This confirms your email address and returns you to the sign-up process. Well done! 



## 2. Prerequisites about basic tools and Linux operations

We will need several tools to start the single cell analysis such as R, Seurat, and Python. 
To know how to start to use slurm, please visit Slurm 4 beginners.
Conda is a very powerful software management system, and we are going to install tools via Conda. 

2.1 Access to the Taco server 
First of all, you need to set the taco server account
Go to the adress https://it.bcm.edu/  and to create a ticket to request access to taco server, go to **Report a Problem** and make the request described in Short description and describe your issue 


For example, 

```
Could you please help create an account for our student xxx on the Taco server? Please find the info below. Thank you so much.

Full name: 
BCM ECA: 
Email: bcm_eca@bcm.edu
xxx will be in Dr. Chen's group.

Best regards, xxx

NOTE: BCM ECA is the Educational credential assessment  (ECA) number
STARTS WITH u + 6 digit number

Example:
Paul John
u228876
```
## 3. Working Remotely (setting up your VPN)
To work remotely, the Taco server is accessible with the BCM network only, so we will need to connect BCM VPN first before logging in to the Taco server. In order to use BCM VPN, we need to set up MFA using our BCM ECA account. Official IT documents can be found at below (Need BCM account to view the page). 
https://bcm.service-now.com/bcmsp?id=kb_article&sys_id=35d6ea9ddb83cfc0729c73d78c9619d6 
 
Briefly, there are two ways to use MFA, SMS and Google Authenticator mobile app. See below. 
https://bcm.service-now.com/bcmsp?id=kb_article&sys_id=7fb760da1b3f9414d580b997cc4bcbd2 
https://www.bcm.edu/sites/default/files/2019/8a/mfa-install-brochure.pdf 
 
To install MFA via SMS, we need to link our cellphone number to our BCM account at https://password.bcm.edu. After the cellphone number is linked, we can input “SMS” as response when MFA token is required, and a token will be sent to the phone by a SMS message. 
 
Once MFA is set up, we need to set up VPN. IT help page can be found at below. 
https://bcm.service-now.com/bcmsp?id=kb_article&sys_id=e2672c664fe58780e2d104c85210c754 
 
In short, visit https://vpn.bcm.edu (select BCM-default) and follow instructions to download and install Cisco AnyConnect App. 
 
 ## 4. Logging into the Taco server
After connecting BCM VPN, run below command to log in to the Taco server. 

```
ssh BCM_ECA@taco.grid.bcm.edu

```
## For all users when logging into Taco server: 

Replace “BCM_ECA” with your ECA, and it will prompt for password. 

## For windows users: 
It is required that you download putty to gain access to the taco server. 
Please download the following program: <https://www.putty.org/>. 
In putty: 

```

Go To Session - Host Name or IP adress
Write 10.66.4.154
Port: 22
Connection type: SSH
Now click in OPEN
One screeen requesting your loggin and password will be appear if all its well setup

```
 
## 5. Install Conda on the Taco server   
 
## 5.1 Two directories on the Taco server 
 
There are two directories for our use currently. 
 
 ```
 
 /storage/chen/home/BCM_ECA 

NOTE: BCM ECA is the Educational credential assessment  (ECA) number
STARTS WITH u + 6 digit number

Example:
Paul John
u228876
```


The hard drive of the first directory is RAID 6 with a redundant copy of files, so it is suitable for important files, such as installed software, developed scripts and source codes. The second directory is used to store large size files such as analysis results. We are going to install Conda and other open-source tools under the first directory and put the analysis results under the second directory. 
The pathway of the first directory is: /storage/chen/home
The pathway of the second directory is: /storage/chentemp
If there are large files that you are working with, please store them under chentemp. 

## 5.2 Entering a node
**IMPORTANT: NEVER ACTIVATE CONDA (or do anything other than very simple tasks) IN THE HEAD NODE!**

You can check whether you are in the headnode by looking at the command line. 
The command line appears like this: 
[u2400000@mhgcp-**h00** directoryname]
If you are in the headnode, it wil show h00 in the command line. 

**To create a job in a node:**

1. type ``` hls ``` into the command line
2. Check which nodes are available by looking at the available memory as well as available CPUs.
3. To change the node and create an interactive job, into the command line type in:
   ``` sruntaco.sh [NODENAME] ```
   For example, if you wish to enter the r01 node, type in:
   ``` sruntaco.sh r01 ```

Note that you are now inside an interactive job in the `r01` node, of the interactive partition. When you are done with whatever you're doing, you need to kill the interactive job, to release the node's resources so someone else can use them. To do this, just type `exit` -- you should see that you have returned to the head node `h00`.

FYI: When you are done with using taco, you need to quit the connection to the server. To do this, when you're in the head node, run `exit` and you'll be returned to your regular terminal.


## 5.3 To install Conda on the Taco server 
 
To install conda on the Taco server, we need to first download a Conda Linux installer from the below link. 
https://docs.conda.io/en/latest/miniconda.html 
 
We want to install the “Python 3.9” Linux installer. 
 
Due to the system version Python library will be imported automatically, and it will clash with Conda Python, so we need to reset the Python library environment. 

Add this configs into your ~/.bashrc. 
The bashrc is a shell script that runs in Linux whenever started. Whatever you put into the Bashrc will happen everytime you open Linux. 

In order to edit the file with vim, type this into the command line: 

```
vi ~/.bashrc
```

After opening the conf file with vim, copy and paste the following settings into it (either using your normal copy/paste CTRL-C/V, or certain terminals paste when you right-click):

```
# User specific aliases and functions
# export TMPDIR=~/../../tmp
unset PYTHONPATH
unset R_LIBS_SITE
export PATH=$(sed -e "s/:\/opt[^:]\+//g" -e "s/:\/[^:]\+hisat\+//g" <<< "$PATH")

source /storage/chen/data_share_folder/jinli/script_bcm/bashrc
export PATH=/storage/chen/home/u247700/cellranger/cellranger-7.1.0:$PATH

alias hls='sinfo -O nodehost,available,statecompact,cpus,cpusstate,cpusload,memory,freemem | sed "s/\s\+/\t/g" | sort -V | column -t'
alias sls="squeue -u $USER"

complete -r

```
To exit vim, [esc], :wq

After configuring your BASHRC, run the following commands below into the command line to install conda. 

```
wget https://repo.anaconda.com/miniconda/Miniconda3-py39_4.9.2-Linux-x86_64.sh
bash Miniconda3-py39_4.9.2-Linux-x86_64.sh
# hold down enter, then answer yes when prompted
# when asked where to install, type /storage/chen/home/YOUR_USER_ID/tools/miniconda3
# this will create the folder and install miniconda to it
# finally, answer "yes" to run conda init
```
After installation, we need to reopen the terminal session (open a new window, or re-login to the server) so that Conda paths will be recognized.


## 6. Setting up Conda
The last step of the installer should have run conda init (if you still need to run it, type `~/tools/miniconda/bin/conda init`).

Now, we must update .bashrc. Open the .bashrc file using `vim .bashrc`. Then, copy and cut out the following lines 

```
# >>> conda initialize >>>
# !! Contents within this block are managed by 'conda init' !!
__conda_setup="$('/storage/chen/home/BCM_ECA/miniconda3/bin/conda' 'shell.bash' 'hook' 2> /dev/null)"
if [ $? -eq 0 ]; then
    eval "$__conda_setup"
else
    if [ -f "/storage/chen/home/BCM_ECA/miniconda3/etc/profile.d/conda.sh" ]; then
        . "/storage/chen/home/BCM_ECA/miniconda3/etc/profile.d/conda.sh"
    else
        export PATH="/storage/chen/home/BCM_ECA/miniconda3/bin:$PATH"
    fi
fi
unset __conda_setup
# <<< conda initialize <<<
```
(you can do this the usual way with CTRL-C, or by typing `v` for visual editing, `j` repeatedly to select down to where the line ends, then `x` to cut. `p` will paste.)

Next, type `vim ~/.condainit` to create a new file, then paste the lines above in. Save this file (`:wq`).

To activate conda, type into the command line: `source ~/.condainit`. You should see `(base)` appear in front of your terminal user.



## 8. Common conda commands 
 
There are several commonly used Conda commands. Familiarize yourself with them. 

```
conda env list -> shows a list of all environments you have created
conda activate -> activates conda
conda deactivate -> deactivates conda
conda install -> installs something into conda
conda create -n newenv -> creates a new environment
conda search -> searches conda

```

A good cheat sheet has also summarized commonly used commands. See 
https://www.datamachinist.com/cheat-sheets/anaconda-cheat-sheet/ 


We can also export conda environments from one user to another!
Example:

First, copy the environment from Cristal. We can do this by exporting the environment to a .yml file. 
```
#Copy cellqc env from Cristal
conda env export > environment.yml
```
Then, Cristal can email the environment to you in the .yml file, and the user can download it into their linux folder using FileZilla. 
After downloading, the user can create the new environment using the command line. 
```
#Download to the new user, and use this followed command:
conda env create -f environment.yml

```
 
## 9. Good practices to use working with Conda

1) Use Conda environments: One of the main benefits of Conda is its ability to create isolated environments. Always create a new environment for each project you work on. This ensures that the dependencies for each project are kept separate and avoids conflicts between packages.
 
2) Use environment.yml files: You can export the dependencies of your environment to a YAML file using conda env export > environment.yml. This file can then be used to recreate the environment on another machine or share with others.
 
3) Use descriptive names for environments: When creating a new environment, use a descriptive name that relates to the project. This will make it easier to identify which environment you need to activate when working on a specific project.
 
4) Always activate an environment before working in it: When you create a new environment, you must activate it before you can use it. To activate an environment, use the command conda activate environment_name.
 
5) Use conda deactivate to deactivate an environment: When you are finished working in an environment, always deactivate it using conda deactivate. This will return you to the base environment.
 
6) Update Conda regularly: Conda is regularly updated with bug fixes and new features. Always update to the latest version of Conda using conda update conda.
 
7) Use conda clean to free up disk space: Conda caches packages, which can take up a lot of disk space over time. Use conda clean --all to remove unused packages and free up disk space.
 
8) Use conda list to view installed packages: Use the conda list command to view the packages installed in the current environment.
 
9) Use mamba install to install packages: Use the mamba install command to install packages into the current environment. Specify the package name and version if necessary. For more info, please visit mamba documentation.

By following these good practices, you can make your workflow more efficient and avoid common pitfalls when working with Conda.


## 10. Install Mamba with Conda 

In your base environment, add the following channels and install mamba (a faster way to install packages; it solves environment conflicts way faster)


```
conda config --add channels defaults --add channels bioconda --add channels conda-forge 
conda install -y mamba 
```
You'll see a ton of lines pop up, just let em do their thing.


## 11.  Several commonly used Linux commands- familiarize and practice with these
- ls: to list files or directories 
- cd: to change a working directory 
- cp: copy a file or directory 
- mv: move or rename a file or directory (-i is suggested). To move a large directory, it is encouraged to copy (by cp) or sync (by rsync) it to a new place first, then delete the old one. Because a partial move will cause corrupt source and destination directories. 
- rm: remove a file or directory (always be cautious with this remove command because removed files will not be recovered. Think twice before using this command, and -i is suggested) 
- more/less: two text file peek commands 
- man: return documentation for a command, such as `man ls`. 

## 12. Get to know the text editor on Linux - Vim/Emacs
It is important to edit files on Linux efficiently, and Vim or Emacs are typically used. It is important to know basic editing operations include insert, copy, paste, selection, delete, move, search, and replace. Some cheat sheets will help remember common operations. 
Use https://devhints.io/vim to explore more. 


## 13. Installing the newest version of R 
To run correctly cellqc, we need to make sure if the Rbase version is the newest one. 
To install the newest version of R-base, run the following command: 

```
conda install r-base=4.2.2 
```

To know what is the newest version of a conda package, e.g., R-base: 
Go to Anaconda website < https://anaconda.org/> and search for the package in the search bar
https://anaconda.org/search?q=r-base 


## 14. Tips to easily download files from the server to your computer 

FileZilla is an open source software distributed free of charge under the terms of the GNU General Public License. < https://filezilla-project.org/ > 

If you want to download another conda environment, push to GitHub without the command line, access files from Linux on your local computer, use this program. 

After installation, we need to set information about the remote machine, as follow:


**Host:** IP address of the remote machine - for ChenLab, the IP address is 10.66.4.154
**Username:** login – ECA – BCM ID - same as used to connect to the taco server
**Password:** same as used to connect to the taco server 
**Port:** 22 (if 22 does not work, leave blank). 

Now we can simple drag and drop files from our computer to taco server, and vice-versa. We can also click with the right button in the desire file, and select the Download option. 

## 15. Using the Taco Server
This section is to help use the Taco server efficiently. It first describes available computing nodes and their resources. Then, a script to submit jobs to Slurm is described. Also, several commonly used commands are listed to check jobs status. 

The taco server has 14 computing nodes, and below we can see their detailed resource.

These computing nodes are organized in 4 queues (or called partitions in Slurm) where we can submit interactive and batch jobs. 
Familiarize yourself with the roles of each of the nodes, it is easy to submit a job to a node which is not suitable for the job. 
We share the taco server with other labs, be courteous of your usage of the taco server. 

15.1 The short queue. This queue has four computing nodes, i.e., d00, d01, d02, and d03. Each of them has 48 CPUs and 125GB memory. This queue has a wall-time of 12 hours. That is, a job will be terminated automatically after 12-hour limit. This queue is relatively free and very suitable for small jobs that can be finished within 12 hours. 

15.2 The mhgcp queue. This queue also has four computing nodes, i.e., c00, c01, c02, and m00. Computing nodes c00, c01, and c02 have 56 CPUs and 256GB memory. The node m00 is even larger with 112 CPUs and 1TB memory. This queue does not have a time limit for a job, and it is suitable for a large job that requires a large memory and CPUs. Due to many people are using this queue by default, this queue is typically very busy compared to the "short" queue. 

15.3 The gpu queue. This queue has two GPU nodes, i.e., g00 and g01. The node g00 has 128 CPUs, 2 GPU devices, and 1TB memory. The node g01 has 16 CPUs, 4 GPU devices, and 375GB memory. This queue is suitable for GPU tasks. Of cause, it is also suitable for a relatively large CPU jobs by its large CPUs and memory when it is free. 

15.4 The interactive queue. This queue has four small computing nodes for interactive jobs only, i.e., r00, r01, r02, and r03. Each node has 16 CPUs and 64GB memory. They are suitable for small interactive jobs. 

15.5 The headnote h00 is not used for computing jobs. It is for login purpose, and it is very suitable for data download and transfer due to it is connected to 10Gb/s network. We are safe to use all above queues by submitting jobs through Slurm. 


## 16. Submiting interactive and batch jobs to Slurm 

Currently, the computing resource is organized via Slurm on the Taco server. In principle, we want to run interactive and batch tasks by submitting all interactive jobs and batch jobs via Slurm. Two customized scripts are helpful to submit interactive and batch jobs, i.e., `sruntaco.sh` and `slurmtaco.sh`, respectively. `sruntaco.sh` is to submit interactive jobs, and `slurmtaco.sh` is to submit batch jobs. To use the above two scripts, please add the below command to the end of the Bash configuration file `~/.bashrc`. I.e.,  

```
vi ~/.bashrc 
source /storage/chen/data_share_folder/jinli/script_bcm/bashrc
```
 
This command will add the directory of the two scripts to the PATH variable. After adding the above command, please relogin the server to invoke the configuration to update the PATH. Now, it is ready to run `sruntaco.sh` and `slurmtaco.sh` from any directories on the server. 

## 17. Submitting interactive jobs via `sruntaco.sh` 

Sometimes, we need to run interactive tasks to test scripts or commands on the server. We should create an interactive Slurm job to run interactive commands. In principle, please never `ssh` to a computing node and run commands there. A direct `ssh` will bypass Slurm job management, and it will compete the computing resource with jobs submitted to the Slurm. Instead, we could use the below script `sruntaco.sh` to request an interactive job. 
```
sruntaco.sh -h

Options: 
  -h|--help                                           Help. 
  -j|--jobname JOBNAME                  Jobname. Default: $(basename "$(mktemp -u)"). 

Example: 
sruntaco.sh g01 
sruntaco.sh mhgcp-g01



Note: 
  1. This is to start an interactive session through SLURM. 
  2. The partition will be inferred automatically from the host. 

Date: 
  2021/12/01 

Authors: 
  Jin Li <lijin.abc@gmail.com> 
```

This script actually invokes a fresh bash session on a computing node by `srun` (a Slurm command). Enter: 

`srun -p "$partition" --nodelist="$host" -J "$jobname" -n 1 --pty bash`



Slurm will schedule the submitted interactive job once the computing resource is ready on the requested node. 

For example, the below command will open a new bash session on r03 by requesting 1 CPU and 2GB memory. We are ready to run interactive commands on r03. 

`sruntaco.sh r03  `


## 18. To submit batch jobs via `slurmtaco.sh` 

Mostly, we want to run a long batch of commands in a script, and we want to submit a batch job for it. 

`slurmtaco.sh -h`

Options: 




### Example: 

```
slurmtaco.sh -- cmd 
slurmtaco.sh -p short -t 2 -m 10G -- samtools index file.bam 
slurmtaco.sh -p mhgcp -t 8 -m 40G -n mhgcp-c02 -- cmd
```


Note: 
  1. `-n|--node` is exclusive and superior to `-x|--exclude`. 



The above script is to request detailed computing resource for the batch command. I.e., 

-p : indicating which partition to use. Default is the short queue. 
-t  : the number of CPUs to request.
-m : the size of memory for a job 
-n : a specific node in the queue



This script is actually calling `sbatch` (a Slurm command) to submit a batch script with the configured resource requirement. 

### Example 1: 
```
slurmtaco.sh -p short -t 2 -m 10G -- samtools index file.bam 
squeue -u $USER
```



To submit the running command `samtools index file.bam` (characters after `--`) to the “short” partition in Slurm (-p short). This job is requiring 2 CPUs (-t 2), and 10GB memory (-m 10G). `squeue -u $USER ` is to show the running status of all jobs submitted by user. 

### Example 2: 
```
slurmtaco.sh -p short -t 2 -m 10G -n mhgcp-d03 -- samtools index file.bam 
squeue -u $USER
```



Similar to the previous example, the job will be submitted to a specific node “mhgcp-d03” (-n mhgcp-d03) of the “short” queue. This `-n` is useful when we know which node is idle to avoid those busy nodes. 

### Notes 

Our dedicated machines 

Sometimes, the Slurm partition is extremely busy, our job is less likely scheduled soon, and we have emergency jobs to run immediately, we still have a last backup solution. We have our dedicated machines, c00, c02, and g01. We could directly login to these 3 machines and run a command there. Because these 3 machines are our dedicated machines, so, people from other labs will not complain about it. But please refrain from doing this unless necessary because it will compete resources and slow down all the jobs in the machine. 

### 19. Check the status of queues using sinfo 

Show detailed status of computing nodes by using `sinfo` 

`sinfo -O nodehost,available,statecompact,cpus,cpusstate,cpusload,memory,freemem`


Show the running jobs and resources using `squeue` and `sacct` 

```squeue -u $USER
squeue -u u247700 #Example from Cristal's user
sacct -u $USER -L -o JobID,JobName,State,User,Submit,ReqCPUS,ReqMem,NCPUS,Elapsed,NodeList,MaxRSS 
```

The column labeled ST displays each job's status: PD means "Pending" (waiting); R means "Running"; CG means "Completing" (cleaning up after exiting the job script).

## 20. How to create a good documentation: An Odyssey

"Always pass on what you have learned.” — Yoda

Documentation effectively connects humans and machines. 
Why is this so important? adapted from Berkeley guide

**For you:** 
You will be using your code in 6 months
You want people to use your code and give you credit
You want to learn self-determination
Others would be encouraged to contribute to your code

**For others:**
Others can easily use your code and build upon it
For science:
Advance the science
Encourage open science 
Allow reproducibility and transparency
What should you document about your research? Everything! All the data, notes, code, and materials someone else would need to reproduce your work

**Consider the following questions:**
How is your data gathered?
What variables did you use?
Did you use any code to clean/analyze your data?



To improve this, Benjamin D. Lee. create Ten simple rules article. <https://journals.plos.org/ploscompbiol/article?id=10.1371/journal.pcbi.1006561>
In summary:
Write comments as you code
Include examples
Include a quickstart guide
Include a README file with basic information
Include a help command for command line interfaces
Version control your documentation
Fully document your application programming interface
Use automated documentation tools - The best type of documentation is documentation that writes itself!
Write error messages that provide solutions or point to your documentation
Tell people how to cite your software

**Tips to Adding locally hosted code to GitHub** https://docs.github.com/en/migrations/importing-source-code/using-the-command-line-to-import-source-code/adding-locally-hosted-code-to-github

**Tips to Creating your first repository using GitHub Desktop** https://docs.github.com/en/desktop/installing-and-configuring-github-desktop/overview/creating-your-first-repository-using-github-desktop

**Tips to Create a repository in Web**
 https://docs.github.com/en/get-started/quickstart/create-a-repo

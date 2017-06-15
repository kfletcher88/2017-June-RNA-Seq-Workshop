Running jobs on the cluster
=============================

**1\.** Here at the UC Davis Bioinformatics Core we have a large computational cluster (named cabernet) that we use for our analyses. The job scheduling system we use on this cluster is called [Slurm](https://slurm.schedmd.com/). In this section, we will go through examples of the commands we will be using to interact with the cluster. First, what is a cluster?

![cluster diagram](cluster_diagram.png)

The basic architecture of a compute cluster consists of a "head node", which is the computer from which a user submits jobs to run, and "compute nodes", which are a large number of computers on which the jobs can be run. It is also possible to log into a compute node and run jobs directly from there. **Never run a job directly on the head node!**

---

**2\.** Now, let's look at the commands. First, log into the head node (cabernet.genomecenter.ucdavis.edu). The two main commands we will be using are srun and sbatch. 'srun' is used to run a single command on a compute node or to log into a compute node directly. Take a look at the options to srun:

    srun --help

The way we have set up our cluster requires that you specify a time limit and maximum memory size for your job. If your job exceeds these limits, then it will be terminated. So try running the following to log into a node:

    srun -t 1440 -n 1 --mem 8000 --reservation workshop --pty /bin/bash

This command is requesting a compute node with a time limit of 1440 minutes (i.e. 24 hours), one processor, a max memory of 8000Mb (i.e. 8Gb), using a compute reservation for this workshop (an option you would not normally use), and then finally, specifying a shell to run in a terminal ("--pty" option). Run this command to get to a compute node when you want to run jobs on the command-line directly.

---

**3\.** 'sbatch' is used to submit jobs to run on the cluster. Typically it is used to run many jobs at once. Look at the options for sbatch:

    sbatch --help

Generally, we do not use any options for sbatch... we typically give it a script (i.e. a text file with commands inside) to run. Let's take a look at a template script:

    wget https://ucdavis-bioinformatics-training.github.io/2017-June-RNA-Seq-Workshop/monday/slurm.sh
    cat slurm.sh

The first line tells sbatch what scripting language the rest of the file is in. Any line that begins with a "#" symbol is ignored, except lines that begin with "#SBATCH". Those lines are for specifying sbatch options without having to type them on the command-line every time. In this script, on the next set of lines, we've put some code for calculating the time elapsed for the job. Then, we set up the variables for the rest of the script. In this case, "$1" refers to the first argument to the script. So, for example, when you would run this script, you would run it using a sample name like so (**don't actually run this command yet!**):

    sbatch slurm.sh I892_S88

So the script takes "I892_S88" and puts it into the variable "$1". We then copy that into another variable called "sample", for more clarity. Then we use "$sample" to construct the names of the forward (R1) and reverse (R2) read files. The utility of doing this is that we can reuse the same script to run every sample. So to run another sample, we would do this (**again, do not run this command!**):

    sbatch slurm.sh C91_S79

Now, after the variable section, we load the modules that we will be using for this job. Finally, we run the actual commands using the variable names we created earlier. "${sample}" gets replaced with the actual sample name when it runs. And then at the end we calculate and print out the elapsed time.

---

**4\.** There are some more commands that 
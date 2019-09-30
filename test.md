=============================================== 
Introduction to IL Academic Compute Environment
===============================================

Overview 
========

The IL Academic Compute Environment provides an environment for supporting
and advancing academic research in a variety of areas. It currently consists of
Xeon, Xeon Phi, and `FPGAs <FPGA.html>`_.

Cluster Architecture 
==============================

Compute Nodes 
--------------

Each compute node has access to both NFS and Lustre file systems. The Xeon
and Xeon Phi nodes are connected to each other by Intel Omni-Path (Intel OPA) 100 Series
interconnect.  All nodes are connected via 10Gb Ethernet.

Access Nodes 
------------

Upon login, users are placed onto an access node, by default in their $HOME
directory. This access node has the same compilers and tools installed as each
of the compute nodes. You are free to do compilations and text editing from this
node but please keep compute intensive tasks confined to the compute nodes. If
you require direct access to a compute node for compilation/prototyping
purposes you can run the following to get an interactive session on a compute node:

::

   qsub -I

How to request an account 
==============================

You’ll need an account to be able to access the IL Academic Compute Environment.
To request an account please fill out the form `here <https://registration.intel-research.net/register>`_.

After filling out and submitting the registration you’ll receive an email
within 48 hours during business days. This will give you further instructions
of how to reach the nodes through the SSH gateway. You will also receive a
second email from Duo with instructions on how to set-up the two-factor
authentication required to log-in to the nodes.

How to access the nodes 
=======================

- Once you have been given access to the IL Academic Compute Environment
  you will be able to reach the cluster by first ssh’ing into the access node

- Please enter the **username** and **password** you created while requesting
  an account to log-in.

- You will then be prompted to select your preferred Duo 2FA method.

- Once you’ve gotten through the 2FA and connected to a host, you will be in
  your home directory on the access node. 

- From here you can push data to the shared storage located at
  /export/<university name> or submit a job to be ran on the cluster through
  the PBS scheduler. These procedures are described in the following sections.    

Pushing a dataset to the nodes 
==============================

Your home directory has limited storage, so any data should be uploaded directly to each
cluster’s shared storage. The shared storage is split into a few partitions.

::

   /export/shared – data to be shared with everyone.
   /export/intel – for intel employees use 
   /export/software – shared software (caffe, torch, MXnet, etc)
   /export/<school> - NFS based storage for quick file access
   /export/<school>/lustre – lustre based storage for large files

Any data pushed to any of these should be accessible from any of the nodes on
the respective cluster. Please use your preferred SCP or SFTP program for
pushing datasets to this location.

Using WinSCP to push data to the nodes 
======================================

- Download `WinSCP <https://winscp.net/eng/downloads.php>`_
- Open WinSCP
- Select “New Site”
- Type the host name and your username
- Hit Save. This should result in this
- Click login.
- Type you password at the prompt
- Use your mobile device to accept the Duo notification.

You should now be in your home directory. Feel free to update small files here
if you’d like to compile/prototype. For any larger datasets please upload them
to **/export/<school>/lustre**. You can get there by clicking through the UI
starting with the folder/button in the upper right.

Just drag and drop from your local machine on the left panel to the cluster in
the right panel. Anything uploaded to your home directory or shared storage
will be available on all the nodes in the cluster.

How to access additional compilers and libraries
================================================

Multiple compilers and libraries are available on the cluster via the ``module`` command.  To see the available compilers use the ``module avail`` command.

::

     $ module avail

   -------------------------- /opt/ohpc/pub/modulefiles --------------------------
      cmake/3.13.4    gnu8/8.3.0    intel/18.0.0.128    intel/19.0.3.199 (D)    prun/1.3

     Where:
      D:  Default Module

   Use "module spider" to find all possible modules.
   Use "module keyword key1 key2 ..." to search for all possible modules matching any of the "keys".


To load a specific compiler version, use the module load command

::

   $ module load intel
   $ icc --version
   icc (ICC) 19.0.3.199 20190206
   Copyright (C) 1985-2019 Intel Corporation.  All rights reserved.


Note that after a compiler family is loaded, you will then have access to various MPI libraries.  After you've loaded the compiler family, use ``module avail`` again to find out what MPI libraries are available.


::

   $ module load intel
   $ module avail

   -------------------------------- /opt/ohpc/pub/moduledeps/intel --------------------------------
      openmpi3/3.1.3

   ---------------------------------- /opt/ohpc/pub/modulefiles -----------------------------------
      cmake/3.13.4    gnu8/8.3.0    intel/18.0.0.128    intel/19.0.3.199 (L,D)    prun/1.3

     Where:
      D:  Default Module
      L:  Module is loaded

   Use "module spider" to find all possible modules.
   Use "module keyword key1 key2 ..." to search for all possible modules matching any of the "keys".


   $ module load openmpi3
   $ module avail

   --------------------------- /opt/ohpc/pub/moduledeps/intel-openmpi3 ----------------------------
      phdf5/1.10.4

   -------------------------------- /opt/ohpc/pub/moduledeps/intel --------------------------------
      openmpi3/3.1.3 (L)

   ---------------------------------- /opt/ohpc/pub/modulefiles -----------------------------------
      cmake/3.13.4    gnu8/8.3.0    intel/18.0.0.128    intel/19.0.3.199 (L,D)    prun/1.3

     Where:
      D:  Default Module
      L:  Module is loaded

   Use "module spider" to find all possible modules.
   Use "module keyword key1 key2 ..." to search for all possible modules matching any of the "keys".


For more information see the :doc:`OpenHPC` page.



How to submit a task 
====================

The IL Academic Compute Environment utilizes the PBS scheduler. Jobs submitted 
to the cluster will run on one or more of the available nodes. Ensure that your 
data exists in the shared storage location (/export/<school> or /export/<school>/lustre) 
so that it is available to the node that ends up processing your job. 

To submit your task, first add the following to your ~/.bashrc and ‘source’ it
so that you have access to the PBS commands.

::

   export PATH=$PATH:/opt/pbs/default/bin

After this is complete, you can verify that your environment is set up properly
by running qstat -B, which should give the output listed below.


::

   qstat -B

   Server           Max   Tot   Que   Run   Hld   Wat   Trn   Ext   Status 
---------------- ----- ----- ----- ----- ----- ----- ----- ----- -----------
   iam-pbs          0     3     0     1     0     0     0     2     Active


- There are two main queues that are available to you in the new PBS installation:

   - knl (Knights Landing)
      - KNM nodes have 288 ncpus each (Professors are being contacted about interest and will need to follow additional approval steps to gain access)
   - skl (Skylake Xeons)
      - SKL nodes have 112 ncpus each

- The default queue is skl. If you submit a job and don’t specify the queue, your job will be submitted to the skl queue automatically. There is a limit of 10 SKL nodes per user. If you require more nodes, you can reach out to us and we will try to accommodate your request.

- To submit a job to a non-default queue, the queue must be specified in the qsub command:

  :: 

   qsub -q <queue-name> <job-script>
   qsub -q <queue-name> -I

- To request a single SKL node (default queue):

  ::

   qsub -lselect=1:ncpus=112 <job-script>

- To request a single KNL node:
  
  ::

   qsub -q knl -lselect=1:ncpus=272 <job-script>


To submit your first job use the qsub command. This job’s output will be sent
to ~/shared.txt

::

   qsub ./HelloWorld.sh 
   68.iam-pbs (Where 68 is an unique job id )

HelloWorld is a simple script which sleeps for 30s on a node and writes the
directory structure of /export/data1/ to your home directory at shared.txt.

::

   #PBS -l select=1:ncpus=112 -lplace=excl 
   echo "Sleeping for 30 seconds on `cat $PBS_NODEFILE`" >> ~/shared.txt echo "Writing directory structure of ls -l
   /export/software to ~/shared.txt" >> ~/shared.txt ls -l /export/software >>
   ~/shared.txt

To view the status of your running jobs, use the qstat command:

::

   -bash-4.2$ qstat

   Job id           Name             User             Time Use S Queue 
---------------- ---------------- ---------------- -------- - -----
   67.iam-pbs       64_node_pbs.sh   labuser         5512:16: R skl
   69.iam-pbs       HelloWorld.sh    labuser         00:00:00 E skl

To stop a task prior to its completion use qdel with the job id:

::

   -bash-4.2$ qdel 
   67.iam-pbs

Important Takeaways 
===================

- The first line of your script defines how many nodes you want to run on and
  whether or not you want your job to have exclusive access to the nodes while
  running.

   - select=1 – increase to increase the # of nodes your job runs o - n
   - lplace=excl – exclude this if you don’t require exclusive access to the
     nodes your job is scheduled on during the duration of your run- - 

- Make sure any script you put together is executable (chmod 0755
  ./HelloWorld.sh).

- When running the command use the whole path to the script you are
  referencing. For example if you have a “HelloWorld.sh” in your home directory
  do this.

  :: 

   qsub ./HelloWorld.sh

  Not this.

  ::
  
   qsub HelloWorld.sh

- When capturing stdout from a script run make sure to put the file in your
  home directory. Example:

  ::

   ls /tmp > ~/tmp.txt


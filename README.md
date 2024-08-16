# This is a modified version of the Vertex Project for the Yazdan Lab
A spiking neural network (SNN) model with simulated extracellular potential recordings. 
For more information, see the [Vertex page](http://vertexsimulator.org/).
This repository is forked off of the Vertex 2 Codebase, which includes an STDP implementation 
that can be activated in the top-level simulation scripts.
Paired-Pulse field stimulation has been added.

## The Model
The model in this project is based off the cortical slice model described in Tomsett et al. 2014, with the below main differences: 

- Our model is slightly larger, with 1.5x1.5x2.6mm in dimensions. 
- We've added more recording sites, placing 54 recording sites in a 3D grid, leaving 0.25mm of buffer on each side of the XY-plane. 
- We've added the ability to scale the mean and std of neuronal input currents for the ADEX input model.  Scaling parameters are in the top-level script.
- An optogenetic stimulation input model (InputModel_i_opto.m) has been added with example usage in bsf_opto_single_pulse.m
- Parametric definitions for 3D field stimulation electrodes are demonstrated in bsf_surface_single_pulse.m and bsf_stim_single_pulse.m
- Closed loop stimulation triggering off LFP oscillations can be defined in bsf_stim_single_pulse.m and bsf_opto_single_pulse.m (and activated in the top-level script)

## Running the Model

The top level script is: nsg_main_stdp.m
This is where the script can be configured to run on a local machine, STDP can be enabled, and scripts for field and optical stimuation can be defined.
There are several examples of nsg_main_stdp.m saved as nsg_main_<stimulation_type>.m as examples of how to activate different stimulation modules
If running locally, set s.run_local = true; % Simulation runs on a lower number of processors at reduced unit density.
If running on cluster, set s.run_local = false;  % Simulation runs at full density on 8 processor nodes.

### Computing Resource
As the model is computationally intensive to run, it is recommended that the model be run on a computing cluster instead of a local machine. 
[NSG (Neuroscience Gateway)](https://www.nsgportal.org/) is a good candidate, as it provides an easy-to-use UI and responsive support.
This cluster can run simulations at full density using 8 processor nodes and 240 GB RAM.  The entire code folder must be compressed into a .zip file
to download to the cluster. Users need to fill out a registration form to get a login.

Running a simulation on the portal is pretty easy:

1. Setup all the code and data in one enclosing folder and create a .zip file of the folder. 
   (in the nsg_main_stdp.m file set s.run_local = false to run a full density simulation)
   **Note:** NSG requires top-level scripts to be run from the top-level folder

2. Login to NSG. There will be a Folders side bar for creating subfolders with Data and Task lists.

3. In a Data list, download the .zip file as a Data set and give it a name.

4. In the Task list, click the "Create New Task" button. 
   Here you will name the task, chose the Data set, Select the Matlab on Expanse tool,
   Set Parameters for job, and run the task.

The Parameters are:
- Maximum Hours to Run: 48 is the maximum for free accounts.  The current simulation takes 24.
- Main Input Matlab filename: nsg_main_stdp.m (or what ever name you are using)
- sub-directory name: <blank>
- Number of CPUS per Task: 8 (This can be changed in the code, but 8 is pretty good)
- Number of GB Memory: 243 is the maximum
- Name for the compressed output file: <blank>

When the simulation completes you can download an output.tar.gz file which contains
a copy of the code and all the results in a vertex_results folder.  This is a very 
large (>4 GB ) file since it contains the entire network topology.  You can use 
testfigs('<full pathname to vertex_results folder>') once you have uncompressed the 
archive.  This will take 10 minutes to generate a figures of LFP and spikes coincident
after a stimulus.
 
### Run Files
- `vertex_2/nsg_main_stdp.m` Is the main entrypoint script to run the model. It can be configured to run locally or on the NSG.
- `vertex_2/bsfModel_yazdan/run_bsf_model_stdp_sim.m` Is the main function that encapsulates model initialization/simulation.
- `vertex_2/bsfModel copy/bsf-*.m` These files store the main parameter scripts used for setting up the model.

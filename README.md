# Here is a simplified, step-by-step guide to using Jupyter Notebooks on the Lengau HPC system. 

## Step 1: Login into Lengau
```
ssh YOURUSERNAME@lengau.chpc.ac.za
```

## Step 2: Request and interactive node
You need an interactive compute node to run Jupyter.

1. Request a single-core interactive node:

```
qsub -I -P PROJ0101 -q serial -l select=1:ncpus=1:mpiprocs=1
```
**Note**: Replace PROJ0101 with your project name (e.g., ERTH01234).

2. **Record the compute node ID** (e.g., cnode1234), you will need it later.

## Optional: Request a Node with more Cores or Memory
- For multiple notebooks, request 4 cores:
```
qsub -I -P PROJ0101 -q serial -l select=1:ncpus=4:mpiprocs=4
```
- For large datasets, request a **bigmem node** (shared with others):
```
qsub -I -P PROJ0101 -q bigmem -l select=1:ncpus=4:mpiprocs=4
```
## Step 3: Load Python Module
1. Check for available Python modules:
```
module avail 2>&1 | grep python
```
2. Load your preferred Python version (example below):
```
module add chpc/python/3.6.0_gcc-6.3.0
```
## Step 4: Set up Jupyter Security
Since multiple users share the system, you must secure your notebook.
1. **Generate a Juypter config file**:
```
jupyter-notebook --generate-config
```
- This creates the config file: /home/YOURUSERNAME/.jupyter/jupyter_notebook_config.py. To list the config file run ```$ la -al```
2. **Set a password**:
  ```
python
from notebook.auth import passwd
passwd()
```
- Enter and Verify your password **Try not to forget this password as you will need it later**. This will result in a **password hash** being generated (e.g 'sha1:abcd1234...')
- Copy the generated sha file

3. **Edit the config file**
```
cd ~/.jupyter/
vim jupyter_notebook_config.py
```
- Add this line to the config file
  ```
  c.NotebookApp.password = 'sha1:abcd1234...'  # Use your actual hash
  ```
## Step 5: Start the Jupyter Notebook
1. On the interactive node, start Jupyter:
```
jupyter-notebook --no-browser
```
2. Note the **port number** and **nodeID** displayed (e.g., ```http://cnode1234:8888```).

## Step 6: Configure SSH on your Local Machine 
1. Open a terminal on your local machine and create a ```.ssh/config``` file:
```
touch ~/.ssh/config
```
2. Add the following lines to the file:
```
Host cnode*
    Hostname %h
    User YOURUSERNAME
    ProxyCommand ssh YOURUSERNAME@lengau.chpc.ac.za nc %h 22
    LocalForward 8888 localhost:8888
```
**Note**: Replace ```YOURUSERNAME``` with your Lengau username.

## Step 7: Connect to the Compute Node (From your local machine)
1. Open a terminal and SSH into your compute node (replace cnode1234 with your node ID):
```
ssh cnode1234
```
- Enter your password twice (once for Lengau and once for the cnode). 

## Step 8: Access Jupyter in Your Browser
1. On your local machine, open your browser and go to: 
```
http://localhost:8888
```
Enter the Jupyter password you set earlier.

**Each interactive session will have a different node and port number. Update your .ssh/config file accordingly.**

This process ensures a secure and efficient way to run Jupyter on Lengau. If you encounter any issues, send a ticket to the CHPC Helpdesk at https://users.chpc.ac.za/helpdesk/tickets/submit/ or send a email to msovara@csir.co.za for further assistance. 

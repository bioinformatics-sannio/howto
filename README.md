## Computational Infrastructure available at DST Unisannio

- Superdome server (224 CPU cores Intel(R) Xeon(R) Platinum 8280 CPU @ 2.70GHz, 1.5 TB RAM, 1 GPU NVIDIA Tesla V100)

In this document some technical information to correctly adopt the computational infrastrucutre.

## Setup miniconda local notebook with python, pytorch, R

Get the last version of miniconda at https://repo.anaconda.com and install it in your local home.

```console
wget https://repo.anaconda.com/miniconda/Miniconda3-py39_4.9.2-Linux-x86_64.sh
bash Miniconda3-py39_4.9.2-Linux-x86_64.sh 
```

Update Conda

```console
conda update -n base -c defaults conda
```

Install pytorch with cuda enabled

```console
conda install pytorch torchvision -c pytorch
conda install -c conda-forge biopython
```

Install R and R essentials. Set the appropriate version if needed.

```console
conda install -c conda-forge r-base=4.0.3
conda install -c conda-forge r-essentials
```

Install Jupiter notebook

```console
conda install -c conda-forge notebook
```

Lauch the notebook server in a tmux session. 

```console
tmux new -s "Jupiter"
jupyter notebook --no-browser --port=8899
```

If the port number 8899 not available choose a different one. The server gives the link to connect with a tocket. Copy in the clipboard and exit the session with Ctrl-b d.

Some useful commands to manage a tmux session. More info about tmux available here https://github.com/tmux/tmux/wiki/Getting-Started.

```console
tmux ls                  # to see if there are any other tmux sessions
tmux a                   # this will connect to the most recent session that was created
tmux new -s "Jupiter"    # launch a new session names with -s
tmux attach -t "Jupiter" # Connect to a sessione named with -s
```

In your local shell (client side) set an ssh tunnel and connect to the notebook with http://localhost:8899
The port should be the same chose for the notebook server. Information about <userid> <ip> and <sshport> are the same you adopt for ssh login.

```console
ssh -N -f -L localhost:8899:localhost:8899 <userid>@<ip> -p <sshport>
```

## Setup a local podman container with R and rstudio server
 
Create in your local home a directory shared with the container and set the appropriate podman uid
```console
mkdir rstudioshare
podman unshare chown 1000:1000 -R $HOME/rstudioshare
```

Launch in a tmux console an rstudio podman container (See how to manage tmux session in the previous section). Choose the appropriate port number if not available and a password.
 
```console
podman run --rm -p 8787:8787 -v $(pwd)/rstudioshare:/home/rstudio/hosthome:Z -e PASSWORD=<apassword> rocker/rstudio
```
 
In your local shell (client side) set an ssh tunnel and connect to rstudio server with http://localhost:8787 and loging with rstudio/<apassord>.
The port should be the same chose for the container. Information about <userid> <ip> and <sshport> are the same you adopt for ssh login.

```console
ssh -N -f -L localhost:8787:localhost:8787 <userid>@<ip> -p <sshport>
```
 
 

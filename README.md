## Computational Infrastructure available at DST Unisannio

- Superdome server (224 CPU cores Intel(R) Xeon(R) Platinum 8280 CPU @ 2.70GHz, 1.5 TB RAM, 1 GPU NVIDIA Tesla V100)

## Setup miniconda local notebook with python, pytorch, R

Get the last version of miniconda at https://repo.anaconda.com with 
```console
wget https://repo.anaconda.com/miniconda/Miniconda3-py39_4.9.2-Linux-x86_64.sh
bash Miniconda3-py39_4.9.2-Linux-x86_64.sh 
```

### update Conda
conda update -n base -c defaults conda

### install pytorch
conda install pytorch torchvision -c pytorch

conda install -c conda-forge biopython


### install R 4 base and essentials
conda install -c conda-forge r-base=4.0.3
conda install -c conda-forge r-essentials

### install Jupiter notebook
conda install -c conda-forge notebook


 launch Jupiter in a tmux session
 to exit use ^Bd
 last tmux session tmux a  
tmux
jupyter notebook --no-browser --port=8899

 set an ssh tunnel in your local shell
 connect with your browser using the Jupiter token
 choose a free port
ssh -N -f -L localhost:8899:localhost:8899 luce@212.189.146.15 -p 22201


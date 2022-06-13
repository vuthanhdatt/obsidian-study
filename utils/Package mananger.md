## conda

[How to Install Miniconda from the Command Line (Linux/MacOS) | Engineering for Data Science](https://engineeringfordatascience.com/posts/install_miniconda_from_the_command_line/)

```shell
mkdir -p ~/miniconda3
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh -O ~/miniconda3/miniconda.sh
bash ~/miniconda3/miniconda.sh -b -u -p ~/miniconda3
rm -rf ~/miniconda3/miniconda.sh
~/miniconda3/bin/conda init bash
~/miniconda3/bin/conda init zsh
```

- list all environment
`conda info --envs`
- turn off auto activate base env
```bash
conda config --set auto_activate_base false
```

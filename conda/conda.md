# CONDA


## Environments

* list envs
  ```
  conda env list
  ```
* create env
  ```
  conda create --name <name> python=<version>
  ```
* delete env
  ```
  conda env remove --name <name>
  ```
* activate env
  ```
  source activate <name>
  ```
* deactivate env
  ```
  source deactivate
  ```
* save env to a yaml file (Oftentimes build numbers don't match across platforms. Use the `--no-builds` flag with `conda env export`.)
  ```
  conda env export --no-builds >myenv.yml
  ```
* create env from a yaml file
  ```
  conda env create --name <name> --file myenv.yml
  ```


## Packages

* search package
  ```
  conda search <expression>
  ```
* install
  ```
  conda install <package>
  ```
* update
  ```
  conda update <package>
  ```
* update all
  ```
  conda update --all
  ```
* list installed packages
  ```
  conda list
  ```
* remove
  ```
  conda remove --name <env> <package>
  ```


## Doc

* [conda cheatsheet](https://conda.io/docs/_downloads/conda-cheatsheet.pdf)
* [docs](https://conda.io/docs/index.html)


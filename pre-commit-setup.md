# What is pre-commit?
Pre-commit is a tool written in python that allows user to easily define and install git hooks (Shell scripts found in the hidden . git/hooks directory of a Git repository. These scripts trigger actions in response to specific events, so they can help automate development lifecycle.).  
- Official introduction to this library can be found on the [project website](https://pre-commit.com/).  
- All hooks triggered during commit should be defined in [.pre-commit-config.yaml](.pre-commit-config.yaml) file.
- List of available hooks can be found [here](https://pre-commit.com/hooks.html)


# Hooks proposed for ML project:
This repo contains example [.pre-commit-config.yaml](.pre-commit-config.yaml) file designed for machine learning project. All hooks defined in it, would be triggered before commit. It contains three types of hooks:
1. Hooks formatting jupyter notebooks code:
- **nbqa-ruff** - lightweight code linter that does not catch a lot, but it is really fast and it is only automatic formatter that removes unused imports (unused variables too, so be aware) that i was able to found.
- **jupyter-notebook-cleanup** - clears notebook outputs, and metadata, so git would not consider them as modified files every time notebooks were run.
- **nbqa-black** - apply black formatter to jupyter notebooks
- **nbqa-isort** - sorts imports in notebooks 
2. Hooks formatting python code.
- **ruff** - the same as nbqa-ruff, but for python scripts. 
- **autoflake** - apply flake formatting for python scripts (More complex and slower version of ruff, so you can consider to remove one of them. Both are kept in this repo to get benefits from autoflake and stay consistent with notebook formatting - where only ruff is available).
- **isort** - sorts imports in python scripts.
3. Others.
- **no-commit-to-branch** prevent user from committing directly to main, master or dev branches. Specified branches can be edited in hook "args".
-  **detect-private-key** looks for string with high entropy, so, there is a chance that it find password taht you accidentally almost commit, but not for sure.
- **check-added-large-files** check if all uploaded files have less than 500kb, it is possible to change that threshold by providing correct args.
- **trailing-whitespace**  trims trailing whitespace,
- **end-of-file-fixer** ensures that a file is either empty, or ends with one newline.
- **check-yaml** checks yaml files for parsable syntax.  

*All hooks except of no-commit-to-branch, detect-private-key, check-added-large-files and check-yaml WILL ATOMICALLY MODIFY YOUR CODE WHILE YOU COMMIT, but if they do so, they stop committing procedure, so you can manually check and accept those changes before actual commit.* 

# Other hooks worth consideration.
* if you work a lots with formats like **json**, **toml**, **xml**, there are hooks that checks syntax, format those files, or sort them.
* If you work with aws, there is **detect-aws-credentials**
* **mypy**, **nbqa-mypy** for type checking.
* **pydocstyle**, **nbqa-pydocstyle** for docstring checking.
* **pylint**, **nbqa-pylint** for complex code analyses.

*Note that all hooks have to be bind to correct repo. You can determinate which one to use in [hooks collection](https://pre-commit.com/hooks.html).*

# How to setup pre-commit for ml project:

1. Go to project folder with git initialized or initialize new repo.
```console
git init
```
3. Install pre-commit:
```console
pip install pre-commit
```
4. Verify installation:
```console
pre-commit --version
```
4. Copy [.pre-commit-config.yaml](.pre-commit-config.yaml) file to your main project folder.
5. Run pre-commit install to set up the git hook scripts
```console
pre-commit install
```
6. (Optional) run all hooks to verify, everything works properly:
```console
pre-commit run --all-files
```
*First run will take longer time, because of environments setups, unless you are one one of the banned branches, in that case. Procedure will fail, after second hook. We will deal with it soon.*

7. Commit changes:
```console
git add ./.pre-commit-config.yaml
git commit -m "pre-commit configured."
```
If you try to commit it on one of the banned branches then procedure will fail, so you have to specify to omit no-commit-to-branch hook execution.
```console
git commit --no-verify -m "pre-commit configured."
```
or
```console
SKIP=no-commit-to-branch git commit -m "pre-commit configured."
```
*If you want to skip more than one hook, then provide them as a comma separated list of hook ids.*

8. (Optional) If you want to add this pre-commit file to other branch it may be recommended to use cherry pick.
- switch to branch that you want to add file.
```console
git checkout <other-branch-name>
```  
-  find commit hash that you use will pick:
```console
git log <branch-with-pre-commit-name> -1
```
*it produce output in format:  
commit \<commit-hash\> (\<branch-name\>)  
Author: ...  
Date: ...*  
- cherry pick commit:
```console
git cherry-pick <commit-hash>
```
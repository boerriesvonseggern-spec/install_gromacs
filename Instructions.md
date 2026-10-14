# Instructions
Welcome to the Repo. Here is additional information about installing gromacs with spack.
Firstly, to get spack set up, go to the spack source directory and source the setup-env.sh file
```bash
# For bash/zsh/sh
. spack/share/spack/setup-env.sh
```
In order to have plumed, it is necessary to modify the config such that spack thinks plumed is supported with the newest gromacs versions.
To do this, modify the dictionary in the package.py file of gromacs
```
find "$HOME/.spack/package_repos" -path "*/repos/spack_repo/builtin/packages/gromacs/package.py"
```
look for a dictionary mapping the gromacs versions to the plumed versions and modify it for your version.
Note that this is also necessary when disabling plumed for later scripts to identify the correct version.

Then, to compile the gromacs versions for all architectures, call the install gmx all script. 
The first argument has to be the gromacs version, and all later arguments will be appended to the install command. This way you can toggle spack versions, toggle compiler flags, etc.
So, to install gromacs 2026.3 without plumed, run
```
bash install_spack_gmx_all.sh 2026.3 ~plumed
```
For multiprocessing during the compilation, change the N_JOBS variable at the top of the script.

Then, we need to create the modulefiles for users to use the modules. This is automated through the "create_spack_gmx_module.sh" script. This one is quite simple,
it looks through all the installed gromacs versions, figures out where the gromacs installation of the different builds exist, and then goes ahead and fills in the blanks into the relevant parts of the template module_file_template_spack.tcl.
The things getting replaced are marked |KEY| in the template.
For flexibility, I have set up the spack and module paths to be variables that are set in the "create_spack_gmx_module.sh" script.
To find the spack path, run something like
`spack location -i "gromacs@2026.3 ~mpi ~double ~cuda arch=linux-debian13-ivybridge"`
and then take everything ahead of "/opt/spack" as the SPACK_ROOT, the module_root can be chosen freely.
With this, run something along the lines of 
`bash create_spack_gmx_module.sh 2026.3`
to set up the modulefiles.


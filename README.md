# ROOT and Geant4 with Lua modules

This playbook builds ROOT and Geant4 and creates versioned Lmod modulefiles:

- `/opt/modulefiles/root/6.40.04.lua`
- `/opt/modulefiles/geant4/11.4.2.lua`

Paths and versions follow the variables in `install.yml`. Each module uses the
installed setup script, preserving software-specific paths and Geant4 dataset
environment variables, and adds the installation prefix to `CMAKE_PREFIX_PATH`.
Only one version of each package can be loaded at a time.

## Run

Edit `ansible.ini` for your target, then run from this directory:

```bash
ansible-playbook install.yml
```

For ROOT and Geant4 already installed at the configured paths:

```bash
ansible-playbook install.yml --tags modulefiles
```

Lmod is installed using apt by default. Lua modules use `source_sh`, which requires
Lmod 8.6 or newer. If your cluster already manages Lmod, set `install_lmod: false`
and adjust `lmod_init_script` to its shell initialization script. This playbook
targets apt-based systems, as did the original installation.

## Use

Open a fresh login shell after running the playbook, then:

```bash
module avail
module load root/6.40.04 geant4/11.4.2
root-config --version
geant4-config --version
module unload geant4/11.4.2 root/6.40.04
```

The playbook replaces `/etc/profile.d/root-geant4.sh` so it initializes Lmod when
needed and registers `/opt/modulefiles`; it no longer automatically sources ROOT
and Geant4. Start a fresh login session to discard environment variables from the
old profile script before testing module unloading.

For a non-login Bash job, initialize explicitly if necessary:

```bash
source /usr/share/lmod/lmod/init/bash
module use /opt/modulefiles
module load root/6.40.04 geant4/11.4.2
```

Adjust the initialization path if using a custom Lmod installation. Re-running
the module tasks updates the current version's files and preserves other versions.

Lmod documentation: https://lmod.readthedocs.io/en/latest/260_sh_to_modulefile.html

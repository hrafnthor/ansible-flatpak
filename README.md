# ansible-flatpak
An Ansible role for managing Flatpak repositories and packages

---


### Requirements / Dependencies

This role requires the Ansible collection `community.general` to be installed on the system.

This role requires the `jsonschema` Python package be installed. To do so for example using `pip` run the following:

```shell
pip install jsonschema
```

### Setup

Before the role can be used it needs to be added to the machine running the playbook, and as of writing this, this role is not hosted on Ansible-Galaxy only on Github.

1. Create a `requirements.yml` file in the root directory of the playbook being worked on.

2. Add the following definition inside the `requirements.yml` file:

```yml
- name: hth-flatpak
  src: https://github.com/hrafnthor/ansible-flatpak.git
  scm: git
  version: <tag or branch to use>
```

3. Install the requirements by executing:

```shell
ansible-galaxy install -r .requirements.yml
```

This will allow any playbook run from this machine to use the role `hth-flatpak`

### Variables

The package expects the following object definition to be available to it:

```yaml
flatpak:
  remove: bool                Indicates if the package 'flatpak' should be removed or not. If false or not defined, then 'flatpak' will be installed.
  remotes:
    - name: string            [required] The identifier to give the remote repository locally. Is used when selecting a repository to install packages from.
      url: string             [required] The url of the repository.
      users: string array     A list of users for whom the remote should be configured for. If this field is missing, the remote is installed system wide.
  packages:
    - name: string            [required] The package ref to install. This can be a remote domain name 'is.hth.packagename' or a more elaborate string 'app/io.elementary.calculator/x86_64/stable'.
      remote: string          [required] The local identifier of the remote repository to install the package from.
      update: bool            If true will update the package to the latest version.
      users: string array     A list of users for whom the package should be installed for. If this field is missing, the package is installed system wide.
```

For example the following sets up two remote repositories, `flathub` and `elementaryos`, with the former being setup both system wide and for a user Hrafn, while the latter is only setup for users Hrafn and Ansible.

**Note: To be able to install a package from a repository for a specific user, the repository has to be setup for that user. It is not enough to install it system wide.**

It then sets up three packages, one installed system wide from the system wide available repository, one from `flathub` but only for user Hrafn while the third one is installed for both users Hrafn and Ansible from the `elementary` repository.

```yaml

flatpak:
  remove: false
  remotes:
    - name: flathub
      url: https://dl.flathub.org/repo/flathub.flatpakrepo
    - name: flathub
      url: https://dl.flathub.org/repo/flathub.flatpakrepo
      users:
        - hrafn
    - name: elementary
      url: https://flatpak.elementary.io/repo.flatpakrepo
      users:
        - hrafn
        - ansible
  packages:
    - name: org.gnome.gedit
      remote: flathub
      update: true
    - name: app.devsuite.Manuals
      remote: flathub
      update: true
      users:
        - hrafn
    - name: app/io.elementary.calculator/x86_64/stable
      remote: elementary
      update: true
      users:
        - hrafn
        - ansible
```
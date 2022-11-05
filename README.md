# Ubuntu setup guide

Tested for xubuntu 20.04.3 LTS (Focal Fossa) in a Lenovo Thinkpad X1 Carbon 8.

## 0. Basic configuration

### Key bindings

* Set Super key as shortcut for Whisker menu at "Keyboard -> Application Shortcuts".
* At "Keyboard -> Layout", add the "English (US, intl., with dead keys)", and set the "Change layout option" to "Alt+Caps Lock".

### Power management

* At "Power Manager", ensure that "When laptop lid is closed" is set to "Suspend".

#### TLP for battery thresholds

1. Install TLP `sudo apt install tlp tlp-rdw acpi-call-dkms`.
2. Edit [the configuration files](https://linrunner.de/tlp/settings/introduction.html#config-files) to set the battery charging thresholds.
3. Activate the thresholds by running `sudo tlp start` and verify the active settings by running `sudo tlp-stat -s -c -b`.

## 1. Terminal

1. Install `curl`, `git`, `zsh`, i.e., `sudo apt install curl git zsh`.
2. Install [oh-my-zsh](https://github.com/ohmyzsh/ohmyzsh) and set it as default shell.
3. Log out and log back in.

## 2. Messaging and browsing

1. Install [Flatpak and add Flathub remote](https://flatpak.org/setup/Ubuntu) (a potential TLS error can be solved using `sudo apt install --reinstall ca-certificates`).
2. Install [Telegram using Flatpak](https://flathub.org/apps/details/org.telegram.desktop).
3. Install [Signal](https://signal.org/en/download/).
4. Install [Brave browser](https://brave.com/linux/#release-channel-installation) and use the sync code from Telegram/Signal saved messages.
5. Install [Slack using Flatpak](flatpak install flathub com.slack.Slack).

## 3. Git and GitHub

1. [Generate a new ssh key](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent) and then [add it to your GitHub account](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account).
2. Download gpg key from MEGA and import it, i.e., `gpg --import private.key`.
3. Trust the imported key by first checking its ID using `gpg --list-keys --keyid-format LONG`, then `gpg --edit-key <key-id-after-rsa4096>`, enter `trust`, assign a decision (e.g., "5 = I trust ultimately"), enter `quit` and kill the gpg agent as in `gpgconf --kill gpg-agent` so that the changes take effect.
4. Configure git:
  * set the [git user name and email](https://git-scm.com/book/en/v2/Getting-Started-First-Time-Git-Setup)
  * add [signing key to git](https://docs.github.com/en/authentication/managing-commit-signature-verification/telling-git-about-your-signing-key), i.e., `gpg --list-keys --keyid-format=long` and `git config --global user.signingkey <key-id-after-rsa4096>`
  * set the name of the default branch to "main", i.e., `git config --global init.defaultBranch main`
  * set git to sign all commits, i.e., `git config --global commit.gpgsign true`
8. Download the [emacs gitignore file](https://github.com/github/gitignore/blob/main/Global/Emacs.gitignore) and set it as global gitignore, i.e., `git config --global core.excludesfile ~/Emacs.gitignore`

## 4. Docker

https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-ubuntu-20-04

## 5. Mamba

1. Install [Mambaforge](https://github.com/conda-forge/miniforge#mambaforge).
2. Initialize the shell by running `mamba init` (ensure that the command modifies `.zshrc`, otherwise run `mamba init zsh`).

### Software to install in the base environment

1. Install the packages from the `environment.yaml` file, i.e., from the root of this repository, and within the base environment, run `mamba env update -f environment.yaml`.
2. Follow the instructions below for package-specific configuration.

#### Jupyter notebook

1. Generate the base configuration, i.e., `jupyter-notebook --generate-config`.
2. In the generated config file, uncomment the line with `c.NotebookApp.open_browser = True` and set it to `False`.

#### direnv

1. Update the `.zshrc` file to automatically activate it by adding:

    ```
    # >>> direnv setup for zsh >>>
    eval "$(direnv hook zsh)"
    # <<< direnv setup for zsh <<<
    ```

#### AWS CLI

1. Configure the named profiles using the command `aws configure --profile <profile-name>`.

### Autocompletion for oh-my-zsh

#### Snakemake
    
Update the `.zshrc` file to activate autocompletion by adding:

```
# >>> snakemake autocompletion for zsh >>>
compdef _gnu_generic snakemake
# <<< snakemake autocompletion for zsh <<<
```

#### Terraform

Add the autocompletion setup to the zsh profile by running `terraform -install-autocomplete`

#### mamba

1. Clone the [esc/conda-zsh-completion repository](https://github.com/esc/conda-zsh-completion) at `$ZSH_CUSTOM/plugins/`, i.e.: `git clone https://github.com/esc/conda-zsh-completion ${ZSH_CUSTOM:=~/.oh-my-zsh/custom}/plugins/conda-zsh-completion`
2. Modify the `plugins` line of `.zshrc` and add `conda-zsh-completion`, e.g., `plugins=(git conda-zsh-completion)`

**Optional**: to work with [emacs conda mode](https://github.com/necaris/conda.el), in `~/mambaforge/etc/profile.d/mamba.sh`, change

```
    case "$cmd" in
        activate|deactivate)
```

to

```
    case "$cmd" in
        activate|deactivate|config)
```

#### minikube

1. Add the autocompletion setup to the zsh profile. First ensure that the directory `~/.oh-my-zsh/completions` exists (otherwise create it), then run `minikube completion zsh > ~/.oh-my-zsh/completions/_minikube`.

#### kubectl

1. Add the autocompletion setup to the zsh profile. First ensure that the directory `~/.oh-my-zsh/completions` exists (otherwise create it), then run `kubectl completion zsh > ~/.oh-my-zsh/completions/_kubectl`.

#### Others

First, use the following command to print the completion folders ([credits to Andrej Friesen](https://www.ajfriesen.com/github-cli-auto-completion-with-oh-my-zsh/)):

```bash
print -l $fpath | grep completion
```

which should output something like:

```
/home/<user>/.oh-my-zsh/custom/plugins/<some-plugin>
/home/<user>/.oh-my-zsh/completions
/usr/share/zsh/vendor-completions
```

Then, **after** the line with `source $ZSH/oh-my-zsh.sh` add the following lines to the `.zshrc` file:

```
# >>> completion (conda, github cli, minikube, kubectl...) >>>
autoload -U compinit
compinit -i
# <<< completion (conda, github cli, minikube, kubectl...) <<<
```

## 6. Emacs

1. Clone [the emacs setup](https://github.com/martibosch/.emacs.d), i.e., `git clone git@github.com:martibosch/.emacs.d.git` to the user's home directory.
2. Follow the installation steps in the `README.md` file.

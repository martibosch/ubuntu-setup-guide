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
4. Configure [the git user name and email](https://git-scm.com/book/en/v2/Getting-Started-First-Time-Git-Setup).
5. Add [signing key to git](https://docs.github.com/en/authentication/managing-commit-signature-verification/telling-git-about-your-signing-key), i.e., `gpg --list-keys --keyid-format=long` and `git config --global user.signingkey <key-id-after-rsa4096>`.
6. Configure git to sign all commits, i.e., `git config --global commit.gpgsign true`.

## 4. Docker

https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-ubuntu-20-04

## 5. Mamba

1. Install [Mambaforge](https://github.com/conda-forge/miniforge#mambaforge).
2. Initialize the shell by running `mamba init` (ensure that the command modifies `.zshrc`, otherwise run `mamba init zsh`).

### Zsh tab-completion

1. Clone the [esc/conda-zsh-completion repository](https://github.com/esc/conda-zsh-completion) at `$ZSH_CUSTOM/plugins/`, i.e.: `git clone https://github.com/esc/conda-zsh-completion ${ZSH_CUSTOM:=~/.oh-my-zsh/custom}/plugins/conda-zsh-completion`
2. Modify the `plugins` line of `.zshrc` and add `conda-zsh-completion`, e.g., `plugins=(git conda-zsh-completion)`
3. Activate the autocompletion in `.zshrc` by the adding (ACHTUNG: the snippet below must go **after** the line with `source $ZSH/oh-my-zsh.sh`):

    ```
    # >>> conda completion >>>
    autoload -U compinit && compinit
    # <<< conda completion <<<
    ```

### Software to install in the base environment

1. Install the packages from the `environment.yml` file, i.e., from the root of this repository, and within the base environment, run `mamba env update -f environment.yml`.
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

#### Snakemake
    
1. Update the `.zshrc` file to activate autocompletion by adding:

    ```
    # >>> snakemake autocompletion for zsh >>>
    compdef _gnu_generic snakemake
    # <<< snakemake autocompletion for zsh <<<
    ```
    
#### Terraform

1. Add the autocompletion setup to the zsh profile by running `terraform -install-autocomplete`

#### AWS CLI

1. Configure the named profiles using the command `aws configure --profile <profile-name>`.

## 6. Emacs

1. Clone [the emacs setup](https://github.com/martibosch/.emacs.d), i.e., `git clone git@github.com:martibosch/.emacs.d.git` to the user's home directory.
2. Follow the installation steps in the `README.md` file.

# Git Config
## Installing Git
The first step towards advancing your development is installing a version control solution, in which the industry standard is **Git**. The recommended Git development process will be detailed in a future markdown file. For now, we will focus on configuring the software.

### Downloading Git
The setup can be downloaded from [here](https://git-scm.com/downloads). This guide assumes you are using the standalone installer for Windows, and is currently based on version 2.39.2.

If you already have Git installed, you can just run the installer again to reconfigure the below settings if you'd like. If you are working on one of my projects and already have Git installed, don't necessarily concern yourself so much with this.

### Git Installation Guide
It is recommended to check all of the boxes on the 'Select Components' screen.

Choose your preferred default editor - [Notepad++](https://notepad-plus-plus.org/) or [VS Code](https://code.visualstudio.com/) are some good, lightweight options with excellent features if you don't have a preferred editor already. Windows' built-in text editors like Notepad or Wordpad are not recommended.

It is recommended to override the default branch name for new repositories to "**main**", as this is set to be the standard in the near future.

For the remaining options, select the following:
- Adjusting your PATH environment: **Git from the command line and also from 3rd-party software**
- Choosing the SSH executable: **Use bundled OpenSSH**
- Choosing HTTPS transport backend: **Use the native Windows Secure Channel library**
    - Has all of the features that OpenSSL offers and then some.
- Configuring the line ending conversions: **Checkout Windows-style, commit Unix-style line endings**
    - The most important thing here is that you commit Unix-style line endings. There is some argument for checkout as-is, even on Windows. This option ensures better Windows compatibility, but the files you're checking out are technically different from what is there remotely. One way or another, there is some rare incompatibility potential.
- Choosing the terminal emulator to use with Git Bash: **Use Windows' default console window**
    - Windows Terminal has replaced the old Command Prompt at this point, and now sports much better features than MinTTY.
- Choose the default behavior of `git pull`: **Rebase**
    - The truth is, you should forget this command even exists. You should also never instruct anyone to use it (without any arguments) as it's default behavior for each user is never guaranteed. However, if you do use it for some reason, rebasing is usually recommended over merging. Git commands such as this will be discussed in a future markdown file.
- Choose a credential helper: **Git Credential Manager**
- Configuring extra options: **Enable file system caching** and disable symbolic links.
- Configuring experimental options: **Enable experimental built-in file system monitor** if you'd like, though this is an experimental feature

Note that, despite these settings, there are project-specific settings that can override some of them (e.g. line ending conversions), such as via a `.gitattributes` file. This is because maintainers can't expect contributers to always have Git configured exactly as they'd like it. This will be touched on in a future markdown file.

## Configuring Git
### Basic Configuration
Next, open Git Bash. Set the username you would like associated with your commits, as well as an email (the one you will use for GitHub, GitLab, Azure DevOps, etc.) with the following commands:

- `git config --global user.name "Your Name Here"`
- `git config --global user.email "xxxxxxxxxx@xxxxx.xxx"`

### Generating a GPG Key
Next, I am going to recommend setting up something called **GPG**. This will let you sign your commits to verify that they are indeed yours. Why? Because anyone can technically set their Git username and email to yours, and it will appear as though you published commits that you are not responsible for. By setting this up, you will get a neat "Verified" badge next to your verified commits, and an "Unverified" badge next to any unverified ones in GitHub.

In the terminal, type `gpg --version` to see if you have GPG installed. If not, install it via the latest version of [Gpg4win](https://gpg4win.org/index.html). Only the first, required component is necessary (**GnuPG**).

After you've installed it, reopen the terminal and input `gpg --full-gen-key`. After that, input the following as it prompts you:
1. Please select what kind of key you want: `1` (RSA and RSA)
2. What keysize do you want?: `4096`
3. Please specify how long the key should be valid: `0` (key does not expire)
4. Is this correct?: `y`
5. Real name: `Your Name Here` (same as above)
6. Email address: `xxxxxxxxxx@xxxxx.xxx` (same as above)
7. Comment: `<empty, or whatever you'd like>`
8. Change (N)ame, (C)omment, (E)mail, or (O)kay/(Q)uit?: `O`

**IMPORTANT:** Immediately after completing the next step, start frantically inputing into your keyboard and mouse. It will tell you to do so in the terminal. It may seem ridiculous, but this will further randomize your key.

A window should have popped up at this point asking you to enter a passphrase. Use your best password practices, and make sure you save it or remember it, as you will need to reenter it occasionally in the future. Right after, do that important step I mentioned above. You have successfully generated your key.

### Backing Up Your Key
Next, enter `gpg --list-keys`. Enter the following commands, wherein your 'fingerprint' is the 40 character string in the key listing.
- `gpg --export --armor <fingerprint> > ./gpg-key.pub`
- `gpg --export-secret-keys --armor <fingerprint> > ./gpg-key.asc`

These can be found in your user directory. Appropriately backup `gpg-key.asc` to multiple locations (NOT `gpg-key.pub`).

### Setting Up GPG (Git)
In Git Bash, enter the following commands:

- `git config --global commit.gpgsign true`
- `git config --global user.signingkey <fingerprint>`

This will set up Git to sign your commits with your GPG key.

### Setting Up GPG (GitHub)
Move over to GitHub, and navigate to your [SSH and GPG keys](https://github.com/settings/keys) section. Click "New GPG key", and paste the contents of the previously generated `gpg-key.pub` into the Key field. Choose whatever title you'd like, and save.

In the same section, check off **Flag unsigned commits as unverified** under the 'Vigilant mode' section.

### Setting Up GPG (GitLab)
Move over to GitLab, and navigate to your [GPG Keys](https://gitlab.com/-/profile/gpg_keys) section. Paste the contents of the previously generated `gpg-key.pub` into the Key field, and save.

### Addendum: Deleting GPG Keys
If you need to delete this key for any reason in the future, do the following:

- `gpg --delete-secret-key <Real name>` (same as above)
- `gpg --delete-key <Real name>` (same as above)

Then, navigate to `%AppData%\gnupg\openpgp-revocs.d\` and delete the `.rev` file with the matching fingerprint.

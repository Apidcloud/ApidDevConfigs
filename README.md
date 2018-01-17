# Apidcloud Development Configurations
Personal Development Configurations - Shells, etc.

For now, this is a step-by-step guide to configure oh-my-zsh in Windows and allow it to be used inside editors at will (e.g., VS Code, WebStorm)

![WithinVSCodeAndGit](https://imgur.com/nF5VHod.png)

# Basic Setup
1. Install [Cygwin](https://www.cygwin.com/)
2. When running the above setup, select the desired packages for download, including:
    - zsh (required)
    - wget (required)
    - git (required)
    - lynx (required for apt-cyg - similar to apt-get)
    - nano and/or vim editors (not required)
    - ssh (not required)
3. Open Cygwin bash and run:
    ```sh
    $ mkpasswd -l -p "$(cygpath -H)" > /etc/passwd
    ```
4. Open `/etc/passwd` with a text editor. It should be inside the installation path of Cygwin (e.g., `C:\cygwin64`)
5. Replace `/bin/bash` at the end of the users we want for `/bin/zsh`
6. Close the terminal and open again. **It should now be using zsh instead of bash**.
7. Install oh-my-zsh 
    ```sh
    $ sh -c "$(wget https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O -)"
    ```

# Configure Theme
1. Color theme - e.g., **Solarized Dark**:
    ```sh
    $ git clone https://github.com/karlin/mintty-colors-solarized.git ~/.solarized
    $ echo 'source ~/.solarized/mintty-solarized-dark.sh' >> ~/.zshrc # for light, replace dark with light
    ```
2. Powerline (if you want powerline fonts with fancy symbols and additional customization) - e.g., **Powerlevel9k**:
    ```sh
    $ git clone https://github.com/bhilburn/powerlevel9k.git ~/.oh-my-zsh/custom/themes/powerlevel9k
    ```
    Then edit your `~/.zshrc` and set `ZSH_THEME="powerlevel9k/powerlevel9k"`

# Configure Font
1. Download Powerline fonts - e.g., the following [repository](https://github.com/powerline/fonts)
2. Install a font you'd like (e.g., Meslo LG M for Powerline). Keep in mind that **to use Powerlevel9k, a powerline-patched font is needed**.
3. To effectively use it in the terminal, you can **right click on the terminal header » options » fonts** and **select it**. 
    *If*, however, *it doesn't show up in that list*, run the following command to **change it manually**:
    ```sh
    $ C:\cygwin64\bin\mintty.exe -i /Cygwin-Terminal.ico -o Font="Meslo LG L for Powerline" -
    ```
4. Test if it's working correctly by running
    ```sh
    $ echo "\ue0b0 \u00b1 \ue0a0 \u27a6 \u2718 \u26a1 \u2699"
    ```
5. It should now be able to render the characters from the echo command
    Note that to load the font correctly, **you might need** to **run that command everytime**.
    To do so, you can start a zsh shell on demand, and add that batch script (.bat extension) to your path or start menu. 
    As an alternative, you can install **ConEmu** and use it as default shell (startup task) command - see ConEmu section.

# 256 colors Warning:

If the following message shows up, which it should:
```sh
WARNING! Your terminal appears to support less than 256 colors!
If your terminal supports 256 colors, please export the appropriate environment variable
_before_ loading this theme in your ~/.zshrc. In most terminal emulators, putting
export TERM="xterm-256color" at the top of your ~/.zshrc is sufficient.
```
As the warning suggests, you can edit the file and copy paste the export command manually. 
You can also accomplish the same thing by going to **Cygwin options » terminal » type** and **change to xterm-256color**

# Editor integration

**This section could particularly use the help of some contributions, as each editor handles these configurations differently.**

In order to get the terminal and shell to work within an editor, such as VS Code, there are two things left:
1. Create a new file with the name **editorTerminalScript.bat** and add the following to it:
    ```sh
    @echo off
    set INTELLIJ=true
    set CHERE_INVOKING=1
    set TERM=xterm-256color
    set PATH="C:\cygwin64\bin";%PATH%

    C:\cygwin64\bin\zsh.exe -l -i
    ```
2. Now all you need is to tell the editor to use this script as the terminal, and to change its font. Note that **there isn't a variable *Font*** to set through the script (as in the other one). Maybe there's an alternative to **zsh** or **env.exe** which I don't know yet about.

### VS Code

In VS Code, the above can be accomplished by going to the **User Settings** (from the cogwheel at lower left corner) and add the following configurations:

```sh
"terminal.integrated.shell.windows": "C:\\FolderWhere\\EditorTerminalScriptIsLocated\\editorTerminalScript.bat",
"terminal.integrated.fontFamily": "Meslo LG M for Powerline",
"terminal.integrated.fontSize": 14
```

Saving the file and reopening the editor should make it work.

# Apt Cyg

In order to install Cygwin's packages through the terminal instead of running the setup every single time you need one, you can install **apt-cyg**.
As a requirement, you'll need to install `lynx` through the cygwin setup first. After that, just run the following commands:
```sh
$ lynx -source rawgit.com/transcode-open/apt-cyg/master/apt-cyg > apt-cyg
$ install apt-cyg /bin
```
# Plugins

### Syntax highlightning

1. Clone its repository to custom zsh plugins:
    ```sh
    $ git clone https://github.com/zsh-users/zsh-syntax-highlighting.git $ZSH_CUSTOM/plugins/zsh-syntax-highlighting
    $ echo "source $ZSH_CUSTOM/plugins/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh" >> ${ZDOTDIR:-$HOME}/.zshrc
    ```
2. You can check if it has worked properly by opening `.zshrc` file. It should have something equivalent to the following at the end:
    ```sh
    $ source (fullcygwinPathToHome)/.oh-my-zsh/custom/plugins/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh
    ```

### Auto Suggestions

1. Clone its repository to custom zsh plugins:
    ```sh
    $ git clone git://github.com/zsh-users/zsh-autosuggestions $ZSH_CUSTOM/plugins/zsh-autosuggestions
    ```

2. Add the plugin to the list of plugins for Oh-My-Zsh to load:
    ```sh
    plugins=(zsh-autosuggestions) # or plugins+= if you're using other plugins
    ```

3. Reopen the terminal and run the following command:
    ```sh
    ZSH_AUTOSUGGEST_HIGHLIGHT_STYLE='fg=6'
    ```
4. **If it shows suggestions as you type**, place the same command below the plugin line inside `.zshrc`
    **If it doesn't**, try other colour numbers (e.g., 5) until the suggestions are shown correctly and then copy to the file.


# Additional Customization

### Aliases

A few useful examples, which you can add to your `.zshrc` file:
```sh
alias pbcopy="cat >/dev/clipboard"
alias pbpaste="cat /dev/clipboard"
alias work="cd D:/Workspace"
```
### Powerline9k

My personal configuration. You can also add icons to home, etc.

```sh
ZSH_THEME="powerlevel9k/powerlevel9k"

POWERLEVEL9K_TIME_FORMAT="%D{%H:%M}"

POWERLEVEL9K_PROMPT_ON_NEWLINE=true
POWERLEVEL9K_RPROMPT_ON_NEWLINE=false

POWERLEVEL9K_COMMAND_EXECUTION_TIME_THRESHOLD=1

POWERLEVEL9K_LEFT_PROMPT_ELEMENTS=(dir vcs)
POWERLEVEL9K_RIGHT_PROMPT_ELEMENTS=(status command_execution_time time)

POWERLEVEL9K_SHORTEN_STRATEGY="truncate_from_right"
POWERLEVEL9K_SHORTEN_DELIMITER=""
POWERLEVEL9K_SHORTEN_DIR_LENGTH=3
```
### ConEmu

You can also use [ConEmu](https://conemu.github.io/) terminal. It allows further customization, such as tabs, explorer integration (e.g., context menus)

Use the following command in the ConEmu task:
```sh
set CHERE_INVOKING=1 & C:\cygwin64\bin\mintty.exe -i /Cygwin-Terminal.ico -o Font="Meslo LG L for Powerline" -
```

In order to get it working inside VS Code and other editors, use the following batch script instead:
```sh
C:\cygwin64\bin\zsh.exe -l -i
```

If you're using the explorer integration, but it's not opening the shell in the working directory, place the following  inside .zshrc file in C:\Users\username:
```sh
if [ -n "${ConEmuWorkDir}" ]; then
  cd "$ConEmuWorkDir"
fi
```

# Final Result

![Default](https://i.imgur.com/ToCOcAl.png)

![CustomizedPluginSyntaxHighlightning](https://i.imgur.com/QRzDdNv.png)

![CustomizedWithPlugins](https://i.imgur.com/d6lJuHm.png)

![WithinVSCode](https://imgur.com/OjVwgOI.png)

# Useful resources
- https://gist.github.com/aviaryan/5a57aa25b140f6c48f0e
- https://gist.github.com/kevin-smets/8568070

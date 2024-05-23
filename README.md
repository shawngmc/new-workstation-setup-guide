# new-workstation-setup-guide

# Font - Roboto Mono for Powerline
Using the [CodingFont Tournament Bracket](https://www.codingfont.com/), I found I like Roboto Mono: More readable, slashed zeroes, sans-serif, good contrast
1. Install the powerline fonts shim, which alone makes powerline always render properly
    - Fedora 40: ```sudo yum install powerline-fonts -y```
    - EL7/8/9: ```sudo yum install powerline-fonts -y```
    - Debian/Ubuntu/etc.: ```sudo apt-get install fonts-powerline -y```
2. Install the patched powerline fonts, since I like a couple specific options
```
git clone https://github.com/powerline/fonts.git --depth=1
cd fonts
./install.sh
cd ..
rm -rf fonts
```

# Terminal - Guake
1. Install Guake
    - Fedora 40: ```sudo yum install guake -y```
    - EL7/8/9: ```sudo yum install guake -y```
    - Debian/Ubuntu/etc.: ```sudo apt-get install guake -y```
2. Set up Super-D Keybind
    - X: Use the preferences Key Bindings tab
    - Wayland-Gnome: Settings > Keyboard > Key Bindings > Custom > Add... Super-D to ```guake-toggle```
3. Other settings
    - General > Start Guake at Login (ON)
    - Main Window > Hide on Lose Focus (OFF)
    - Main Window > Height (100%)
    - Appearance > Font (Fira Code Medium)

# Prompt - Powerline w/ K8s Support
1. Install conda
```
mkdir -p ~/miniconda3
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh -O ~/miniconda3/miniconda.sh
bash ~/miniconda3/miniconda.sh -b -u -p ~/miniconda3
rm -rf ~/miniconda3/miniconda.sh
```
2. Init the conda system for bash ```~/miniconda3/bin/conda init bashs```
3. activate conda env - using 'default' here
4. python3 -m pip install powerline-status powerline-kubernetes
5. in ~/.bashrc
```conda activate default
POWERLINE_PATH=$(python3 -m pip show powerline-status | grep "Location:" | sed "s/:Location: //")/powerline
if [ -f ${POWERLINE_PATH}/bindings/bash/powerline.sh ]; then
  powerline-daemon -q
  POWERLINE_BASH_CONTINUATION=1
  POWERLINE_BASH_SELECT=1
  PROMPT_COMMAND=""
  source ${POWERLINE_PATH}/bindings/bash/powerline.sh
fi
```
6 Copy the default configs to homedir
```
mkdir -pv ~/.config/powerline/colorschemes  
mkdir -pv ~/.config/powerline/themes/shell
POWERLINE_PATH=$(python3 -m pip show powerline-status | grep "Location:" | sed "s/Location: //")/powerline
cp ${POWERLINE_PATH}/config_files/colorschemes/default.json ~/.config/powerline/colorschemes/
cp ${POWERLINE_PATH}/config_files/colorschemes/solarized.json ~/.config/powerline/colorschemes/
cp ${POWERLINE_PATH}/config_files/themes/shell/default.json ~/.config/powerline/themes/shell/
```
7. Add to the groups block in ~/.config/powerline/themes/shell/default.json
```
    "kubernetes_cluster":         { "fg": "gray10", "bg": "darkestblue", "attrs": [] },
    "kubernetes_cluster:alert":   { "fg": "gray10", "bg": "darkestred",  "attrs": [] },
    "kubernetes_namespace":       { "fg": "gray10", "bg": "darkestblue", "attrs": [] },
    "kubernetes_namespace:alert": { "fg": "gray10", "bg": "darkred",     "attrs": [] },
    "kubernetes:divider":         { "fg": "gray4",  "bg": "darkestblue", "attrs": [] },
```
8. Add to the left segments in ~/.config/powerline/colorschemes/default.json
```
{
    "function": "powerline_kubernetes.kubernetes",
    "priority": 30,
    "args": {
        "show_kube_logo": true, // set to false to omit the Kube logo
        "show_cluster": true, // show cluster name
        "show_namespace": true, // show namespace name
        "show_default_namespace": false, // do not show namespace name if it's "default"
        "alerts": [
          "live", // show line in different color when namespace matches
          "cluster:live"  // show line in different color when cluster name and namespace match
        ]
    }
}
```
9. Restart the daemon
```
powerline-daemon --replace
```

Debug with:
powerline-daemon --replace --foreground


https://github.com/so0k/powerline-kubernetes
https://stackoverflow.com/questions/58482081/configure-powerline-to-display-git-status

# CLI Copy/Paste
## If on XOrg or Wayland w/XWayland (test via ```xlsclients -l```; this might need installed!)
1. Install Xsel: ```sudo yum install xsel -y```
2. Add the following aliases:
```
touch ~/.bash_aliases
echo "alias pbcopy='xsel --input --clipboard'" >> ~/.bash_aliases
echo "alias pbpaste='xsel --output --clipboard'" >> ~/.bash_aliases
```
3. After loading aliases, use the following commands format:
```
# To copy, pipe in:
echo "Hi!" | pbcopy
# To paste to a file:
pbpaste > file.txt
```

## If on Wayland w/o Xwayland
The [wl-clipboard](https://github.com/bugaevc/wl-clipboard) package is roughly equivalent.

# CLI Data Fetcher - Fastfetch
1. Install Fastfetch: ```sudo yum install -y fastfetch```
2. Add to ~/.bashrc
```
# Run fastfetch if present
FASTFETCH_PATH=$(which fastfetch)
if [ $? -eq 0 ]; then
  fastfetch
fi
```
3. Generate a config file with a few less enabled modules:
```
fastfetch --structure Title:Separator:OS:Host:Kernel:Uptime:Packages:Shell:Display:DE:WM:Terminal:TerminalFont:CPU:GPU:Memory:Swap:Disk:LocalIp:Battery:PowerAdapter:Locale:Break:Colors --gen-config
```


# Remote Desktop - Remmina
1. Install Remmina AND the RDP Auth plugin (supports MS accounts)
```sudo yum install -y remmina remmina-plugins-rdp```
2. When setting up the connection to a personal dekstop:
   - Choose a domain of MicrosoftAccount
   - Choose a resolution of 1920x1080; after connecting, enable Dynamic Resolution

# VSCode
1. Install VSCode
2. Apply the settings.json in this repo by copying to ~/.config/Code/User/settings.json; note that the comments are OK, as VSCode technically uses JSONC :)

# lsfg-auto

`lsfg-auto` is a wrapper script for SteamOS gaming handhelds that dynamically applies LSFG based on your device's power status.
  
Instead of manually toggling framegen and FPS caps every time you dock or plug your handheld into AC, `lsfg-auto` does it for you in the background.  

This is useful for all those games where you may want a certain FPS cap when playing handheld (say, 45 fps on your Steam Deck OLED) to save power, whereas you wouldn't care about it when docked, since the device has now access to direct electricity and can manage more fps on its own.

Some use cases:
- On your steam deck OLED, target 90fps while handheld (45fps with a 2x multiplier in LSFG), and then have it disabled automatically when plugged into your dock in order to get 60fps native, without a care for battery consumption.

- Get a game running at 120fps, with 40fps as base and a 3x LSFG multiplier, to take advantage of your high refresh rate screen both when handheld and while charging, then squeeze the extra performance to get 60fps native on your bigger living room TV that is 60hz only.

- Play a really heavy game at 60fps, with a 30fps base and a 2x multiplier, in order to keep playing for hours without running out of battery, then unleash your device with a higher TDP when docked, in order to easily reach the 60fps mark on its own.

- Or if it's not enough, just leave the multiplier on even when docked.

If used alongside SimpleDeckyTDP or other TDP management programs with power profiles dependent on AC status, this script can provide an even more seamless experience.

## How it Works

The script wraps your game's launch command and continuously polls your device's hardware state for changes (specifically: battery, charging, docked to an external display).

Based on the active power state and your preferred mode, it will:

1. **Toggle Frame Generation**: Modifies your `lsfg-vk` configuration (for the selected profile) to automatically enable, change or disable the frame multiplier. By using the same config file that lsfg-vk Decky does, manual changes in the plugin will still reflect in game, when using lsfg-auto.
2. **Adjust FPS Limits**: Dynamically overrides your MangoHud configuration to cap, uncap or match the refresh rate of an external display when docked, and restore your (presumably lower) FPS cap when back on battery.

## Requirements

This script has been developed for use on SteamOS, so most dependencies should be already available on systems running it, and some other are installed in the python virtual environment. 

Generally, you should only need to install LSFG and its decky plugin. Nevertheless, here is the complete list:

- [lossless scaling](https://store.steampowered.com/app/993090/Lossless_Scaling)
- [lsfg-vk decky](https://github.com/xXJSONDeruloXx/decky-lsfg-vk)
- [python3-venv](https://docs.python.org/3/library/venv.html)
- [simple decky tdp](https://github.com/aarron-lee/SimpleDeckyTDP) (optional, you can use it to set different TDP profiles when docked)
- [zenity](https://linux.die.net/man/1/zenity) (optional, for GUI error messages visible from gaming mode)
- [xrandr](https://x.org/releases/X11R7.5/doc/man/man1/xrandr.1.html) (optional, for automatic external monitor FPS cap)


## Installation

The installation method is fairly straight forward, both if you decide to use the automatic method or if done manually.

### Automatic

Paste this command into your terminal, press enter and the installation will begin.

```sh
curl -fsSL https://raw.githubusercontent.com/rrikiliba/lsfg-auto/main/install | sh
```

Of course, in order to follow best practices, I advise you to read the [installation script](install) yourself before executing it.

### Manual

Open a terminal and use the following commands in order to clone the repo in the correct location and prepare the python virtual environment. 

```sh
# place yourself in your home directory
cd ~

# clone this repository
git clone https://github.com/rrikiliba/lsfg-auto.git

# move into the main folder
cd lsfg-auto

# create the python virtual environment
python3 -m venv .venv

# install dependencies into the newly created environment
.venv/bin/pip3 install -r requirements.txt

# done!

```

## Usage

First of all create a named profile in lsfg-vk decky, by accessing the plugin in the QAM. Configure everything as you like, but pay special attention to multiplier and fps cap.

These settings will be actively used by the lsfg-auto script, and thus you shouldn't worry if you see them changing "on their own" as you run your games. the base fps cap, specifically, will appear disabled in lsfg-vk decky, because of how it is handled, while the active profile will be switched automatically, and the multiplier turned on or off depending on your configuration. 

Then, simply place this script, with the arguments you desire, before your steam game `%command%` in the game's launch options. 

The arguments for `lsfg-auto` allow you to customize the behaviour of the script. They can be inspected at any moment by running:

```sh
~/lsfg-auto/lsfg --help
```

The following is simply the output of this command. 

```
usage: ~/lsfg-auto/lsfg [-h] [--lsfg-path LSFG_PATH] [--config-path CONFIG_PATH]
                        [--docked-fps DOCKED_FPS] [--docked-mult DOCKED_MULT]
                        [--mode {battery,charging,docked}]
                        profile ...

lsfg-auto: dynamically set base FPS and LSFG multiplier based on device power status.

positional arguments:
  profile               The lsfg-vk profile to use, as configured in the Decky plugin
                        settings. Might be per-game, global, or anything in between.
  command               The game command.

options:
  -h, --help            show this help message and exit
  --lsfg-path LSFG_PATH
                        Specify a different lsfg script file path than the one in ~/lsfg
  --config-path CONFIG_PATH
                        Specify a different lsfg-vk config file path than the one in
                        ~/.config/lsfg-vk/conf.toml
  --docked-fps DOCKED_FPS
                        Override FPS cap when docked. Set to 0 to disable. (default: auto-
                        detect refresh rate)
  --docked-mult DOCKED_MULT
                        Override FPS mult when docked (default: 1)
  --mode {battery,charging,docked}
                        Highest power mode in which LSFG will be active (default: battery)
```

as you can see, the only required argument is the name of the lsfg-vk decky profile to use for your game.

Once you run your game through lsfg-auto, you are free to modify any parameter in lsfg-vk decky (if you want to play around with flow scale or presentation mode, for example), but know that multiplier and base fps will be overwritten with the values they held before the game started, once you actually exit the game.

## Disclaimers

1. I am in no way affiliated with the developers of Lossless Scaling, lsfg-vk or its Decky plugin. 
1. While I am happy to share what I created with the community, this software was initially created for personal use, and thus comes with no guarrantee. If you notice anything wrong or unusual in the code feel free to let me know, i will try my best to fix it. 
1. Also, while this script is absolutely not "vibe coded", I did employ AI to write some parts of the code. I have experience in software development, Linux, Steam and SteamOS's ecosystem, and I thouroughly reviewed (and most times corrected) each AI generated addition, but both LLMs and humans can make mistakes, and knowing the strong opinions sometimes manifested in software development communities, I thought of putting this disclaimer here.

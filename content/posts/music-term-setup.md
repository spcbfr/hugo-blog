+++
title = "My terminal-based music setup"
description = "The proper way to listen to music"
date = 2021-11-12
tags = ["music", "commandline", "linux"]
draft = false
author = "Youssef Bouzekri"
+++

## Introduction {#introduction}

despite having some of the worst names ever, ncmpcpp, mpc and mpd make the best music setup on linux in my opinion, let's go through their pros and cons first

**Pros**

-   terminal based
-   free and open source
-   familiar user interface
-   absolutely no ads
-   built-in Lyrics fetcher

**Cons**

-   Offline, meaning you will have to download everything you want to listen to
-   Can be a hastle to configure when you are getting started


## the difference between mpd and ncmpcpp and mpc {#the-difference-between-mpd-and-ncmpcpp-and-mpc}

These tools work together to achieve a good music system through a server-client relationship. in this case **mpd is the server** while **Mpc and ncmpcpp are the clients**. you can have the server running in the background controled by your init system and control it through one of the clients, that means that you can close your client without the music stopping since that mpd, the server, is still running.

now the one question left is, since mpc and ncmpcpp are both clients what is the difference between them and why do most people use both at the same time?
mpc is command based, meaning that you type the commands in the terminal to control it (like `mpc pause`, `mpc next` and `mpc toggle` just to list some examples.) where ncmpcpp has a terminal graphical interface that looks like this

{{< figure src="/img/ncmpcpp.png" alt="Headings Example" position="center" style="border-radius: 8px;" caption="Figure 1: ncmpcpp's example interface" >}}
why do we use both then? well, because most people bind the most common mpc commands to keyboard shortcuts through their window manager or desktop environment ! I'll look at how to bind mpc keys later in the blog post


## installing and configuring mpd {#installing-and-configuring-mpd}

we will first start by installing mpd, which is as we said the server that manages your music collection. it is in the main repositories for most distros. on arch you can install it through pacman

```shell
sudo pacman -S mpd
```

mpd doesnt create a configuration file by default, so go ahaid and create an `mpd` directory in your config dir (usually in `~/.config`).
next create a file in that directory named mpd.conf, here is my configuration

```toml
music_directory "/home/youssef/music/"
playlist_directory "/home/youssef/.config/mpd/playlists"
db_file "/home/youssef/.config/mpd/mpd.db"
log_file "/home/youssef/.config/mpd/mpd.log"
pid_file "/home/youssef/.config/mpd/mpd.pid"
state_file "/home/youssef/.config/mpd/mpdstate"
audio_output {
	type "pulse"
	name "pulse audio"
}

bind_to_address "127.0.0.1"
port "6601"
```

let's look at the most important parts:

1.  **music\_directory** you need to set this to your actual music folders. if this is not set up, mpd will use the the default XDG directory which is `~/Music` make sure to replace \`youssef\` with your actual username
2.  **port** the default port is 6600 but that is used for something else on my system, so I'll have to use port 6601, if this is the case for you too, make sure to have this line in your config
3.  **playlist\_directory** this important if you want playlists enabled, if you want this, the direcotry should already exist

finally you will want to start mpd, if you are on a systemd distro you can use **systemctl**

```shell
sudo systemctl start mpd # starting mpd for the current session
sudo systemctl enable mpd # run this if you want ot start mpd on startup
```


## Installing and configuring the clients {#installing-and-configuring-the-clients}

next, we will need to setup the two clients **mpc** and **ncmpcpp**, both of the clients can be found in the default repos for most distros, since I am on arch I'll use pacman but apt or dnf will work fine

```shell
sudo pacman -S mpc ncmpcpp
```

now, let's configure ncmpcpp, go ahead and create a configuration dir in `~/.config/ncmpcpp/` we will need two files:

1.  **bindings** for keyboard bindings
2.  **config** for other general configuration (look and feel, functionality, etc..)

since ncmpcpp doesnt come with vim bindings out of the box, I was always looking for a set of vim bindings for the client (because I am two lazy to make one myself :)) I've recently found [this](https://gitlab.com/LukeSmithxyz/voidrice/-/blob/master/.config/ncmpcpp/bindings) set of vim-inspired keybindings done by luke smith as part of the larbs bootstraping script so that is what I am using for my keybindings. and here is my main configuration file

```toml
# general
autocenter_mode = "yes"
follow_now_playing_lyrics = "yes"
ignore_leading_the = "yes"
ignore_diacritics = "yes"

# seeking
incremental_seeking = "yes"
seek_time = "1"

# display
playlist_editor_display_mode = "columns"
search_engine_display_mode = "columns"
browser_display_mode = "columns"
playlist_display_mode = "columns"

# songlist
song_columns_list_format = "(10)[blue]{l} (30)[green]{t} (30)[magenta]{a} (30)[yellow]{b}"
song_list_format = "{$7%a - $9}{$5%t$9}|{$5%f$9}$R{$6%b $9}{$3%l$9}"

# colors
colors_enabled = "yes"
empty_tag_color = magenta
main_window_color = white
progressbar_color = black:b
progressbar_elapsed_color = blue:b
statusbar_color = red
statusbar_time_color = cyan:b

# progress
progressbar_look = "=>-"

# other
system_encoding = "utf-8"
regular_expressions = "extended"

[current item]
current_item_prefix = "$(blue)$r"
current_item_suffix = "$/r$(end)"
current_item_inactive_column_prefix = "$(cyan)$r"

# interface
user_interface = "alternative"
display_volume_level = yes
alternative_header_first_line_format = "$0$aqqu$/a {$6%a$9 - }{$3%t$9}|{$3%f$9} $0$atqq$/a$9"
alternative_header_second_line_format = "{{$4%b$9}{ [$8%y$9]}}|{$4%D$9}"

# navigation
cyclic_scrolling = "yes"
header_text_scrolling = "yes"
jump_to_now_playing_song_at_start = "yes"
lines_scrolled = "2"

# Now playing
now_playing_prefix = "> "
centered_cursor = "yes"

# misc
display_bitrate = "no"
enable_window_title = "yes"
empty_tag_marker = ""
```


## Bonus: setting up mpc keybindings in xmonad and a song module in xmobar {#bonus-setting-up-mpc-keybindings-in-xmonad-and-a-song-module-in-xmobar}


### mpc keybindings in xmonad {#mpc-keybindings-in-xmonad}

I am currently using the fantastic Window Manager Xmonad along with xmobar for the top bar. I have of course integrated my music setup into xmonad. you can add the following lines to your keyboard bindings variable to control mpc through the keybindings, you can also change the actual keybindings used

```haskell
-- ...
, ("<XF86AudioPlay>", spawn "mpc toggle")
, ("<XF86AudioStop>", spawn "mpc stop")
, ("<XF86AudioPrev>", spawn "mpc prev")
, ("<XF86AudioNext>", spawn "mpc next")
-- ...
```


### mpd module in xmobar {#mpd-module-in-xmobar}

Most people have song change notifications setup with mpd but for me, I prefer a simple module in my bar that shows the current playing song, for this I am using the following script. **make sure this script is exectuable and in your PATH**

```shell
#!/bin/bash

TCOL="#DFDFDF"    # The colour to be used to draw the song title when playing
ACOL="#9ca0a4"    # The colour to be used to draw the song artist when playing
PCOL="#73797e"    # The colour to be used to draw both the song title and artist when paused

MPDSTATE=$(mpc | sed -e '2 !d' -e 's/^.*\[//' -e 's/\].*$//')

if [ $MPDSTATE == "playing" ]; then
  # MPD is playing
  echo "<fc=$ACOL>$(mpc current | sed "s/ - /\<\/fc\> - \<fc=$TCOL>/")</fc>"
elif [ $MPDSTATE == "paused" ]; then
  # MPD is paused
  echo "<fc=$PCOL>$(mpc current)</fc>"
fi
```

and then I simply add this script to my xmobarrc

```haskell
, Run Com "xmobar-music" [] "music" 10
```

this module will look like this in action
{{< figure src="/img/xmobar-music.png" alt="Headings Example" position="center" style="border-radius: 8px;" caption="Figure 2: xmobar's music module " >}}

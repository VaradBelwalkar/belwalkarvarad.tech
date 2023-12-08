---
title: "My tmux configuration"
date: 2023-05-05T09:15:19+05:30
draft: false
ShowToc: false
ShowRelatedContent: true
summary: "Make your life easy using tmux :)"
tags: ["tmux","Configuration"]
categories: ["tmux","Configuration","Productivity"]
---


## Following is the tmux.conf file I normally use
```
# Bind Alt+j to move to the previous window
bind-key -n M-j select-window -p

# Bind Alt+l to move to the next window
bind-key -n M-l select-window -n

# Bind Alt+i to switch to the previous session
bind-key -n M-i switch-client -p

# Bind Alt+k to switch to the next session
bind-key -n M-k switch-client -n

# Bind Alt+c to create a new window
bind-key -n M-f new-window

# Bind Alt+r to rename the current window
bind-key -n M-r command-prompt -I "#W" "rename-window '%%'"

# Bind Alt+s to rename the current session
bind-key -n M-s command-prompt -I "#S" "rename-session '%%'"

# Bind Alt+d to detach the current session
bind-key -n M-d detach-client

# Create vertical pane (alt+v)
bind-key -n M-v split-window -h

# Create horizontal pane ( alt+h)
bind-key -n M-h split-window -v

# Go to left pane (goes to extreme right when at extreme left) (alt+ ,)
bind-key -n M-, select-pane -L

# Go to upper pane (goes to bottom when at top) (alt+ .)
bind-key -n M-. select-pane -U

# Swap current window with next window (alt+g)
bind-key -n M-g swap-window -t :+

# Swap current window with previous window ( alt+n)
bind-key -n M-n swap-window -t :-

# Run alt+q to clear the tmux history ( equivalent to tmux clear-history)
bind -n M-q send-keys -R Enter \; clear-history

# Enable mouse scrolling
set-option -g mouse on


setw -g mode-keys vi
```

### Simple and easy to learn key bindings for this tmux config.

| Description | Command |
|-----------|--------|
Create new window | `alt+f` |
Go to previous window |`alt+j` |
Go to next window | `alt+l` |
Detach current session | `alt+d` |
Rename current window | `alt+r` |
Rename current session | `alt+s` |
Go to next session | `alt+k` |
Go to previous session | `alt+i` |
Create horizontal pane | `alt+h` |
Create vertical pane | `alt+v` |
Go to left pane | `alt+,` |
Go to right pane | `alt+.` |
Go to upper pane | `alt+g` |
Go to below pane | `alt+n` |


### Copying Content:

- To initiate copying, use Ctrl + B as the starting command.
- Navigate to the content by pressing [ and utilize arrow keys for precise selection.
- Press SPACE to begin selecting the desired text.
- Upon completion, press ENTER to confirm the selection.

### Alternative Method:

- Scroll through the content using the mouse.
- Select the text by holding down the mouse click.
- Release the mouse click to return to the original prompt with the text now copied.

### Pasting Content:
- For pasting, press Ctrl + B to activate the tmux command.
- Press ] to paste the previously copied text.


### Copy to clipboard

This is the tmux buffer which you are using, i.e when  you run above commands, the data is copied to tmux buffer, to copy this content to your system's clipboard, just enter following command while in tmux session: 

```    
tmux show-buffer | xclip -selection clipboard 
```


## Using Tmux in Windows

To use tmux in windows, first install any distro of your choice on WSL, once installed, install tmux according to the distribution's docs,  
and after that, simply start it.

You may ask i want the tmux to be working in windows environment right?

Well it is pretty simple, simply run `powershell.exe` in the terminal and woh!  
Powershell with windows info just got up right,

now simply do `cd C:` and yup, you got into windows file system!

Now you can use tmux in windows as if you are running it in windows itself.




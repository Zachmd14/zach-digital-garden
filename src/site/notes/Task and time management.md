---
{"dg-publish":true,"permalink":"/task-and-time-management/"}
---

# Task and time management workflow workflow
![tmux-session-screenshot.jpg](/img/user/files/tmux-session-screenshot.jpg)
<sub>*Screenshot of my tmux session*</sub>

As of 2025-04-21, I recently discovered two apps for task and time managements : [TaskWarrior](https://taskwarrior.org/) and [TimeWarrior](https://timewarrior.net/).
> Fun fact : they're actually the reason I started writing this website !

They're both CLI based, I let you find out more on their respective website, here I'm going to focus on how I use them and how I did configure them.

## Tmux
First as you can see in the screenshot, I run all theses tools in a tmux session, that I run with a keybind  binded to a bash script int my `~/scripts/` directory.

Here is the script :
**task-time-manager.sh**
```bash
#!/bin/bash

SESSION_NAME="task-time"

# Check if the session already exists
if tmux has-session -t $SESSION_NAME 2>/dev/null; then
    echo "Session $SESSION_NAME already exists. Attaching to it."
    tmux attach-session -t $SESSION_NAME
else
	tmux new-session -d -s $SESSION_NAME
	tmux send-keys "~/scripts/task-refresh.sh" C-m
	tmux split-window -t 1 -v
	tmux split-window -t 1 -h
	tmux send-keys "~/scripts/time-refresh.sh" C-m
	tmux select-pane -t 3
	tmux split-pane -h
	# tmux resize-pane -x 39 -y 24
	tmux send-keys "~/scripts/task-burndow-refresh.sh" C-m
	tmux attach-session -t $SESSION_NAME
fi
```
Let me quickly explain what it does : first, it check if the session already exists. If it does, it attach to it. If not, it create a new session, run three scripts in differents tmux panes, and then create a last pane for you to be able to run commands.

As you saw, there are differents scripts running inside this script. Let's talk about them.

**task-refresh.sh**
```bash
#!/bin/bash

while true; do
	clear && ssh -t zach@xx.xxx.xxx.xxx -p xxxxx task rc._forcecolor=on
	sleep 5
done
```

**time-refresh.sh**
```bash
#!/bin/bash

while true; do
	clear && ssh -t zach@xx.xxx.xxx.xxx -p xxxxx timew summary :ids 
	sleep 5
done
```

**task-burndow-refresh.sh**
```bash
#!/bin/bash

while true; do
	clear && ssh -t zach@xx.xxx.xxx.xxx -p xxxxx task rc._forcecolor=on burndown.daily
	sleep 5
done
```
For now ignore the ssh side of the commands, I'll explain it later.

As you can see, these commands are pretty simple. They're simple running a command and then waiting 5 seconds before refreshing the screen. That's it.
Its really nice in tmux so the panes are constantly updated, and you can focus on writing the commands and seeing the effects on the differents tables and charts.

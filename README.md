# snake copy

snake-like 1-path pipeline copy (to utilize serial I/O bottleneck as much as possible)

snakecopy -i hosts.txt /full_path/to/local/target_dir

all nodes shall have snakecopy command before running.

## stream format

Line delimiter is \n (LF, 0x0A)
[BOS]
BUFSIZE
local_command_to_execute arg1 arg2 ...
ssh_commandline_to_connect_next_pipeline arg1 arg2 ...
HOST1
HOST2
HOST3
...
[empty line]
[binary data for stdin of the command]
[EOS]

## what it will do?

send the stream data and launch remote command. At the same time, it
bypass the stream (via ssh -A by default) to next host until it terminates



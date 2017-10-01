# snake copy

snake-like 1-path pipeline copy (to utilize serial I/O bottleneck as much as possible)

`snakecopy -i hosts.txt /full_path/to/local/target_dir`

all nodes shall have snakecopy command in the path before running.

## stream format

```
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
`

## hosts.txt

it shall contain target hosts in order to communicate (so locality matters!)

example)
```
172.16.1.1
172.16.1.2
172.16.1.3
```

## what it will do?

master makes tar archive of the given path and sends the content with a header to the first node given in `hosts.txt`.

slave will untar the archive. At the same time, the slave will pop the first node in the given header and send the stream to the header. This stream split is done in buffer level, so you don't need to wait until first slave to finish before sending content to the next node. If node list is empty, it just untar the content.

executed commands are defined in the `config` dictionary (hard-coded so far). slaves will receive the content so you don't need to re-copy the `snakecopy` command when you edit the config dictionary.

## security consideration

- it's quite easy to write old content over new directory.
- header contain the command to execute. If the content is compromised, you'll be messed. But any commands are executed as your authority over ssh, so you are responsible to see what will be executed anyway (wishlist: `--dry-run`).

## license: MIT

Copyright 2017 Yusuke DOI <yusuke.doi@gmail.com>

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

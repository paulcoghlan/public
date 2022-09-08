# Scripting Examples

- See also [./bash.md]
- End long-running commands with `; date` to find time completed
- Super-simple script using function:

```sh
#!/bin/bash

do_something() {
}

do_something
```

## Text Processing

### awk

`awk -F '<delim>' {printf $col}`

Extract fields:
`awk -F '~' '{printf $1; printf " "; print $12}' E-London.txt`

Count Accesses from Apache Log file:
`cat access_log | awk '{print $1}' | sort -n | uniq -c | sort -n | tail -n 10`

Unique hosts accessing site:
`cat /var/log/apache2/access.log | awk '{print $1}' | sort -n | uniq`

Convert spaces into commas (CSV):
`cat file.log | awk '{$1=$1}1' OFS=","`

Print lines where col doesn't match:
`cat staging-us-central1.log | awk '($7 != "0")'`

### bat

Install bat from [https://github.com/sharkdp/bat]

Output Markdown Files using bat:
`bat <markdown file>`

### column

Show CSV file: `column -s, -t < somefile.csv | less -#2 -N -S`

Use columnar output:
`mount | column -t`
`mount | column -t -s:`

### comm

Disjoint of 2 files:
`comm -13 <file1> <file2>`
`comm -23 <file1> <file2>`

### grep

Grep with lines before/after:
`grep -B <n>`
`grep -A <n>`

### parallels

Parallels example: run curl 20 times, parallelism of 20:

```sh
seq 20 | parallel -n0 -j20 'curl -k -H "Host: <hostname>" -m5  "https://$USER:$PASS@server"'
```

### tail

Ignore first line:
`tail -n+2`

### tr

Convert commas to new lines:
`tr , '\n' < head.csv`

Replace spaces with tabs:
`cat <file> | tr ':[space]:' '\t' > out.txt`

### xargs

If xargs only reads first line:

```sh
xargs -L 1
xargs -n 1 (MacOS) 
```

Regexp replace inline - note -i '' (see [http://joemaller.com/823/quick-note-about-seds-edit-in-place-option/]):
`find . -name '<filename pattern>' | xargs sed -i '' -e 's/<old>/<new>/'`

Find all of the 'FIX ME' s:
`find . -name '*' | xargs grep -i 'fix' -I`

## File Management

### Checking files/directories

Find lines of code:
`find . •name ”*.java” -print | xargs wc -l`

Show size of top-level directories in MB:
`du -m -d 1`

Count number of files:
`find . -name '*.jsp' | xargs wc`

Find all files changed in last 12 days:
`find . -mtime -12 -print`

### Doing things with files

Rename all files to lowercase:
`for f in *.txt; do mv -v "$f" "`echo $f | tr '[A-Z]' '[a-z]'`"; done`

Move file from title_* to kr_title_*:
`for f in title_*; do mv $f kr_$f; done`

Find files changed in last N minutes:
`find -cmin -N`

Find scala files and sort by size:
`find . •name ’*.scala’ | xargs ls -l | awk ’{print $5 ” ” $9}’ | sort -n`

Iterate over files and execute script:
`for file in *.txt; do script.sh $file; done`

Use a subshell to generate a complex list of files for tar:
`(find /one -print0; find /two -print0) | tar cvf backup.tar --null -T -`

SHA:
`shasum -a 256 <file>`

### ffmpeg

Transcode all the .WAV files to .OGG in the current directory:
`for f in *.wav; do ffmpeg -i $f -vn -ab 96k -acodec libvorbis -ab 96k -ar 48000 -ac 2 "${f%%.*}".ogg; done`

## Networking

'too many open files'
To see how many open files configured:
`ulimit -a`

### fuser

When `lsof` isn't installed, you can use `fuser`:

```sh
fuser -v /dev/snd/*
```

### netcat

Netcat - test connection as client:
`nc -vz <host>:<port>`

Netcat - setup server:
`nc -lvnp <port>`

Use netcat to see if a remote port is open:
`nc -vz <dest_ip> <dest_port>`

More verbose: `nc -vv -n <dest_ip> <dest_port>`

### netstat

Number of connections to a given port:
`netstat -ant | grep 10.42.0.16:18494 | wc -l`

Number of remote hosts connected:
`netstat -tup | awk '{print $5}' | awk -F ':' '{print $1}' | sort | uniq -c`

Continuous network stats:

```sh
netstat -i -w 1
netstat -i -c
```

MacOS route table:
`netstat -rn`

### lsof

To see how many open files or ports currently used:

```sh
lsof -p <pid> |wc -l
or:
lsof | grep apache | wc -l
netstat -ntlp | grep LISTEN
```

Check for open deleted files: `lsof +L1 | grep <PID>`

### iperf

IPerf3 - network performance test server side:
`iperf -s`

IPerf3 - network performance client side test for 30s, show results every 5s:
`iperf -c 192.168.2.1 -t 30 -i 5`

### route

Debug ip routing table:
Linux:
`ip route get xx.xx.xx.xx`

MacOS:
`route get xx.xx.xx.xx`

### sysdig

Track SSL connections: `sysdig | grep s_connect`

### tcpdump

Traffic sniffing for Docker port 9000 (minio!):

`sudo tcpdump -s 0 -A -i docker0 tcp port 9000`

## Processes

Top process `iotop -P -b -o -n 3`

Kill Suspended Job: `kill -KILL %1`

Find number of threads used by process:`ps -o nlwp <pid>`

### cron

Cron tips:
% needs to be escaped with backslash, e.g:
`0 5 * * * some_command > /home/ubuntu/backups/dump_$(date +\%Y_\%m_\%d).sql`

## Architecture

`file <executable>` - show file type and dynamically linked libraries.  If DLL doesn't exist, you get 'No such file or directory' error

`patchelf --set-interpreter /opt/makemkv/lib/ld-linux-aarch64.so.1 <file>`

## SSH

Login as another user:
`sudo su webapp`

Copy SSH key to remote server:
`ssh-copy-id 'user@remotehost'`

e.g.:
`ssh-copy-id pi@192.168.2.198`

Reverse Proxy:
`ssh -R 80:localhost:8000 serveo.net`

`ssh-keygen -y -e -f <private key>` - check private key

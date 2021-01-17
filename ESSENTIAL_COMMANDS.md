# Essential Commands

## Obtain a full list of directories under `/` along with their size

```
$ sudo du --max-depth=1 -hx /
```

## Count the number of lines in lorem.txt

```
/vagrant$ wc -l lorem.txt 
209 lorem.txt
```

Assuming lines are numbered from 1 to 210, move the 210th line to line 1.

```
/vagrant$ ed -p% lorem.txt 
Newline appended
60155
%n
210	Consetetur sadipscing elitr, sed diam nonumy eirmod tempor invidunt ut labore et dolore magna aliquyam erat, sed diam voluptua. At vero eos et accusam et justo duo dolores et ea rebum. Stet clita kasd gubergren, no sea takimata sanctus est Lorem ipsum dolor sit amet. Lorem ipsum dolor sit amet, consetetur sadipscing elitr, sed diam nonumy eirmod tempor invidunt ut labore et dolore magna aliquyam erat, sed diam voluptua. At vero eos et accusam et justo duo dolores et ea rebum. Stet clita kasd gubergren, no sea takimata sanctus est Lorem ipsum dolor sit amet. Lorem ipsum dolor sit amet, consetetur sadipscing elitr, sed diam nonumy eirmod tempor invidunt ut labore et dolore magna aliquyam erat, sed diam voluptua. At vero eos et accusam et justo duo dolores et ea rebum. Stet clita kasd gubergren, no sea takimata sanctus.
%210m0
%n
1	Consetetur sadipscing elitr, sed diam nonumy eirmod tempor invidunt ut labore et dolore magna aliquyam erat, sed diam voluptua. At vero eos et accusam et justo duo dolores et ea rebum. Stet clita kasd gubergren, no sea takimata sanctus est Lorem ipsum dolor sit amet. Lorem ipsum dolor sit amet, consetetur sadipscing elitr, sed diam nonumy eirmod tempor invidunt ut labore et dolore magna aliquyam erat, sed diam voluptua. At vero eos et accusam et justo duo dolores et ea rebum. Stet clita kasd gubergren, no sea takimata sanctus est Lorem ipsum dolor sit amet. Lorem ipsum dolor sit amet, consetetur sadipscing elitr, sed diam nonumy eirmod tempor invidunt ut labore et dolore magna aliquyam erat, sed diam voluptua. At vero eos et accusam et justo duo dolores et ea rebum. Stet clita kasd gubergren, no sea takimata sanctus.
%w
60155
%q
```

## Delete lines 208 to 210 inclusive.

```
/vagrant$ ed -p% lorem.txt 
60155
%n
210	
%208,210d
%n
207	Duis autem vel eum iriure dolor in hendrerit in vulputate velit esse molestie consequat, vel illum dolore eu feugiat nulla facilisis.   
%wq
59281
```

## Replace every occurrence of the word Consetetur with an uppercase C with the word Cricket.

```
/vagrant$ sed -i 's/Consetetur/Cricket/g' lorem.txt
```

s is the substitute command for sed to find and replace.
g means substitute globally (without this option the first occurance will be replaced).
To find and replace in case insensitive manner we would use the I option i.e. 's/Consetetur/Cricket/gI'
-i means update the file.


## Add a new line at the very end of the document that contains _Lorem ipsum_.

```
/vagrant$ printf '\nLorem ipsum' >> lorem.txt
```

## Archive and compression exercises

- Create a /opt/tmp001.zip file out of /tmp
- Extract all files from archive file /opt/tmp001.zip into target directory /opt/SAMPLE001
- Create a tar archive file /opt/SAMPLE0001.tar containing the files in the directory /opt/SAMPLE001
- Compress the archive file /opt/SAMPLE0001.tar using the bzip2 compression algorithm 
- Compress the tar archive file /opt/SAMPLE0001.tar using the `xz` compression algorithm

```
$ zip -r tmp001.zip /tmp/
$ sudo mv tmp001.zip /opt/
$ sudo unzip /opt/tmp001.zip -d /opt/SAMPLE001
$ sudo tar -cvf /opt/SAMPLE0001.tar /opt/SAMPLE001/
$ sudo xz -k /opt/SAMPLE0001.tar
``` 

## Finding files exercises

See [40 Best Examples of Find command in Linux](https://geekflare.com/linux-find-commands/) as a reference.

## Find all directories in `/vagrant/FILES`.

```
$ find /vagrant/FIND -type d
/vagrant/FIND
/vagrant/FIND/empty
/vagrant/FIND/somedir
/vagrant/FIND/another
```

## Find all files in `/vagrant/FILES` (result not shown for brevity), then count them.

```
$ find /vagrant/FIND -type f
$ find /vagrant/FIND -type f | wc -l
14
```

## Find all files in `/vagrant/FIND` with case-insensitive word `heythere` in title.

```
$ find /vagrant/FIND -type f -iname heythere*
/vagrant/FIND/somedir/HEYthERE1.sh
/vagrant/FIND/somedir/heythere.sh
```

## Find all executable files in `/vagrant/FIND`

```
$ find /vagrant/FIND -type f -executable
/vagrant/FIND/exec2.sh
/vagrant/FIND/exec1.sh
/vagrant/FIND/somedir/surprise.sh
```

## Find all files in `/vagrant/FIND` containing the case sensitive word `kiwi`. 

The `-l` option in `grep` means `--files-with-matches` which will suppress normal output and instead print the name of each input file from which
output would normally be printed.

Note with `find (GNU findutils) 4.7.0-git` the `;` needs to be escaped else you'll get `find: missing argument to -exec`.

```
$ find /vagrant/FIND -type f -exec grep -l "kiwi" {} \;
/vagrant/FIND/hey
/vagrant/FIND/bob
```

## Find all files in `/vagrant/FIND` larger than 512kb in size

```
$ find /vagrant/FIND -type f -size +512k
/vagrant/FIND/somedir/ddgenfile.img
```

Even better include the actual file size in human readable format as follows.

```
$ find /vagrant/FIND -type f -size +512k -exec du -h {} \;
1.0M	/vagrant/FIND/somedir/ddgenfile.img
```

## Find all empty files in `/vagrant/FIND`

```
$ find /vagrant/FIND/ -type f -empty
```

To sanity check:

```
$ find /vagrant/FIND/ -type f -empty -exec du {} \;
```

## Find all directories in `/vagrant/FIND` larger than 20kb

```
$ find /vagrant/FIND -type d -size +20k
```

## Find all files in `/vagrant/FIND` that have not been accessed in the last 30 days

```
$ find /vagrant/FIND -type f -atime +30
```

## Find all files in `/vagrant/FIND` that have not been modified in the last 30 days

```
$ find /vagrant/FIND -type f -mtime +30
```

## Find all executable files in `/vagrant/FIND` and delete them

```
$ find /vagrant/FIND -type f -executable
$ find /vagrant/FIND -type f -executable -exec rm {} \;
$ find /vagrant/FIND -type f -executable
```

## Find all empty files in `/vagrant/FIND` and delete them

```
$ find /vagrant/FIND -type f -empty
$ find /vagrant/FIND -type f -empty -exec rm {} \;
$ find /vagrant/FIND -type f -empty
```

## Find all files in `/vagrant/FIND` with `.txt` extension and write with full path to `/vagrant/FIND/compress.txt`

```
$ find /vagrant/FIND -type f -name "*.txt"
$ find /vagrant/FIND -type f -name "*.txt" > /vagrant/FIND/compress.txt
```

## Find all files containing the case insensitive word `kiwi`.

The `-i` means ignore case distinctions, so that characters that differ only in case match each other.

```
$ find /vagrant/FIND -type f -exec grep -l -i "kiwi" {} \;
$ find /vagrant/FIND -type f -exec grep --files-with-matches  --ignore-case "kiwi" {} \;
/vagrant/FIND/super.txt
/vagrant/FIND/hey
/vagrant/FIND/bob
```
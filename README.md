# LFS201

Linux Foundation Certified System Administrator (LFCS) Preparation

Having completed the _Essentials of Linux System Administration (LFS201)_ course, these are the exercises completed before sitting the actual LFCS exam.

## Setup

As per the Vagrantfile first install these to get a virtual local Ubuntu Ubuntu 18.04.5 LTS:

* vagrant
* virtualbox

Then _vagrant up_, _vagrant ssh_, _cd vagrant_ and let's crack on.

## Cracking On

### Essential Commands

Count the number of lines in lorem.txt

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

Delete lines 208 to 210 inclusive.

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

Replace every occurrence of the word Consetetur with an uppercase C with the word Cricket.

```
TODO
```

Add a new line at the very end of the document that contains _Lorem ipsum_.

```
TODO
``` 
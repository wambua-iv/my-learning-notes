
**file into multiple files and recombine them later,** that will allow you to transfer all of the data, just not as a single file (so you'll likely need to recombine the file before it is useful). For example, on Linux you can do something similar to:

```
$ truncate -s 6G my6gbfile
$ split --bytes=2GB --numeric-suffixes my6gbfile my6gbfile.part
$ ls
my6gbfile         my6gbfile.part00  my6gbfile.part01
my6gbfile.part02  my6gbfile.part03
$
```

Here, I use [`truncate`](https://www.man7.org/linux/man-pages/man1/truncate.1.html) to create a sparse file 6 GiB in size. (Just substitute your own.) Then, I split them into segments approximately 2 GiB in size each; the last segment is smaller, but that does not present a problem in any situation I can come up with. You can also, instead of `--bytes=2GB`, use `--number=4` if you wish to split the file into four equally-sized chunks; the size of each chunk in that case would be 1 610 612 736 bytes or about 1.6 GiB.
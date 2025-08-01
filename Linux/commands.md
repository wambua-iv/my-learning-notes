
[ln] => create links between files
	> creates hard links by default
		take up no virtual space
		fast 
		don't break when delete
		cannot work across partitions
	> **-s** option
		creates symbolic links
		can link to directories
		 break if the target is deleted 
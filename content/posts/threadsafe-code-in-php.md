---
title: How to run threadsafe code in PHP
date: 2019-05-10T09:09:18+05:30
description: Tutorial to acquire lock in PHP and run a thread safe code so that only one thread could execute a portion of code at a time.
slug: threadsafe-code-in-php
categories:
  - Web Development
tags:
  - php
---
Suppose you have a php file in which there is a code block which you want thread safe. It means, if that php file is being called from many places simultaneously and you want the code block to be executed one by one irrespective of being called at the same time by different places. So here is the theory :

1. Create a temporary directory if not exits.
2. Check whether temporary directory is empty or not. If not empty then repeat this step ( step 2) after some time, lets say 50 milliseconds. If empty then go to step 3
3. Fetch current timestamp in milliseconds (milliseconds since 1 Jan 1970). Create a directory with that timestamp inside the temporary directory. In this way that temp directory will contain directories whose filename is a number.
4. Now check all the files inside that temporary directory and check whether our timestamp is the lowest number among all file name. If it is the lowest timestamp then you have acquired the lock and go to step 5, if not it means somebody already acquired the lock and therefore sleep for 50 milliseconds and then go to step 2.
5. Write down your thread safe code.
6. Now to release the lock, delete that temporary directory and all it sub directories recursively.
7. Thats it. Here is a sample program 

```php
<?php

$lockPath =	'tempDir/lock/';
if (!file_exists($lockPath)) {
	mkdir($lockPath, 0777, true);
}

while (true) {
	 $lock = getLock($lockPath);
	 if ($lock == 1) {
		break;
	 }
	 sleep(mt_rand(10, 50));
}

// Your thread safe code goes here


// releasing the lock
if (file_exists($lockPath)) {

	 $handle = opendir($lockPath);
	 while (false !== ($entry = readdir($handle))) {
	 if ($entry !== '.' && $entry !== '..') {
		rrmdir($lockPath . $entry);
	 }
	 }
	 closedir($handle);
}


function rrmdir($dir) {
	 if (is_dir($dir)) {
		$objects = scandir($dir);
		foreach ($objects as $object) {
		 if ($object != "." && $object != "..") {
			if (is_dir($dir."/".$object))
				 rrmdir($dir."/".$object);
			else
				 unlink($dir."/".$object);
		 }
		}
		rmdir($dir);
	 }
}

function getLock($lockPath) {
	 if (is_dir_empty($lockPath) == 1) {
		$milliseconds = round(microtime(true) * 1000);
		mkdir($lockPath. '/'.$milliseconds, 0777, true);
		return isLowestTimestamp($lockPath, $milliseconds);
	 } else {
		return 0;
	 }

}

function isLowestTimestamp($path, $milliseconds) {

	 $dir = opendir($path);
	 while(false != ($file = readdir($dir))) {
		if(($file != ".") && ($file != "..")) {
				 if ((int)$file < $milliseconds) {
				 return 0;
			}
		}
	 }
	 return 1;
}

function is_dir_empty($dir) {
	 $handle = opendir($dir);
	 while (false !== ($entry = readdir($handle))) {
	 if ($entry !== '.' && $entry !== '..') {
		closedir($handle);
		return 0;
	 }
	 }
	 closedir($handle);
	 return 1;
}
?>
```

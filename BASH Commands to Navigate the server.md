# Navigating the Server: Bash Commands
## Notes by: Pagé Goddard
## Tutorial session by: Jennier Liberto

### look at your directory contents
* `ls` lists just the file and subfolder names in your directory
* `ls -1` does the same, but puts it all in a single column
* `ll` lists contents with permissions, size, and date information
* `ls -lrta` list in reverse time, anything (lists latest last in list)

### permissions
`drwxr-xr`-- *example 1*
`-rwxr-xr`-- *example 2*

* initial `d` = directory; `-` = file
* `w` = can write
* `x` = can execute
* `r` = can read

### changing directories

```bash
pwd
/media/burchardraid01/datafreeze # example directory

cd .. && pwd # change then prnt new directory
/media/burchardraid01 # parent directory

cd ~ && pwd
/media/.../your.directory # you are now in your home directory

cd path/to/desired/directory && pwd
path/to/desired/directory # you are now in the directory you asked for
```

`pwd` = print working directory; tells you where you are

`cd` = change directory

`.` = current directory

`..` = previous directory

`~` = home

###### note: when typing a path, use `/` not backslash; if you get an error, check that you are not trying to `cd` into a file instead of the directory; if error persists, check your slashes, your capitalization and your path

### visualize files
`less filename` = opens 1 page view of data without printing the whole thing to screen; move by line (arrow keys) or page (space)

`head filename` = prints first 10 lines of file

* `head -3 filename` = prints first 3 lines
* `tail filename` = prints last 10 lines of file
* `tail -3 filename` = print last 3 lines
* `head 10 filenme | tail -3 -` = print 3 lines after line 10 of file
* `cut -d' ' -f 1-5 filename` = print columns 1-5 of file; `d` is your file delimiter (the symbol that separates columns, usually space `' '`, comma `','`, or tab `'\t'`)
* `cut -d' ' -f 1-5 filename | head -5 -` = print only rows 1-5 of columns 1-5 of file

`vim filename` = let's you view and edit file

* to edit, press `i` (for insert) and write as you please
* navigate lines with arrow keys
* to save click `Esc` and type :w
* to quit, click `Esc` and type :q
* to save and quit type :wq

### copying files to your computer

###### on a mac/linux

```bash
exit # leave server
scp username@hostname.edu:path/to/file/filename.csv ~/Desktop
```

* translation: copy/paste \[this file on server\] \[to my personal desktop\] 
* `scp` = secure copy paste (copy paste over secure ssh connection)
* to push to server, just switch ~/Desktop and username@hostname.edu:/path

###### or use filezilla GUI

# you can now read your data into R studio from your personal computer

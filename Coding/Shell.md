- Ls: list. used to list the contents of a directory
	- Used with -a to list all contents including hidden files and directories
	- Used with -l to list all contents in long format
	- Used with -t to list all contents by time they were last modified

- Pwd: used to print working directory file path
- Helper commands
	- Clear: clears the terminal
	-  <kbd>tab</kbd> autocompletes a line
	-  <kbd>up arrow</kbd> and <kbd> down arrow</kbd> to cycle through the previous commands

- Touch: creates a new file in the current working directory
- Cd: changes the current working directory. There are several different arguments that can be used:
	- Full file path
	- Names of children of the current directory
	- The parent of the current directory

- Grep -i: used to search files for lines matching a pattern, case insensitive
- Grep -R: used to search all files in a directory, including its subdirectories, and outputs filenames and lines containing matched results
- Mkdir: make directory.  Used to create a new directory in the file system according to its argument. If a file path is given, the new will be placed at the end. Otherwise, it will create a new directory in the current working directory.
	- Creating a new folder office in an existing directory /home/mydata/
		- Mkdir /home/mydata/ office

- Cp: copy. The basic argument structure is cp source destination.
	- Cp file1 file1copy

- Command options: typically a single letter preceded by a -
- Mv: move. Used to move a file. Source file first in argument then destination directory after.
- Rm: remove. Used to delete files and subdirectories. The rm command with -r flag deletes a directory and all of its subdirectories
- >>
- Append redirect: is used to to redirect the standard output of the command on the left and append (add) it to the end on the right

- |

- The pipe command is used to pipe, or transfer, the standard output from the  command on its left into the standard input  of the command on its right

- >

- Redirects the output by taking the output from the command on the left and passing as input to the file on the right

- Cat
	- Displays the contents of one or more files to the terminal

- Export: command makes a given variable available in all child sessions initiated in the current session
	- Example: export USER=“Jane Doe”

- HOME: environment command used to list the home directory of the given user in Unix
- History: used to get a history of commands from the active session. This command also allows us to perform operations on the list of commands that have been executed
- Alias: creates shortcuts
	- Example: alias pd=“pwd”

- For Unix based systems, the command env returns a list of environment variables for the current user
- Examples from Code  academy
![[Pasted image 20260807143331.png]]
![[Pasted image 20260807143439.png]]
![[Pasted image 20260807143444.png]]
![[Pasted image 20260807143450.png]]
![[Pasted image 20260807143455.png]]

To return the UUID of something: wmic path win32_computersystemproduct get uuid
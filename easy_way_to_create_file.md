# The Fastest Way to Create an Empty File on Linux
There's a nice trick to create an empty file on Linux. It's the shortest command you can type, for this purpose. And it requires just one special character, >.

## create an empty file called notes
This works because > is a so-called output redirection operator (in Bash, and similar shells). It tells the shell (command interpreter):
```
>notes
```
## see list of directory
```
ls 
```
```
ls >files.txt
```
## Generate an Empty File on Linux with the touch Command
```
touch notes
```
## Create a File with One Line of Text Inside, on Linux
```
echo "I love DevOps">notes
```
## If you read file with the cat command:
```
cat notes
```
# How does this work?
echo is a command that generates output, based on what text you pass to it.
## If you would run the command 
```
echo "I love python"
```
## > redirect operator. So adding that at the end:
```
echo "Don't forget to buy milk" >notes
```
## Create a File with Multiple Lines of Text Inside, on Linux
```
cat >notes
```
Press CTRL+D (hold down CTRL key and press d).

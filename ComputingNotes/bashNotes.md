#Notes on bash scripting

##Basic bash

Bash scripting is useful for automating command line actions. Alternatively, you can also perform command line actions through interfacing with python.

A simple bash script could look something like this:

```bash
#!/bin/bash

timestamp() {
  date +"%T"
}

timestamp

num=0
for d in {0..5}
do
  echo ${d}
  ./c_script ${d} || break;
  num=$((num+1))
  echo ${num}
done

timestamp
echo "Done"
```

This script runs c_script in a for loop while taking an input of value d, and printing to screen which number the script is on.

Depending on the permissions for where you are running a script, you may need to change the permissions of the script by: `chmod u+x c_script_name`.

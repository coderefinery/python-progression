# Command-line interfaces (CLI)
```{objectives}
- Understand the basics of command-line interfaces
- Learn how to write code that can change its behaviour based on command-line arguments
```

In the previous section, we learned how to convert a Jupyter notebook into a Python script that is executable from the command line. Though we can now run it in the command line, every time we want to change something in the analysis we would need to edit the script.

In this section we will learn how to change the behaviour of a script based on command-line arguments. This will allow us to run the script with different parameters without having to edit the script every time. 

This is useful when you want to share your script with other people, as they wont necesarilly need to know what is inside the script to run it. 


## Command-line arguments with `argparse`
Command-line arguments are parameters that are passed to a script when running it in the command line. 
An example of this is:
```shell
python my_script.py arg1 arg2
```
In this case, `arg1` and `arg2` are the command-line arguments that are passed to the script `my_script.py`.

`argparse` is a Python module that makes it easy to write descriptive command-line arguments, and it also automatically writes useful `--help`sections for your script. 

`argparse` defines two types of arguments:
- Positional arguments: these are required arguments that are passed to the script in a specific order.
- Optional arguments: these are arguments that are not required and can be passed in any order.

 
To use argparse you first set up a parser by calling `parser = argparse.ArgumentParser()` and then you add arguments using `parser.add_argument(args)`.

Lets write an example script that takes in a name and a birth date as arguments and prints them out.

```{code} python
import argparse

# Initialize the parser
parser = argparse.ArgumentParser()

# One positional and one optional argument
parser.add_argument('name', type=str, metavar="N",
                    help="The name of the subject")
parser.add_argument('-d', '--date', type=string, default="01/01/2000",
                    help="Birth date of the subject")

# Parse the arguments and collect them in args
args = parser.parse_args()

print(args.name + " was born on " + args.date)
```  
when this is run you have to pass in the name and the date as arguments in this way:
```shell
python birthday.py John -d 01/01/1990
```
If you run the script without the date argument, it will default to the date specified in the `add_argument` function.

If you run the script with the `--help` flag, you will get a description of the arguments that the script takes in:
```shell
python birthday.py --help
```
which would return:
```
usage: birthday.py [-h] [-d DATE] N

positional arguments:
  N                     The name of the subject

optional arguments:
  -h, --help            show this help message and exit
  -d DATE, --date DATE  Birth date of the subject

```
Lets now try  

````{challenge} Exercise Scripts-2: Add command-line arguments to a script (15 mins)

Use the example above edit the script `weather_observations.py` from the previous exercise to take in the date range as arguments using `argparse`.

- Hint: The script should be able to be run like this:
    ```shell    
    python weather_observations.py --start 2020-01-01 --end 2020-12-31  
    ```
- Hint: try not to do it all at once, but add one or two arguments, test, then add more, and so on.

- Hint: The input and output filenames make sense as positional arguments, since they must always be given. Input is usually first, then output.

- Hint: The start and end dates should be optional parameters with the defaults as they are in the current script.

Discussion:
- What was the main challenge in adding the arguments?
- What would you have to do if you wanted to add more arguments? or a new analysis?
- How would you work with the arguments in for example a slurm submit script?
````
This is not the only way to add command-line arguments to a script. We encourage you to explore other ways to do this, such as using `sys.argv`, `doctopt`, `typer` or `click`.
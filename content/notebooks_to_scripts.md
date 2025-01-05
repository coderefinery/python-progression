# From notebooks to scripts 

```{objectives}
- Understand when notebooks are not useful anymore and when to start using scripts
- Be able to use `nbconvert` or other tools to convert a notebook to a script
- be able to edit a script and work with command line interface arguments
```

## Why make the change?
Though we have seen that notebooks are very practical and useful for many tasks, such as testing out analyses, sharing results, teaching, and more, there are some cases where it is better to use scripts.

We will all at one point find out that notebooks are not the best tool for  every use. Some cases where a script would do a better job are:
- You want to run an analysis at a specific time every day, with a scheduler of some kind
- Your notebook has exceeded your laptops capabilities and you want to migrate your workflow to a supercomputer with a scheduler such as SLURM
- You run a complex process in the notebook needs to be optimized and profiled with the relevant tools (We will look at this more later in this course) 

Sometimes one also finds oneself in some common pitfalls due to deadlines or just poor planning (test code that turns into production code), these can be such as:
- lack of linearity, 
- notebook becoming too long
-  Attempt at parallelization with duplicate notebooks, which make it hard to spot errors and maintain the code.  

I hope I have convinced you that notebooks are not always the best tool for the job. Although scripts are not the perfect tool either, they are still a powerful tool and allow for more flexibility and control.
Let's move on to how we can convert a notebook to a script.

## Converting a notebook to a script

There are several ways to make a notebook into a command line script. One such way is `nbconvert`, which is a tool that comes with Jupyter.
Check the `JupyterLab documentation <https://jupyterlab.readthedocs.io/en/stable/user/export.html>`for more information.

You can get a command line in jupyter lab by (File → New Launcher → Terminal - if you go through New Launcher, your command line will be in the directory you are currently browsing), you can convert files in the terminal by running:
```{code-block} bash
  jupyter nbconvert --to script your_notebook_name.ipynb
```
If you are struggling with the command line, you can also convert a notebook to a script by going to the notebook and clicking on `File → Save and Export Notebook As → Executable Script`.
This will save the notebook in your machine, so you will have to copy it to the relevant directory (if you want it on a remote machine such as a supercomputer).

```{figure} img/jupyter_to_scripts/exporting-notebooks.png
:alt: Exporting notebooks using the top menu bar in jupyter lab.
:width: 80%

Press in the top menu **File → Save and Export Notebook As → Executable Script** to download the notebook as a script.

```

````{challenge} Exercise Scripts-1: Convert a notebook to a script (20 mins)

1. Download the following [notebook](https://nbviewer.org/github/coderefinery/python-progression/blob/main/notebooks/weather_observations.ipynb)

2. Convert the notebook to a script using the terminal following the instructions above.
    ```{code-block} bash
    jupyter nbconvert --to script weather_observations.ipynb
    ```
3. Run the generated script in the terminal with:
    ```shell
    python weather_observations.py
    ```
Discussion questions:
- What changed when you converted the notebook to the script?
- What would have happened if the notebook was not written with linear execution in mind?
- Is this something you think you can use in your work?
````
There are some other ways of executing your notebook as a script, such as papermill, though we will not go through these today.

We can now move on to working with command line arguments in scripts, which is where scripts really shine.



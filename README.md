# AWK_tutorial

This tutorial shows you how to work with tables and other text files on the command line with AWK and sed. You can use these tools to select rows and columns, to calculate sums and means, to clean up names, to stack the tables of several samples into one, and to combine information from two files. You will try every command on real biological data: a table with plant growth measurements, and a table with information on the bacterial strains used in that experiment.

## What are AWK and sed?

**AWK** is a small programming language made for tables. It reads a file one line at a time and splits each line into **fields**, the separate values in a line, like the cells in one row of a spreadsheet. You then tell AWK what to do with these fields, for example "print the second and fourth column" or "keep only the rows where the root length is above 50".

**sed** reads a file one line at a time and changes the text on the fly. You use it mostly to search and replace, for example to shorten a long sequence name, or to delete lines you do not need.

Both tools come with almost every Linux and Mac system, they work on files of any size, and they need only one line of code for many everyday tasks. That is why you meet them in many bioinformatics scripts and tutorials.

## Why not just use Excel, R or Python?

For statistics and plots, R or Python are the better tools. But for many smaller jobs, AWK and sed have several advantages:

-   **You keep a record of what you did.** A command can be saved in a script or a notebook, and run again on new data. A table that you change by hand in Excel leaves no record, and Excel sometimes changes your data without asking, e.g. it is known to turn gene names such as `SEPT2` into dates.
-   **The same step works on many files.** Together with a [`for` loop](https://scienceparkstudygroup.github.io/ibed-bioinformatics-page/source/core_tools/bash-for-loops.html), one command can clean the names in 50 FASTA files, or take one column from 200 sample tables.
-   **You can look at your raw data yourself.** Amplicon and (meta)-genome analyses typically start on the command line and AWK commands are extremely useful to quickly explore this kind of data.

## Why learn this when an LLM can write it?

LLMs (large language models, such as ChatGPT or Claude) are normally good at writing AWK and sed commands. So it is not the goal of this tutorial to make you write long AWK programs from memory. Instead, after this tutorial you should be able to:

-   **read** a short AWK or sed command, and say what it does
-   **check** whether it does what you asked for, on your own data
-   **adapt** it, for example to use another column or another file
-   **recognize the common traps**, where a command runs without an error but gives the wrong result

Recognizing traps is extremely important. That is because a command that fails with an error message is easy to notice. However, aa command that quietly skips a few rows, or treats a missing value as a zero, is not.

## What you will do

1.  **Use AWK on a table** (about 1 hour): print and select columns and rows, and find missing values
2.  **Calculate with AWK** (about 45 minutes): add a column, and calculate sums, counts and means per group
3.  **Learn sed basics** (about 1 hour): search and replace text, delete lines, change a file safely, and remove Windows line endings
4.  **Use more of AWK** (about 40 minutes): change and search text in one column, pass a value from a bash loop into AWK, and split a value into parts, all forms you often see in code from an LLM or a forum
5.  **Stack tables** (about 30 minutes): put one table per sample under each other into one long table, add a column with the sample name, and check that no header or row is there twice
6.  **Join two tables** (about 1 hour): add columns from the strain table to the growth data by the strain name, make names match, and check the result
7.  **Use AWK and sed on FASTA files** (optional, about 20 minutes): count sequences and clean up sequence names
8.  **Use AWK on FASTQ files** (optional, about 20 minutes): count sequencing reads without counting too many, and turn reads into a FASTA file
9.  **Check code from an LLM** (about 45 minutes): a short routine to test any AWK or sed command before you trust its output
10. **Command overview**: the main commands from all pages on one page, to look up later

The times are a rough estimate. They include reading and running the commands, but not the exercises.

## A short route (about 2 hours)

This route covers the commands you need most often, and the traps that give a wrong result without an error:

1.  [AWK on a table](https://ndombrowski.github.io/AWK_tutorial/source/awk_table.html), the whole page.
2.  On [sed basics](https://ndombrowski.github.io/AWK_tutorial/source/sed_basics.html), the first section, [The shape of a sed command](https://ndombrowski.github.io/AWK_tutorial/source/sed_basics.html#the-shape-of-a-sed-command), and the section [Windows line endings](https://ndombrowski.github.io/AWK_tutorial/source/sed_basics.html#windows-line-endings). That section uses the strain table, `awk_data/mmc5_metadata.txt`.
3.  On [Checking code from an LLM](https://ndombrowski.github.io/AWK_tutorial/source/check_llm_code.html), the table [A routine for any AWK or sed command](https://ndombrowski.github.io/AWK_tutorial/source/check_llm_code.html#a-routine-for-any-awk-or-sed-command).

You can come back for the other pages when you need them, for example when you have to stack or join tables.

## Before you start

To follow this tutorial you need:

-   **A terminal and the command line basics.** This tutorial assumes you know how to use the CLI or that you have worked through the [basics of the CLI workshop](https://ndombrowski.github.io/cli_workshop/source/bash_intro.html): moving around with `cd` and `ls`, looking at files with `head` and `wc`, and combining commands with a pipe (`|`). It also helps if you have used `grep` and `cut`. If you have never opened a terminal, start with [Before you start](https://ndombrowski.github.io/cli_workshop/source/installation.html) of the CLI workshop.
-   **Your own computer.** All commands are meant to run on your laptop. If you already know how to use Crunchomics, the UvA computer cluster, you can also work there (see the [HPC pages of the CLI workshop](https://ndombrowski.github.io/cli_workshop/source/hpc_intro.html)).
-   **The example data.** You download it at the start of the first page, with two commands. It is small (about 440 kB), so it fits on any laptop. The data comes from an experiment where *Arabidopsis* plants grew with low or high phosphorus, either without bacteria or inoculated with one of two rhizobia strains. For each plant, root length and shoot weight were measured. The question was whether these bacteria promote plant growth, and might help plants grow when nutrients are limited. A second table describes these and other strains, from a [published study](https://www.cell.com/cell-host-microbe/fulltext/S1931-3128(18)30318-4).

::: {.callout-important title="AWK and sed are not the same on every computer"}
There are several versions of AWK and sed. Linux systems and Crunchomics usually have the GNU versions, Macs have different (BSD) versions, and the terminal in MobaXterm may have yet another one. Most commands in this tutorial work everywhere, and where a command behaves differently, the page says so.
:::

Start with [AWK on a table](https://ndombrowski.github.io/AWK_tutorial/source/awk_table.html).

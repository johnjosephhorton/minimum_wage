# Replication package
## Price Floors and Employer Preferences: Evidence from a Minimum Wage Experiment
by John Horton

This document describes the organization of code, data, and software associated with the project.
The code for this replication package is at: [https://www.github.com/johnjosephhorton/minimum_wage.git](https://www.github.com/johnjosephhorton/minimum_wage.git)


## Overview
This project is largely self-documenting in the sense that the key recipes for building the project are captured in code. 
The key pieces for replicating this project are: 

1. A [Makefile](https://www.github.com/johnjosephhorton/minimum_wage/blob/main/writeup/Makefile) that gives recipes for how each figure, table, and called-out number is constructed in the paper.

1. A [Dockerfile](Dockerfile) that describes the system set-up needed with respect to computing resources and packages. 

### File organization

The main directories are:
```
-writeup
-analysis
-computed_objects
-data_prep
```

The main writeup of the paper is in [`writeup/minimum wage.tex`](https://www.github.com/johnjosephhorton/minimum_wage/blob/main/writeup/minimum_wage.tex).

The figures, tables, and parameter files (more on this later) are in `writeup/plots`, `writeup/tables` and `writeup/parameters`, respectively.

The code in [`analysis`]( https://www.github.com/johnjosephhorton/minimum_wage/blob/main/analysis ) writes files to these locations. 
In the git repository, these locations are empty by design.
When the project is built, these locations are populated with the necessary files. 

### Conventions
Each main figure and table is generated by its own stand-alone R file in `analysis` folder. 
The output that the file creates (e.g., a table or plot) has the same name as the R file, with the type of output prepended. 
For example, the plot `any_exper.pdf` in `writeup/plots` is created by `plot_any_exper.R` in `analysis.`

### Building the paper
The building of this paper is orchestrated by software called ``[make](https://www.gnu.org/software/make/manual/make.html)''.
From the `writeup` folder at the command line, the user simply types `make minimum wage.pdf` which will then build the paper.

### Getting the data
To get the data, you need to obtain a `.env` file from the author.
It contains a) a private URL to the zipped and encrypted data and b) a key to decrypt the data.
This is all handled with the `fetch_data.sh` file.
Once obtained, there are several options.

#### Docker approach

You can just clone the repo and, assuming the `.env` file is in your Downloads folder:
```bash
git clone git@github.com:johnjosephhorton/minimum_wage.git
cd minimum_wage/writeup
cp ~/Downloads/.env . 
```

From here, running this will do everything:
```bash
make docker
```
The actual final PDF, however, will be inside the Docker container. It will, however, give you a localhost URL that will have the final PDF.

#### Non-Docker, local approach

On a Linux machine, I have created a `system_update.sh` file that will install the right dependencies.

```bash
git clone git@github.com:johnjosephhorton/minimum_wage.git
cd minimum_wage
cp ~/Downloads/.env writeup
sudo ./system_update.sh

cd writeup
make minimum_wage.pdf
```

#### Replit approach (convenient)

I have created a public replit ``repl'' here: https://replit.com/@johnhorton/minimumwage
You can __fork__ this repository, then add the `.env` file to to the `writeup` folder then just push the big green 'Run' button.
The dependencies are specified in the '.replit' `replit.nix` files.
It will generate the PDF and store it in `writeup'

### A short make tutorial
A Makefile lists recipes for how a particular output used in the paper is constructed.
The Makefile entry for the any_exper.pdf figure looks like this: 

```Makefile
plots/cool_stuff.pdf: ../analysis/plot_cool_stuff.R  ../data/cool_data.csv
	cd ../analysis && Rscript plot_cool_stuff.R
```

Note that the target, or output is `plots/cool_stuff.pdf` 
After the colon is what this output depends on, code-wise, which is an R file in `analysis` called `plot_cool_stuff.R`.
The tabbed-in-line below is the recipe for how the target is constructed. 
In this case, it is just by running `Rscript` on `plot_cool_stuff.R.`

There is some code that is shared across multiple figures or tables in this repository. 
For example, `utilities_outcome_experimental_plots.R` has helper functions used in several plots.
To capture this dependency, there are entries in the Makefile that look like this: 

```Makefile
../analysis/plot_any_exper.R: ../analysis/utilities_outcome_experimental_plots.R
	touch ../analysis/plot_any_exper.R
```
The command `touch` here updates the timestamp of `analysis\plot_any_exper.R` which in turn 
would cause the recipe for `any_exper.pdf` to be re-run, as `plot_any_exper.R` is a dependency.

### Numbers called out in the writeup
There is also code that does not generate tables for figures, but instead generates "parameters."
These are numbers that are called out in the text but are generated automatically. 
For example, the `parameters/parameters.tex` file contains a line `\newcommand{\numTotal}{159,656}` that is related to the total allocation to the experiment.
If we look in `parameters.R` we see the corresponding code that creates this value.
```R
addParam("\\numTotal", formatC(dim(df.mw.first)[1], big.mark = ","))
```

This `addParam` function comes from a personal R package called `JJHmisc` that is available on GitHub.

## Data Availability and Provenance Statements

### Provenance

This data comes from a large online labor market that conducted the experiment described in the paper. The data was pulled from the company's database using SQL. The experiment described in the paper was conducted by the platform by changing the experience for some subset of users.  The data used for this analysis is proprietary.

### Statement about Rights

- [X] I certify that the author(s) of the manuscript have legitimate access to and permission to use the data used in this manuscript. 

- [ ] I certify that the author(s) of the manuscript have documented permission to redistribute/publish the data contained within this replication package. Appropriate permission are documented in the [LICENSE.txt](LICENSE.txt) file.

### Summary of Availability

- [ ] All data **are** publicly available.
- [ ] Some data **cannot be made** publicly available.
- [X] **No data can be made** publicly available.


### Details on each Data Source

The data for this project are confidential, but may likely be obtained with Data Use Agreements with the data provider. 
The data provider itself chooses to remain anonymous.
As such, researchers interested in access to the data may contact John Horton at jjhorton@mit.edu. 
The author will assist with any reasonable replication attempts for two years following publication.
All data files are CSVs. 

# Dataset list
The paper uses the following datasets: 

1. [`df_mw_first.csv`](codebooks/df_mw_first.md) is the main experimental outcomes at job post level.
1. [`df_mw_all.csv`](codebooks/df_mw_all.md) is the main experimental outcomes at job post level.
1. [`df_mw_admin.csv`](codebooks/df_mw_admin.md) is the main experimental outcomes at job post level.
1. [`df_mw_lpw.csv`](codebooks/df_mw_lpw.md) is the main experimental outcomes at job post level.
1. [`df_exp_results.csv`](codebooks/df_exp_results.md)
1. [`hires_country_composition.csv`](codebooks/hires_country_composition.md)
1. [`event_study_windows.csv`](codebooks/event_study_windows.md)
1. [`did_panel.csv`](codebooks/did_panel.md)
1. [`event_study_hired.csv`](codebooks/event_study_hired.md)
1. [`event_study_windows_hr_v_fp.csv`](codebooks/event_study_windows_hr_v_fp.md)] has the composition of fixed 
        
# Replication

## Getting the data and code

A replicator will put a `.env` file in their `minimum_wage\writeup` folder. 
It will look like this:

```bash
GPG_PASSPHRASE='<password>'
project_name="minimum_wage"
DROPBOX_URL="<url of dropbox link hosting the data>"(base)
```

These values are used in a bash script that fetches the data, at [fetch_data.sh](https://www.github.com/johnjosephhorton/minimum_wage/blob/main/fetch_data.sh)

```bash
#!/usr/bin/env bash
source writeup/.env
wget -O remote_data.tar.gz.gpg $DROPBOX_URL
gpg --batch --passphrase "$GPG_PASSPHRASE" --decrypt remote_data.tar.gz.gpg > data.tar.gz
tar -xvf data.tar.gz
```

## Data-fetching entries in the Makefile

```Makefile
../remote_data.tar.gz.gpg:
	cd .. && ./fetch_data.sh
	
fetch-data: ../remote_data.tar.gz.gpg
	echo "Fetching data"
```

## Running the code 
The least error-prone 
```bash 
$ cd writeup 
$ make docker
```


## Computational requirements
The computational requirements for reproducing this paper are minimal. 
It can be run on a modern laptop in less than 10 minutes, using only open-source software.

The machine details when I last ran it are: 
System info:
- OS: "Ubuntu 20.04.6 LTS"
- Processor:  11th Gen Intel(R) Core(TM) i7-11850H @ 2.50GHz, 16 cores
- Memory available: 31GB memory

The code was last run on 2024-08-016 15:16:41.

The Linux dependencies are mostly either for LaTeX or dependencies for various R packages.

#### Summary

Approximate time needed to reproduce the analyses on a standard 2023 desktop machine:

- [X] <10 minutes
- [ ] 10-60 minutes
- [ ] 1-2 hours
- [ ] 2-8 hours
- [ ] 8-24 hours
- [ ] 1-3 days
- [ ] 3-14 days
- [ ] > 14 days
- [ ] Not feasible to run on a desktop machine, as described below.

#### Details

## Description of programs/code

## Analytic files
The Makefile shows the mapping between each outcome in the paper and the analytic file that creates that outcome.

### License for Code

The code is licensed under an MIT license. 

### Details

## List of tables and programs

Given the convention used here where each R file generates a single table or figure, the purpose of each file is mostly self-documenting.  For convenience, each figure or table in the paper is listed here with the file that creates it. It also includes a link to the location in the main body where that file lives. 
Please refer to the Makefile at [Makefile](https://www.github.com/johnjosephhorton/minimum_wage/blob/main/writeup/Makefile) for a list of each table and figure and the associated code and dependencies.

The provided code reproduces:

- [X] All numbers provided in the text of the paper
- [X] All tables and figures in the paper
- [X] Selected tables and figures in the paper, as explained and justified below.

## References

There are no references.


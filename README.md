
# hhsearch-python

##### Current version: 1.1 - Python 3.7

This small package was made to handle data output by the software suite HHSearch. It was tested with output of the HHSearch version 1.5. The project's idea and draft itself originates from Dr. Schmidt and was done as a final task for one of his university modules.

>HHsearch is a software suite for detecting remote homologues of proteins and generating high-quality alignments for homology modeling and function prediction.

[HH-Suite Github](https://github.com/soedinglab/hh-suite) | [Quick Guide to HHSearch](http://ftp.tuebingen.mpg.de/pub/protevo/HHsearch/HHsearch1.5.01/HHsearch-guide.pdf) 

## Installation

You can simply install this package through your pip version. 
```sh
pip install hhsearch-python
```
[Find this package on PyPi!](https://pypi.org/project/hhsearch-python/)

------
### Requirements
For full functionalities you need the following packages as well.
```
pandas==0.23.4
matplotlib==3.0.2
numpy==1.15.4
Pillow==6.0.0
pymol==0.1.0
```

Except for PyMol, everything can easily be installed through pip install. PyMol needs to be installed separately, as well as being installed through `pip` to be used in your regular Python environment. 

##### ```pip``` installation:
```pip install -c schrodinger pymol``` | ```pip install -c schrodinger pymol```

| PyMol Version | Documentation |
| ------ | ------ |
| MAC | https://pymolwiki.org/index.php/MAC_Install | 
| Windows | https://pymolwiki.org/index.php/Windows_Install | 
| Linux | https://pymolwiki.org/index.php/Linux_Install |

------
# Wrapper -  Jupyter Notebook
For this whole module, a wrapper with a UI has been created as [Jupyter Notebook]([https://jupyter.org/](https://jupyter.org/)). 
You just need to open the Jupyter Notebook in this repo with Jupyter, have your `.hhm` and `.hhs` files in subfolders somewhere in the same folder and install the module with `pip install hhsearch-python`. The whole notebook itself is pretty self-explanatory and gives you almost all the options of the functions in this module as a nice UI. 
### Recommended if you like automation and simplicity through UI usage.

------
# Functionalities 
## Broad information about Query & Hit

There are a small handful of functions within this package which can be used to generate a decent organized (visualized) output. However, for this all to work properly, you need to have all the needed `.hhm` as well as all `.hhs` files somewhere located in your current working directory. 

```python
# lets first import all our functions from the module.
from hhsearch_python import *

hhs_file = "data/hhs/d1e0ta1.hhs" # path to your .hhs file.

# first, we can use extract_HHSearch_data() to extract the whole HHSearch statistics into a pandas.DataFrame.
hhs_hits_statistics = extract_HHSearch_data(hhs_file)
```
----
However, we also want regular information about the Query itself, as well as about selected hits. For that we can use the two separate function `extract_HHSearch_main` for the query `.hhs` file, and `get_alignment_term` for a selected hit of the previous created `pandas.DataFrame`.
```python
query_dict = extract_HHSearch_main(hhs_file)

# As an example how this dict() output looks like: 
print(query_dict)
>> {'Query': 'Query d1e0ta1 b.58.1.1 (A:70-167) Pyruvate kinase (PK) {Escherichia coli [TaxId: 562]}',
     'pdb_id': '1e0t',
     'alignment_term': '/1e0t//A/70-167/CA', 
     'full_term': '/1e0t//A//CA', 
     'file_name': 'd1e0ta1'
     }
# alignment_term is needed for a proper PyMol alignment later down the road, 
# as well as full_term, ignoring the specific residues. 

# Let's get information about the second hit of the statistics from the .hhs file. 
hit_dict = get_alignment_term(hhs_hits_statistics, 2)
print(hit_dict)
>> {'pdb_id': '2vgb', 
    'alignment_term': '/2vgb//A/160-261/CA', 
    'full_term': '/2vgb//A//CA', 
    'file_name': 'd2vgba1'}
# except for the key "Query", get_alignment_term() outputs a structure identical dict() as extract_HHSearch_main()
```
------
## Colorized Alignments - HTML formatted

Having selected the second alignment as our target-of-choice, we now desire more information about the alignment itself, so we extract the actual alignment with  `get_full_alignment`. It takes two arguments: the `.hhs` file of the query, as well as the number of the hit within the `.hhs` file, just like `get_alignment_term`. So preferably, one looks at the previously created pandas.DataFrame `hhs_hits_statistics` and choose a hit of interest from that. 

```python
# This also creates a html formatted file in a separate folder - /alignments_highlighted/<query>/<NoX-name>.html
# and also the same file as alignment.html in a folder called /lastrun/, all for your convenience. 
alignment_of_interest = get_full_alignment(hhs_file, 2)
```
The HTML formatted output looks like the example below. As you can see, **h**elices and sh**e**ets are colorized. 
> <img src="https://raw.githubusercontent.com/TimDemtr/hhsearch-python/master/example_alignment.jpg" width="800">



Also, if you desire this formatting to be applied on the whole `.hhs` file, then you can use the function `highlight_hhs_full(hhs_file)` and use the path of the desired `.hhs` file as an argument. It returns the given hhs file as a colorized HTML formatted string and also stores within a separate folder `/alignments_highlighted/<query-name>_full.html` as well as in the `/lastrun folder under the filename hhs_full_colorized.html`.

```python
# outputs the whole .hhs file colorized in the above-shown pattern. 
full_hhs_colorized = highlight_hhs_full(hhs_file)
```
------
## PyMol Alignments - Visualization | Animation
Having alignments organized and colorized is all useful, but we also want to actually create a more visual representation of the chosen alignment. For that, we can use the previous created dictionaries `query_dict` and `hit_dict` and give their information as arguments to the function `pymol_alignment()`. This function also returns the [rmsd value of atomic positions](https://en.wikipedia.org/wiki/Root-mean-square_deviation_of_atomic_positions) in [??ngstr??m](https://en.wikipedia.org/wiki/Angstrom). 

```python
# building up the information from the query. 
pdb_1 = query_dict.get("pdb_id")
aln_term_1 = query_dict.get("pdb_id")
full_term_1 = query_dict.get("pdb_id")

# buildung up the needed information from the chosen hit. 
pdb_2 = hit_dict.get("pdb_id")
aln_term_2 = hit_dict.get("pdb_id")
full_term_2 = hit_dict.get("pdb_id")

# Also returns the RMSD values for the alignment. 
rmsd = pymol_alignment(pdb_1, 
                       pdb_2, 
                       aln_term_1, 
                       aln_term_2,
                       full_term_1,
                       full_term_2)

print(rmsd)
>> (0.8026888370513916, 85, 5, 1.2078778743743896, 98, 160.0, 98)
# In this example RMSD Value is about 0.803 ?? over 85 C-??lpha atoms. 
```

This will create two images in a different folder, as well as a `no_zoom.pse` file, which can be opened with PyMol, alongside with the necessary `.cif` files of the PDB entries into a separate folder called `/cif/`. 
About the pictures: One being zoomed-in into the area of `aln_term_1`whih is in our example:  `/1e0t//A/70-167/CA`, showing the area of interest, as well as a non-zoomed--in picture of `/1e0t//A//CA` in our example.
These images are stored into the `/lastrun/` folder, as well as in the folder `/PyMol_img/<pdb_1>/<pdb_1>-<pdb_2>/`.


| Zoom | No-Zoom|
| ------ | ------ |
| <img src="https://raw.githubusercontent.com/TimDemtr/hhsearch-python/master/main_zoom.png" width="500"> | <img src="https://raw.githubusercontent.com/TimDemtr/hhsearch-python/master/no_zoom.png" width="500">

```
aln_term_1 is represented as cartoon, red.
aln_term_2 is represented as cartoon, blue.

full_term_1 is represented as ribbon, yellow.
full_term_2 is represented as ribbon, green.
```

However, `pymol_alignment` also has an option to output an animated picture instead of just static pictures, as well as the option of a frame multiplier, which needs to be an integer up to 4. But this option takes much more time to process, but of course, gives a _nicer_ output. Each frame multiplier basically doubles the time necessary to create the 360?? view of the model.  The frames are stored into a subdir `/animation` in the `lastrun/` folder, alongside with the animated gif, as well as in the separate folder `PyMol_img/<pdb_1>/<pdb_1>-<pdb_2>/animation/<framemultiplier>`, while the animated gif is stored in the folder upper `/animation`.
```python
# as an example we will create an animated gif with the frame multiplier of 4
pymol_alignment(pdb_1,  
        pdb_2,  
        aln_term_1,  
        aln_term_2,  
        full_term_1,  
        full_term_2,   
        animation = True,   
        framemultiplier= 4)
 ```
 
Be aware, with each run, the lastrun folder's animation subfolder will always be cleared, so there's no confusion in case one runs one time with the animation feature, and in the next run without it. 

>  ```# Example for animation = True, framemultiplier = 4 of our example```
> <img src="https://raw.githubusercontent.com/TimDemtr/hhsearch-python/master/animation_zoom.gif" width="500">

## Barplots of chosen spans
At last, we want to create a barplot of the frequencies of the amino acids within our query based on the [HHMs](https://en.wikipedia.org/wiki/Hidden_Markov_model), as well as in our chosen hit. For that, we first need to extract the frequencies of the `.hhm` file. This gives us a pandas.DataFrame with all the frequencies normed to one, calculated on information of the [HHSuit Wiki](https://github.com/soedinglab/hh-suite/wiki).```
```
Frequency calculation:  
entry = -1000 * log_2(frequency) 
frequency = 2^(-entry/1000)
```
```python
# First we need to set the path of the two hhm files. Luckily, we stored the file_names before.
query_filename = f'data/hhm/{query_dict.get("file_name")}.hhm'
hit_filename = f'data/hhm/{hit_dict.get("file_name")}.hhm'

query_frequencies = read_in_frequencies(query_filename)
hit_frequencies = read_in_frequencies(hit_filename)

```

The output DataFrame of the frequencies looks eventually like this:

| Pos | AS | A |  C | D | E | F | G | (...) |
|-|-|-|-|-|-|-|-|-|
|1|M1|0.030019|0.000000|0.004325|0.014670|0.037111|0.012379|(...) |
|(...)|(...)|(...)|(...)|(...)|(...)|(...)|(...)|(...) |

However, having the frequencies is one thing, we also want to visualize them. For that, one can use the `plot_frequencies` function. This function takes in seven arguments in total, while only one is a requirement. You need to pass down the created pandas.DataFrame of the frequencies. If desired, the name of the created subfolder `barplots/<name>` can be changed. I personally recommend to use the filenames out of the `query_dict` and the `hit_dict` with `query_dict.get("file_name")` and `hit_dict.get("file_name")`. The threshold describes the minimal frequency which has to be hit, so it ends up in the plot. Recommended would be something around 0.1, which equals 10%. Next, we need to set the span_start and span_end for our plot. As an example, we will pick the 1st residue as start and 50th as the end of the span. The filename describes the name the file will be stored under in the `/lastrun` folder. Also, if one likes, one can add a title to the plot, however, I personally dislike this option, since it disturbs the cleaner look. Depending on the span you are choosing, this process can also take some decent time.

```python
plot_frequencies(query_frequencies, # the pd.DataFrames of the frequencies
                 name = query_dict.get("file_name"),  # desired output name. 
                 threshold = 0.1,  # 10% threshold
                 span_start = 1,  # span starting @ 1
                 span_end = 50,  # span ending @ 50
                 filename = "query_barplot.png",  
                 title = False
                 )

plot_frequencies(hit_frequencies, # the pd.DataFrames of the frequencies
                 name = hit_dict.get("file_name"),  # desired output name. 
                 threshold = 0.1,  # 10% threshold
                 span_start = 1,  # span starting @ 1
                 span_end = 50,  # span ending @ 50
                 filename = "hit_barplot.png",  
                 title = False
                 )
  ```

| Query : d1e0ta1, 1-50, min. 10% | Hit : d2vgba1, 1-50, min. 10% | 
| - | - |
|<img src="https://raw.githubusercontent.com/TimDemtr/hhsearch-python/master/query_barplot.png" width = "400">| <img src="https://raw.githubusercontent.com/TimDemtr/hhsearch-python/master/hit_barplot.png" width = "400">| 

-----

#### Contact Information: 
| Email |
|-|
[<img src="https://www.witopia.com/wp-content/uploads/flat-email-icon.png" width = 40>](mailto:"tim.demtroeder@uni-bayreuth.de") 

# Table of Contents

- [Automatically number cells](#automatically-number-cells)
- [Run a notebook remotely](#run-a-notebook-remotely)
- [Sublime-style keyboard shortcuts](#sublime-style-keyboard-shortcuts)
- [Hide code](#hide-code)
- [Hi-Res Plots](#hi-res-plots)
- [Use black](#use-black)
- [Boilerplate](#boilerplate)

## Automatically number cells

Stolen from [here](https://github.com/ipython/ipython/issues/6906)

Insert the following into a cell and run:

```javascript
%%javascript

IPython.notebook.get_cells()
    .filter(function(x){return x.cell_type =='code'})
    .map(function(x,i){
       x.set_input_prompt(i+1)
       // loop also on output_area.outputs and set execution_count if exist.
     })
```

## Run a notebook remotely

On the remote machine, add the following to your ```bashrc``` file:

```bash
ipynb() {
	nohup jupyter notebook --no-browser --port=$1 &
}
```

You can now spinup a remote machine on port 8888 by typing
```bash
$ ipynb 8888
```

Note: you will likely need to check the file `nohup.out` (e.g. run `tail nohup.out`) to find the token (like a password) for your notebook which you'll have to enter later. You should see some lines like

```
[I 11:49:29.071 NotebookApp] The Jupyter Notebook is running at:
[I 11:49:29.072 NotebookApp] http://localhost:8889/?token=e6ebcac8a9f704c2766beac4b06a1e346945348188b70b15
```

where your token is `e6ebcac8a9f704c2766beac4b06a1e346945348188b70b15`.

On your local machine, add the following to your ```bashrc```

```bash
forward_ipynb_ports() {
	ssh -N -f -L localhost:$1:localhost:$2 $3
	printf "Forwarding local port %s to %s remote port %s\n" "$1" "$3"  "$2"
}
```

Forward your local port 8887 to remote port 8888 on host ```remote_hostname``` like:

```bash
$ forward_ipynb_ports 8887 8888 remote_hostname
```

Now, go to http://localhost:8888 in your browser to access your remote notebook. This may open a page where you then have to paste that token.

The "only" pain is shutting the notebook down on the remote instance. You can search for the process (PID) that is running your notebook using `ps aux | grep jupyter` and ```kill``` it. Or, if you're only running one notebook, you can run `pkill jupyter`.

## Sublime-style keyboard shortcuts

Repo is [here](https://github.com/ryantam626/jupyterlab_sublime)

```
jupyter labextension install @ryantam626/jupyterlab_sublime
```

Boom, ⌘+d your way to multiselect freedom.

## Hide code

Stolen from [here](http://chris-said.io/2016/02/13/how-to-make-polished-jupyter-presentations-with-optional-code-visibility/)

Create a `Raw NBConvert` cell at the top of the notebook and add the following. When you export the notebook to HTML, there will be a button to show/hide the code.

There's also this extension, [hide_code](https://github.com/kirbs-/hide_code), but I've found it to be a bit clunky.

```javascript
<script>
  jQuery(document).ready(function($) {

  $(window).load(function(){
    $('#preloader').fadeOut('slow',function(){$(this).remove();});
  });

  });
</script>

<style type="text/css">
  div#preloader { position: fixed;
      left: 0;
      top: 0;
      z-index: 999;
      width: 100%;
      height: 100%;
      overflow: visible;
      background: #fff url('http://preloaders.net/preloaders/720/Moving%20line.gif') no-repeat center center;
  }

</style>

<div id="preloader"></div>

<script>
  function code_toggle() {
    if (code_shown){
      $('div.input').hide('500');
      $('#toggleButton').val('Show Code')
    } else {
      $('div.input').show('500');
      $('#toggleButton').val('Hide Code')
    }
    code_shown = !code_shown
  }

  $( document ).ready(function(){
    code_shown=false;
    $('div.input').hide()
  });
</script>
<form action="javascript:code_toggle()"><input type="submit" id="toggleButton" value="Show Code"></form>
```

## Hi-Res Plots

Stolen from [here](https://gist.github.com/minrk/3301035)

Run the following in a cell

```
%config InlineBackend.figure_format = 'retina'
```

to increase the resolution of matplotlib plots.

Or, do this globally by creating an IPython config file (if you don't have one) at `~/.ipython/profile_default/ipython_config.py` with the following

```python
c = get_config()

c.InlineBackend.figure_format = "retina"
```

## Use black

Install [this](https://github.com/ryantam626/jupyterlab_code_formatter) jupyterlab extension to run the [black](https://github.com/psf/black) code formatter on cells.

Make sure to check out the section about adding keyboard shortcuts. The JSON goes inside Settings -> Keyboard Shortcuts -> User Overrides.

## Boilerplate

I put the following in my `.bash_profile`. I run `jupyter-init` from the command line, and this copies some code into my clipboard which I then paste into the first cell of my jupyter notebooks. While I could create custom matplotlib stylesheets and whatnot, this way my notebooks are more easily reproducible.

```bash
# Copy some jupyter notebook boilerplate to the clipboard.
alias jupyter-init="echo \"%config InlineBackend.figure_format = 'retina'
%load_ext autoreload
%autoreload 2

import matplotlib.pyplot as plt
import pandas as pd
import numpy as np

_RC_PARAMS = {
    'figure.figsize': (10, 6),
    'axes.labelsize': 16,
    'axes.titlesize': 18,
    'font.size': 16,
    'lines.linewidth': 2,
    'lines.markersize': 6,
    'legend.fontsize': 14
}
for k, v in _RC_PARAMS.items():
    plt.rcParams[k] = v
\" | pbcopy "
```

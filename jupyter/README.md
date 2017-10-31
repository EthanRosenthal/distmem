# Automatically number cells

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

# Run a notebook remotely

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

Only pain is killing everything... gotta search for the pids and ```kill```, e.g. `ps aux | grep jupyter`.

# Sublime-style keyboard shortcuts

Stolen from [here](http://blog.rtwilson.com/how-to-get-sublime-text-style-editing-in-the-ipythonjupyter-notebook/)

Find your jupyter configuration folder `jupyter --config-dir`

Go in there and create a `custom` folder. Place the following into a `custom.js` file:

```javascript
require(["codemirror/keymap/sublime", "notebook/js/cell", "base/js/namespace"],
    function(sublime_keymap, cell, IPython) {
        // setTimeout(function(){ // uncomment line to fake race-condition
        cell.Cell.options_default.cm_config.keyMap = 'sublime';
        var cells = IPython.notebook.get_cells();
        for(var cl=0; cl< cells.length ; cl++){
            cells[cl].code_mirror.setOption('keyMap', 'sublime');
        }
 
        // }, 1000)// uncomment  line to fake race condition 
    } 
);
```

Boom, ⌘+d your way to multiselect freedom.
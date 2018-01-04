# Table of Contents

- [Automatically number cells](#automatically-number-cells)
- [Run a notebook remotely](#run-a-notebook-remotely)
- [Sublime-style keyboard shortcuts](#sublime-style-keyboard-shortcuts)
- [Hide code](#hide-code)

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

## Sublime-style keyboard shortcuts

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
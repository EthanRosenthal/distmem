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
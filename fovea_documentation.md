####INTRODUCTION

Dynamical systems play an important role in many scientific fields, but models of such systems can feature many parameters and variables that are confusing to first time users. Fovea is a python package for assisting researchers in the process of building intuition about dynamical systems implemented in algorithms. The package consists of five files.

##1. graphics.py
Fovea graphics provides a set of tools allowing the user to visualize the action of dynamical systems as they evolve over time, or as their parameters change. It adds a new level abstraction to the graphical inheritance structure used by Matplotlib.

Layers are a new data structure that can be associated with the subplots, or axes, of a figure. Whereas axes may contain observed data, the Layers lie over these data like transparent slides and can be used to display an algorithm’s current parameter values or iteration step, hidden variables, or any kind of metadata important to the user. graphics.py includes two main classes:

plotter2D creates the graphical components for specific calculations. It can be used to create and store new figures and Layers and generates artists placed upon them (data, text, axis lines, etc.). For example, two Layers containing clusters of data points observed during two separate experiments may be created and entitled as ‘exp1_data’ and ‘exp2_data’ using separate calls to the method addLayer, then the boundaries of the figure containing these layers may be resized with the method auto_scale_domain.

```python
plotter = gui.plotter

#Create a root figure
plotter.addFig('Master',
title='Project Title',
xlabel='x', ylabel='y',
domain=([0,10],[-2,1.5]))

#Add layers to Master figure with titles.
plotter.addLayer('exp1_data', figure = 'Master')
plotter.addLayer('exp2_data', figure = 'Master')

#Create a single axes object at 11, containing our layers.
plotter.arrangeFig([1,1], {'11':
{'name': 'Two Experiments',
'scale': ([0,10],[-2,1.5]),
'layers': ['exp1_data','exp2_data'],
'axes_vars': ['x', 'y']}
})

gui.buildPlotter2D((8,8), with_times=False)

#Add list data to our layers.
plotter.addData(dataset1, layer='exp1_data', style='k-')
plotter.addData(dataset2, layer='exp2_data', style='r-')

plotter.auto_scale_domain(figure='Master')
plotter.show()

```

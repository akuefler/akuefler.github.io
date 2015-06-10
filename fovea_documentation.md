##INSTALLATION

##INTRODUCTION

Dynamical systems play an important role in many scientific fields, but models of such systems can feature many parameters and variables that are confusing to first time users. Fovea is a python package for assisting researchers in the process of building intuition about dynamical systems implemented in algorithms. The package consists of five files.

#####1. graphics.py
Fovea graphics provides a set of tools allowing the user to visualize the action of dynamical systems as they evolve over time, or as their parameters change. It adds a new level abstraction to the graphical inheritance structure used by Matplotlib.

![matplotlib graphical object hierarchy](https://raw.githubusercontent.com/akuefler/akuefler.github.io/master/images/mpl_hierarchy.png)

**Layers** are a new data structure that can be associated with the subplots, or axes, of a figure. Whereas axes may contain observed data, the Layers lie over these data like transparent slides and can be used to display an algorithm’s current parameter values or iteration step, hidden variables, or any kind of metadata important to the user. graphics.py includes two main classes:

_plotter2D_ creates the graphical components for specific calculations. It can be used to create and store new figures and Layers and generates artists placed upon them (data, text, axis lines, etc.). For example, two Layers containing clusters of data points observed during two separate experiments may be created and entitled as ‘exp1_data’ and ‘exp2_data’ using separate calls to the method addLayer, then the boundaries of the figure containing these layers may be resized with the method auto_scale_domain. The following code snipped demonstrates how layers can be initialized in a single axes object and used to store different types of data:

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

Note also that Plotters can be initialized with a diagnostic manager object, which ensure saves are stored on a path visible to the diagnostics tools.

_diagnosticGUI_ takes an instance of plotter2D in its constructor and provides user-interactivity with the graphics created by that instance. It creates appropriate widgets that can be used for exploring models (such as a slider for incrementing and decrementing time steps) and provides button callbacks for clicking on the axes. For example, the method getDynamicPoint lets the user click a subplot and store the point clicked on a clipboard for later access.

#####2. diagnostics.py

#####3. calc_context.py

#####4. common.py

#####5. domain2D.py

##COMMANDS

###Graphics

####Class Line_GUI
Line of interest context_object for GUI.

#####Methods:
######distance_to_pos(self, dist)
Calculate absolute (x, y) position of distance _dist_ from (x1, y1) along the line instance.

######fraction_to_pos(self, fraction)
Calculate absolute (x,y) position of fractional distance _fraction_ (0-1) from (x1, y1) along line

######make_event_def(self, uniquename, dircode=0)
Binds an event to this instance of line_GUI, which is triggered when the line is crossed by some trajectory. _uniquename_ determines the name of the event and _dircode_ specifies whether the event should be triggered by a crossing from the left, right, or either (must be -1, 0 or 1).

######unshow(self)
Make this line instance invisible.


####Class Plotter2D
Responsible for management and creation of Fovea layers and graphical objects contained inside them.

####Methods:

#####setLayer(self, label, figure=None, **kwargs)
Arrange data sets in a figure's layer

Change or reset properties of an existing layer named _label_. Note that plotter2D.show() must be called afterwards in order to update layer axes.

Valid kwargs:
_data_  
numpy array of numeric data

_dynamic_  
boolean variable

_zindex_  
_style_  
Maximum two character string indicating color and/or style of plotted data (e.g., "r." for a red dot) 

_display_  
boolean determining visibility of layer

_axes\_vars_  
List of strings labeling each axis.

_handles_  
Dictionary of mpl handles belonging to artists in the layer.

_trajs_
_scale_
_kind_
string indicating kind of information displayed in this layer (e.g., "text", "data").

##EXAMPLES

Bombardier

User key commands (defined in bombardier.GUIrocket.key_on):
_'g'_  
Runs the simulation with current angle and velocity

_'l'_  
Click and hold to create a new line_GUI object as a straight line in the current axes. The new instance of line_GUI is stored in GUIRocket.context_objects and game#.selected_object.

_' '_  
Creates a new selected objected at next clicked mouse point.

__'s'__  
Snap next clicked mouse point to closes point on trajectory.

__'.'__  
Click on domain seed point, then initial radius point.

##TUTORIALS

##README

##LICENSE



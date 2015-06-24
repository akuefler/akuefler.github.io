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
class polygon_domain(object):  

grow_step(self, verbose=False):
Iterates over all of the exterior coordinates of of the domain polygon. Each time, increments the values of the points by a certain step size to create a new point. If the domain criterion function, given this new point, returns a value with the same sign as it would return, given the seed point, the new point is stored. Once all the original polygon points have been compared using boolfunc, a new polygon is made out of all the satisfying points.

self.boolfunc (implicit method). Performs a comparison to see if the output of the domain criterion function for a given input (a point) shares the same sign as the output at the seed point.

##COMMANDS

###Graphics

####MISC NOTES:
fig_struct is a dictionary produced as the first output of self._resolveFig(figure), it contains the fields: 'domain', 'arrange', 'xlabel', 'window', 'display', 'layers', 'autoscaling', 'title', 'fignum', 'ylabel', 'tdom', 'shape'. These are properties of the master window/ figure itself. It can also be accessed within the plotter2D class with self.figs[figure_name], which is what ._resolveFig does.

    (_So why does resolveFig exist at all?_)

figure, output as the second argument of self._resolveFig(figure), is just a string. The name of the figure fig_struct describes (e.g., 'master')

layer_struct is a dictionary associated with a unique, named layer. It contains the fields: 'scale', 'kind', 'style', 'data', 'dynamic', 'zindex', 'axes_vars', 'trajs', 'display', 'handles'. These are the properties of the layer named in the call to self._resolveLayer(figure, layer). Each layer has its own struct.

The value of the 'data' key in layer_struct is a dictionary, whose keys are 'style' (string), 'display' (boolean) and 'data' (numpy array)

arrangeFig checks if positions of the subplots are consistent with the declared shape, and if so, stores shape and arrange as items in the fig_struct. These are used later to actually build the figure and plots.


####Class Line_GUI
Line of interest context_object for GUI.

#####Methods:
######distance_to_pos(self, dist)
Calculate absolute (x, y) position of distance _dist_ from (x1, y1) along the line instance.

######fraction_to_pos(self, fraction)
Calculate absolute (x,y) position of fractional distance _fraction_ (0-1) from (x1, y1) along line

######make_event_def(self, uniquename, dircode=0)
Binds an event to this instance of line_GUI, which is triggered when the line is crossed by some trajectory. _uniquename_ determines the name of the event and _dircode_ specifies whether the event should be triggered by a crossing from the left, right, or either (must be -1, 0 or 1).

####Class diagnosticGUI

####Methods:

####buildPlotter2D
Creates GUI based on the properties set with arrangeFig. Takes as input a fig_size (integer pair), which gives the width and height of the master window in inches, and with_times (boolean), which determines if a continuous "time slider" widget should be created. Creates core GUI buttons and sets up their callbacks.

subplot_struct contains the values for a subplot at a given position named in the call to arrangeFig

######unshow(self)
Make this line instance invisible.


####Class Plotter2D(figSize, with_times)
Responsible for management and creation of Fovea layers and graphical objects contained inside them.

List of plotter2D attributes:
_self.dm_  
Diagnostic manager object passed into constructor.

_self.save\_status_  
Boolean

_self.wait\_status_  
Boolean

_self.figs_  
Dictionary containing figure names as keys and fig_structs as values.

_self.currFig_  

_self.active\_layer\_structs_

####Methods:

####addData(self, data, figure=None, layer=None, style=None, name=None, display=True, force=False, log=None)
"""
User tool to add data to a named layer (defaults to current active layer).
*data* consists of a pair of sequences of x, y data values, in the same
format as would be passed to matplotlib's plot.

Use *force* option only if known that existing data must be overwritten.
Add a diagnostic manager's log attribute to the optional *log* argument
to have the figure, layer, and data name recorded in the log.

*display* option (default True) controls whether the data will be
visible by default.
"""

Creates a name for the data parameter in this layer, and adds it to layer_struct.data as a dictionary of dictionaries with the builtin update() method defined for dictionaries. The sub-dictionary's keys are 'data' (the np array passed in as an argument), 'style', and 'display'.

#####addLayer(self, layer_name, figure=None, set_to_active=True, **kwargs)
Add a layer to the figure. layer_names can be created or stored, but they can also be initialized with layer properties such as data, style, scale and kind.

Basically takes a new figure name, and associates with that string its own figure_struct

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

#####_subplots(self, layers, fig_name, rebuild= False)

Nested loop over shape provided in fig_struct. Retrieve the subplot_struct ('name', 'projection', 'layers', ... Things defined in arrangeFig) Each time through the loop.

##EXAMPLES

###Bombardier
Bombardier is a simple game that simulates the trajectory of a projectile as it travels past bodies with gravitational fields. The user sets the speed and angle of the projectile before each run and initiates the simulation by pressing the "Go!" button at the bottom left of the window (or pressing the hotkey 'g').

Four games are included in the Bombardier example folder (though currently only games 1, 2 and 4 are implemented).

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

####Class GUIrocket

####Methods:

#####get_forces(self, x, y)
Function used by mouse_event_force, tied to the ' ' callback.

##TUTORIALS

##README

##LICENSE



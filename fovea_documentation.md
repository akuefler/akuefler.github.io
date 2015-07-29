##INSTALLATION

##INTRODUCTION

Dynamical systems play an important role in many scientific fields, but models of such systems can feature many parameters and variables that are confusing to first time users. Fovea is a python package for assisting researchers in the process of building intuition about dynamical systems implemented in algorithms. The package consists of five files.

###1. graphics.py
Fovea graphics provides a set of tools allowing the user to visualize the action of dynamical systems as they evolve over time, or as their parameters change. It adds a new level abstraction, called a **Layer** to the graphical inheritance structure used by Matplotlib.

![matplotlib graphical object hierarchy](https://github.com/akuefler/akuefler.github.io/blob/master/images/fig_hierarchy.png?raw=true)

**Layers** exist as a way of of grouping together related data and meta-data so they can be manipulated together graphically. Each layer includes a number of attributes such as:
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

The attributes 'kind' and 'data' are related and especially import. 'Kind' determines the type of data that can be stored in the 'data' field, which is itself a dictionary of fields. For instance, a layer whose 'kind' is "patch" can contain multiple 'datas' (e.g., labeled 'data1', 'data2' and 'data3'), each featuring a 'patch' key whose value is a different matplotlib.patch object. The result could be data in 'data1' plotted as circles,  data from 'data2' plotted as squares, and data from 'data3' plotted as triangles.

The fields of the 'data' attribute of layers includes:

__data:__  
The raw data itself. This can be a numpy array containing numerical data or a LineCollection object.
__style:__  
String. The color and linestyle used to plot the raw data, following matplotlib's coding scheme (e.g., 'k-' will produce a black line).
__subplot:__  
String. If the parent Layer of the data  has been assigned to different subplots, this field is used to specify upon which axes the child dataset should be plotted. (e.g., '11' for the the subplot in the first row and first column)

graphics.py includes two main classes for managing GUIs, Layers, and the graphical objects stored inside them:

_plotter2D_ creates the graphical components for specific calculations. It can be used to create and store new figures and Layers and generates artists placed upon them (data, text, axis lines, etc.). For example, two Layers containing clusters of data points observed during two separate experiments may be created and entitled as ‘exp1_data’ and ‘exp2_data’ using separate calls to the method addLayer, then the boundaries of the figure containing these layers may be resized with the method auto_scale_domain. The following code snippet demonstrates how layers can be initialized in a single axes object and used to store different types of data:

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

#####GUI vs. Plotter

Together, _diagnosticGUI_ and _plotter2D_ provide the front and back ends of Fovea. The GUI class is intended as the user's point of entry to the system. It provides _addDataPoints()_ to enter pointSet data that may later be visualized with the plotter and comes built in with a number of "basic" widgets (the save, capturePoint, refresh and back buttons), which can be hidden by setting the optional parameter "basic_widgets" as False when the display is initialized with _buildPlotter2D()_. The suite of basic widgets can also be extended with the _addWidget()_ method, which saves the user the trouble of creating widget objects with matplotlib and adding them to GUI figures directly. For instance, a slider object (ranging from -10 to 10 and initialized at 0) can be added to the figure and connected to the user's callback (self.slideCallback):

```python
gui.addWidget(Slider, callback=self.slideCallback, axlims = (0.1, 0.055, 0.65, 0.03),
    label='my_slider', valmin= -10, valmax= 10,
    valinit= 0, color='b', dragging=False, valfmt='%2.3f')
```

The overall design of _diagnosticGUI_ emphasizes features general to any application, while giving the user the flexibility to patch in their own extensions. Another example is the _key\_on()_ method, which defines hotkeys and connects each to a callback (such as _mouse\_event\_snap()_ in _diagnosticGUI_ and _RectangleSelector_ in matplotlib). Some of these key commands tie into functions defined in the user's application. _diagnosticGUI.assign_user_func()_ takes a callable, such as a function created by the user, and stores this as an attribute of the GUI. This callable (user_func) will then be used by _mouse\_event\_user\_function()_ connected in _key\_on()_.

Although the actions of _plotter2D_ are largely intended to take place within the context of a GUI object, in advanced applications it may be necessary to set properties and give commands to the plotter directly. Currently, **layers** are implemented as a struct (layer_struct) whose fields are the layer's attributes (e.g. data, style, axes_obj) is an instance of _plotter2D_. Any of these properties can be set with _plotter2D.setLayer()_, which along with _addLayer()_, provide the user (at the command line or in user-functions) direct control over how data are displayed and managed in a GUI's subplots.

To curtail excessive calls to the plotter within the user's application, a number of wrapper methods and convenience functions have been defined for diagnosticGUI. The previous example for setting up a Fovea scenario can be simplified using these methods as follows:

```python
from fovea.graphics import gui

#Create a root figure
gui.addFig('Master',  
    title='Project Title',
    xlabel='x', ylabel='y',
    domain=([0,10],[-2,1.5]))

#Create a single axes object at 11, containing our layers.
gui.setup({'11':  
    {'name': 'Two Experiments',
     'scale': ([0, 10], [-2, 1.5]),
     'layers': ['exp1_data', 'exp2_data'],
     'axes_vars': ['x', 'y']}
    },
    size= (8, 8), with_times= False)

#Add list data to our layers.
gui.addDataPoints(dataset1, layer='exp1_data', style='k-')
gui.addDataPoints(dataset2, layer='exp2_data', style='r-')

gui.show()

```
Notice that the previous calls to _plotter.addFig()_ and _plotter.show()_ have been replaced with the equivalent _gui.addFig()_ and _gui.show()_ and _gui.addDataPoints()_ now takes the place of _gui.addData()_. Furthermore, the calls to _plotter.addLayer()_ have been removed altogether. Instead _addDataPoints()_, upon receiving 'exp1_data' and 'exp2_data' as layer arguments, understands that these are new layers to be added to the plotter and calls _plotter.addLayer()_ internally. _gui.setup()_ also combines _plotter.arrangeFig()_ and _plotter.buildPlotter2D()_ into a convenient function, as these two methods will often be called together. An added benefit to using _gui.setup()_ is that it is no longer necessary to specify the shape of the figure arrangement explicity. The function looks at the keys of arrPlots (its first positional argument) and infers the largest row column values should define the shape of the figure. For example, if the keys of the dict passed into _gui.setup()_ are '11', '12', '21', '22', the figure will be arranged 2-by-2.


#####Importing vs. Subclassing
When you are ready to use Fovea in your own setting, you have the option of importing a vanilla version of the GUI (```python with from fovea.graphics import gui ```), as we have already seen. Or you can create your own class, which subclasses diagnosticGUI. The vanilla version is a global singleton instance defined with a plotter2D object at the bottom of graphics.py. For problems that can be solved with the suite of tools built in for diagnosticGUI and plotter2D, using the imported GUI should be sufficient. However, if you wish to create your own methods that interact with diagnosticGUI's internal attributes, a customized gui can be easily created.

```python
from fovea import graphics

class customGUI(graphics.diagnosticGUI):
    def __init__(self, title):

        plotter = graphics.plotter2D()
        graphics.diagnosticGUI.__init__(self, plotter)

        self.fig = 'master'
        self.title = 'my_project'
        self.doi = [(0, 100),(0, 1)]

        name = 'layer_name'

        self.do_fovea_stuff(name)

    def do_fovea_stuff(self, name):
        self.addFig(self.fig,  
            title= self.title,
            xlabel='x', ylabel='y',
            domain= self.doi)

        self.plotter.addLayer(name)
```

The class customGUI initializes graphics.diagnosticGUI as its superclass with a plotter object. Any instance of customGUI can be acted on by diagnosticGUI and plotter2D methods such as _addFig()_ and _addLayer()_. In addition, new functions such as _do\_fovea\_stuff()_ can be defined by the user and used like any other diagnosticGUI method.

In some circumstances, it is necessary to create a custom object that subclasses diagnosticGUI. _diagnosticGUI.make\_gen()_ is a method for creating PyDSTool generator models, which may vary enormously in form, depending on the user's needs. As such, _make\_gen()_ is left empty and will raise a NotImplementedError if called by the global singleton gui. It is up the user to override this method (by defining in the subclass their own method of the same name), if they wish to associate their own PyDSTool model with the GUI. Similarly, _user\_nav\_func()_ will also be called when diagnosticGUI navigation keys (see next section, Callbacks) are pressed, even though it is left empty. The method is there to be overridden if some special behavior is desired during context object navigation.

#####Callbacks

###2. domain2D.py
In some applications, it may be necessary to monitor how the output of a function changes across the xy-plane. For instance, if a trajectory is being plotted through a vector field, the ability to highlight regions of the field where moving objects might get stuck could be invaluable.  _domain2D_ provides utilities for creating such "domains of interest" based on spatial variables in the xy-plane. It consists of two classes, _polygon\_domain_ and _GUI\_domain\_handler_, in addition to two functions, _edge\_lengths_ and _merge\_to\_polygon_.

_polygon\_domain_ is the fundamental shape object used in _domain2D_. An instance of _polygon\_domain_ includes a seed point c and a point p1, where the distance between c and p1 defines the initial radius of the polygon. The domain criterion function (saved as the attribute _condition\_func_) is a user provided, two variable function mapping x and y to a scalar value. The method _grow()_ uses these three attributes to iteratively expand the boundaries of the polygon, allowing it to swallow up nearby space where points within that space satisfy a certain condition. This condition is that the domain criterion function must output a scalar with the same sign given new points as it would given the c, the seed point. Note that the warning "f(p1) has different sign to f(c)" will occur if a p1 is chosen that fails to satisfy this condition. This behavior remains the same for all _polygon\_domain_s, but by providing different domain criterion functions, the user can tailor domains to meet their needs.

_GUI\_domain_handler_ provides the needed interface between Fovea's _domain2D_ and _graphics_ utilities. A domain handler object allows users to assign a domain criterion function created in their own application to the _polygon\_domain_ (using _assign\_criterion\_func()_.


class polygon_domain(object):  

grow_step(self, verbose=False):
Iterates over all of the exterior coordinates of of the domain polygon. Each time, increments the values of the points by a certain step size to create a new point. If the domain criterion function, given this new point, returns a value with the same sign as it would return, given the seed point, the new point is stored. Once all the original polygon points have been compared using boolfunc, a new polygon is made out of all the satisfying points.

self.boolfunc (implicit method). Performs a comparison to see if the output of the domain criterion function for a given input (a point) shares the same sign as the output at the seed point.

###2. diagnostics.py
###3. calc_context.py

###4. common.py

##COMMANDS

###Graphics

####MISC NOTES:
fig_struct is a dictionary produced as the first output of self._resolveFig(figure), it contains the fields: 'domain', 'arrange', 'xlabel', 'window', 'display', 'layers', 'autoscaling', 'title', 'fignum', 'ylabel', 'tdom', 'shape'. These are properties of the master window/ figure itself. It can also be accessed within the plotter2D class with self.figs[figure_name], which is what ._resolveFig does.

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

####addText(self, data, figure=None, layer=None, style=None, name=None, display=True, force=False, log=None)
Does NOT accept a list of text/list of position coords to create text en mass.

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



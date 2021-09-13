[![CircleCI](https://circleci.com/gh/pymoc/pymoc/tree/master.svg?style=shield)](https://circleci.com/gh/pymoc/pymoc/tree/master)
[![Test Coverage](https://api.codeclimate.com/v1/badges/b03ff00b5c86d7afc364/test_coverage)](https://codeclimate.com/github/pymoc/PyMOC/test_coverage)
[![Maintainability](https://api.codeclimate.com/v1/badges/b03ff00b5c86d7afc364/maintainability)](https://codeclimate.com/github/pymoc/PyMOC/maintainability)
[![PyPI version](https://badge.fury.io/py/py-moc.svg)](https://badge.fury.io/py/py-moc)
[![Documentation](https://img.shields.io/badge/docs-PyMOC-informational)](https://pymoc.github.io)
[![License](https://img.shields.io/badge/license-MIT-informational)](LICENSE)

PyMOC is a suite of python modules to build simple "toy" models for ocean's
Meridional Overturning Circulation (MOC). 

The model suite consists of several independent modules representing
various ocean regions and dynamics. Specifically, there are modules
for calculating the 1-D advective-diffusive buoyancy tendencies averaged 
over ocean basins, given the net isopycnal transports in and out of the column.
The isopycnal transports are computed as diagnostic  relationships, with modules
to calculate the wind- and eddy-driven residual circulation in a southern-ocean-like
re-entrant channel, as well as the geostrophic exchange between different basins or
basin regions. These modules may be coupled to study the circulation in a wide range
of different configurations.

The intended audiences for this model are researchers, educators and students
in the geophysical sciences. The goal is to provide an accessible
model appropriate for newcomers to geophysical modeling, but with physics
that reflect the current state of our theoretical understanding of the deep ocean
overturning circulation.

Configuration and execution of the PyMOC suite requires relatively little
technical knowledge or computational resources. All modules are written
in pure Python, and the only core dependencies are the NumPy and SciPy
libraries. If configuration of your base system environment is undesirable,
a preconfigured Docker container has been made available with all required
software libraries pre-installed. 

Anybody is more than welcome to contibute to the development of PyMOC,
but is asked to adhere to the goal of keeping PyMOC well tested, stable,
maintainable, and documented. Further details on installation, configuration,
contribution, and issue reporting is available in the [documentation](https://pymoc.github.io).

---
Update: Hailu Kong, 09/13/2021
--

Several more modules have been added, all for the Southern Ocean (SO) region, for the paper of:

> Time-dependent response of the overturning circulation and pycnocline depth to Southern Ocean wind stress changes

The added modules are:
```
psi_SO_varKGM_local.py
psi_SO_varKGM_bulk.py 
psi_SO_Kst_Ktr.py
psi_SO_Ktr_Tst.py
psi_SO_Keff.py
psi_SO_Keff_Ktr.py
psi_SO_Keff_Dc.py
```
The specific assumptions of these modules are:


1. `psi_SO_varKGM_local.py`

The SO diffusivity is parameterized as

<a href="https://www.codecogs.com/eqnedit.php?latex=\large&space;K_\text{GM}(z)&space;=&space;K_0\left[\frac{D(z)}{D_0}\right]^{n-1}" target="_blank"><img src="https://latex.codecogs.com/svg.latex?\large&space;K_\text{GM}(z)&space;=&space;K_0\left[\frac{D(z)}{D_0}\right]^{n-1}" title="\large K_\text{GM}(z) = K_0\left[\frac{D(z)}{D_0}\right]^{n-1}" /></a>,

i.e., it adopts the form of the diffusivity in Jones et al. (2011) but for each layer.


2. `psi_SO_varKGM_bulk.py`

This is the module that is used to compute the SO dynamics used in PyMOC-no-meander configuration, where only transient eddies are considered, and a bulk value of pycnocline depth is used to parameterize the diffusivity. Specifically, the SO diffusivity is parameterized as

<a href="https://www.codecogs.com/eqnedit.php?latex=\large&space;K_\text{GM}&space;=&space;K_0\left(\frac{D}{D_0}\right)^{n-1}" target="_blank"><img src="https://latex.codecogs.com/svg.latex?\large&space;K_\text{GM}&space;=&space;K_0\left(\frac{D}{D_0}\right)^{n-1}" title="\large K_\text{GM} = K_0\left(\frac{D}{D_0}\right)^{n-1}" /></a>,

i.e., it is the bulk value of D, instead of the depth of each layer, that is used to compute a vertically constant diffusivity.


3. `psi_SO_Kst_Ktr.py`
 
The SO diffusivity has two components: the transient & stationary parts

<a href="https://www.codecogs.com/eqnedit.php?latex=\large&space;K_\text{eff}&space;=&space;K_0\left(\frac{D}{D_0}\right)^{n-1}&space;&plus;&space;(K_1&space;&plus;&space;\alpha\cdot\tau_0)" target="_blank"><img src="https://latex.codecogs.com/svg.latex?\large&space;K_\text{eff}&space;=&space;K_0\left(\frac{D}{D_0}\right)^{n-1}&space;&plus;&space;(K_1&space;&plus;&space;\alpha\cdot\tau_0)" title="\large K_\text{eff} = K_0\left(\frac{D}{D_0}\right)^{n-1} + (K_1 + \alpha\cdot\tau_0)" /></a>

where the transient part remains the same as in `psi_SO_varKGM_bulk.py`. 


4. `psi_SO_Ktr_Tst.py`

Instead of first parameterizating the diffusivity before using it to compute the eddy-induced transport (i.e., <a href="https://www.codecogs.com/eqnedit.php?latex=\inline&space;\large&space;T_\text{eddy}&space;=&space;-K\cdot&space;s" target="_blank"><img src="https://latex.codecogs.com/svg.latex?\inline&space;\large&space;T_\text{eddy}&space;=&space;-K\cdot&space;s" title="\large T_\text{eddy} = -K\cdot s" /></a>), now we separately parameterize the eddy-induced transport by the transient and stationary eddies:

<a href="https://www.codecogs.com/eqnedit.php?latex=\large&space;T_\text{eddy}&space;=&space;-K_0\left(\frac{D}{D_0}\right)^{n-1}\left(\frac{z}{L_y}\right)L_x&space;&plus;&space;(T_1&space;&plus;&space;T_2\cdot\frac{\tau_0}{\tau_\text{ref}})" target="_blank"><img src="https://latex.codecogs.com/svg.latex?\large&space;T_\text{eddy}&space;=&space;K_0\left(\frac{D}{D_0}\right)^{n-1}\left(\frac{z}{L_y}\right)L_x&space;&plus;&space;(T_1&space;&plus;&space;T_2\cdot\frac{\tau_0}{\tau_\text{ref}})" title="\large T_\text{eddy} = K_0\left(\frac{D}{D_0}\right)^{n-1}\left(\frac{z}{L_y}\right)L_x + (T_1 + T_2\cdot\frac{\tau_0}{\tau_\text{ref}})" /></a>

where the last parenthesis denotes the linearly approximated SOMOC transport by the stationary eddies.


5. `psi_SO_Keff.py`

This is the module used for the PyMOC configuration in the paper, where the SO diffusivity is parameterized via a stretching of the transient diffusivity by stationary eddies:

<a href="https://www.codecogs.com/eqnedit.php?latex=\large&space;K_\text{eff}&space;=&space;K_0\left(\frac{D}{D_0}\right)^{n-1}\cdot\left(1&plus;\alpha\cdot\frac{\tau_0}{\tau_\text{ref}}\right)" target="_blank"><img src="https://latex.codecogs.com/svg.latex?\large&space;K_\text{eff}&space;=&space;K_0\left(\frac{D}{D_0}\right)^{n-1}\cdot\left(1&plus;\alpha\cdot\frac{\tau_0}{\tau_\text{ref}}\right)" title="\large K_\text{eff} = K_0\left(\frac{D}{D_0}\right)^{n-1}\cdot\left(1+\alpha\cdot\frac{\tau_0}{\tau_\text{ref}}\right)" /></a>


6. `psi_SO_Keff_Ktr.py`

This is the most complicated parameterization for the SO effective diffusivity, where the dependence on the wind stress appears both in the stretching factor, as in the module introduced above, and in the transient diffusivity part, to mimic the impact of rapid change in local baroclinicity on the transient diffusivity:

<a href="https://www.codecogs.com/eqnedit.php?latex=\large&space;K_\text{eff}&space;=&space;\left\{K0\left(\frac{D}{D_0}\right)^{n-1}&space;\cdot&space;\left[c_1&plus;c_2\left(\frac{\tau_0}{\tau_\text{ref}}\right)\right]^{\frac{n-1}{2}}\right\}\cdot\left[c_1&plus;c_2\left(\frac{\tau_0}{\tau_\text{ref}}\right)\right]" target="_blank"><img src="https://latex.codecogs.com/svg.latex?\large&space;K_\text{eff}&space;=&space;\left\{K0\left(\frac{D}{D_0}\right)^{n-1}&space;\cdot&space;\left[c_1&plus;c_2\left(\frac{\tau_0}{\tau_\text{ref}}\right)\right]^{\frac{n-1}{2}}\right\}\cdot\left[c_1&plus;c_2\left(\frac{\tau_0}{\tau_\text{ref}}\right)\right]" title="\large K_\text{eff} = \left\{K0\left(\frac{D}{D_0}\right)^{n-1} \cdot \left[c_1+c_2\left(\frac{\tau_0}{\tau_\text{ref}}\right)\right]^{\frac{n-1}{2}}\right\}\cdot\left[c_1+c_2\left(\frac{\tau_0}{\tau_\text{ref}}\right)\right]" /></a>


7. `psi_SO_Keff_Dc.py`

Finally, this module parameterizes the SO effective diffusivity in a similar way as `psi_SO_Keff.py` except that it is the cut-off version of D (i.e., D_c), instead of the full-depth-based D, that enters the parameterization:

<a href="https://www.codecogs.com/eqnedit.php?latex=\large&space;K_\text{eff}&space;=&space;K_0\left(\frac{D_c}{D_0}\right&space;)^{n-1}\cdot\left(1&plus;\alpha\frac{\tau_0}{\tau_\text{ref}}\right)" target="_blank"><img src="https://latex.codecogs.com/svg.latex?\large&space;K_\text{eff}&space;=&space;K_0\left(\frac{D_c}{D_0}\right&space;)^{n-1}\cdot\left(1&plus;\alpha\frac{\tau_0}{\tau_\text{ref}}\right)" title="\large K_\text{eff} = K_0\left(\frac{D_c}{D_0}\right )^{n-1}\cdot\left(1+\alpha\frac{\tau_0}{\tau_\text{ref}}\right)" /></a>

Here D_c is computed similarly to D but its lower integral limit is cut off at the AMOC bottom buoyancy, which varies with time. 

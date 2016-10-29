Atooms
======

This is atooms - a high-level framework for atomistic simulations. 

Getting started
---------------

Accessing particles' coordinates in a multi-configuration xyz trajectory file goes like this
```python
from atooms.trajectory import Trajectory

for system in Trajectory('input.xyz'):
    print system.particle[0].position
```

Let us compress the final configuration of the trajectory to unit density
```python
rho = 1.0
with Trajectory('input.xyz') as trajectory:
    system = trajectory[-1]
    factor = (system.density / rho)**(1./3)
    for particle in system.particle:
        particle.position *= factor
    system.cell.side *= factor
```
Actually, the System class has a ```rescale()``` method to do just that. 

We create a new trajectory file with just the rescaled configuration
```python
from atooms.trajectory import TrajectoryXYZ

with TrajectoryXYZ('output.xyz', 'w') as trajectory:
    trajectory.write(system, step=0)
```

To start a simulation from the rescaled configuration, we pick up a simulation backend and pass it to a Simulation object
```python
from atooms.backends.dryrun import DryRunBackend
from atooms.simulation import Simulation

backend = DryRunBackend(system)
sim = Simulation(backend)
sim.run(steps=10000)
print sim.system.temperature, sim.system.density
```
Of course, we just pretended to do 10000 steps! the DryRunBackend won't do any actual simulation, nor write anything to disk. Check out the available backends or make your own!


Features
--------
- High-level access to simulation objects and their properties
- Handle and convert multiple trajectory formats 
- Generic simulation interface with callback logic
- Efficient molecular dynamics backends, e.g. RUMD

Adding packages to atooms namespace
-----------------------------------
If you want to add your package to the atooms namespace, structure it this way

```bash
atooms/your_package
atooms/your_package/__init__.py
```

where ```__init__.py``` contains

```python
from pkgutil import extend_path
__path__ = extend_path(__path__, __name__)
```

Add the package root folder to $PYTHONPATH. Now you can import your package as

```python
import atooms.your_package
```

Authors
-------
Daniele Coslovich <daniele.coslovich@umontpellier.fr>

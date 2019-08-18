# n-Body Problem: simulating motion and searching orbits
Here I represent some of the python code I write for my MSc dissertation - a project about simulating n-body system and finding Choreographies orbits. Codes in `nbody` only contains the main functions and those written for drawing plots and
conducting numerical test is not concluded.  

The code will basically have two big part: one part is using different integrator to numerically solve Newtonian motion eqution in 2D and simulate the trajactory of each body in the system. The simulator can take different number of bodies, masses, integrator, step size and interaction force. Another part is used to find Choreographies, which is a special kind periodic solution to n-body problem.

## Requiements 
The examples and package make use of the following commonplace python modules:
* Python 3
* numpy 1.6+
* Scipy 1.x

## Usage 
### Motion simulation
Once you have the initial postion and velocity of a n-body system you can try the following codes to simulate the trajectory of each body in the system. First you should
```python
import nbody
```
Next, you can simulate the path by using a call like
```python
q_traj,p_traj, e_traj, t_traj ,L_traj= motion_simulation(q0, p0, M, Nsteps, h, Verlet, G_force)
```
Output _q_traj_, _p_traj_ are 2-D array tracing the position and velocity of all the particles in the system . Each row in the _q_traj_ represents the particles' postion at center time and there are in total `Nsteps` rows in the array. The structure of each row is like `(x1,y1,x2,y2,...,xn,yn)`. _p_traj's_ rows has the same structure. _e_traj_, _t_traj_, L_traj_ are the 1-D array storing the total energy, time, angualr momentum along the time. One can use the following call to plot the trajectory of each body in the system.
```python
plt.plot(q_traj[:,::2],q_traj[:,1::2])
plt.show()
```
![alt text](https://github.com/JingyaoDOU/n-Body-simulator/blob/master/git01.png)  
The full list of options for the simulator are:
```python
q_traj,p_traj, e_traj, t_traj ,L_traj=motion_simulation(q0, p0, M, Nsteps, tlim=1e10, h, step_function, force_function, adaptive=False, lamda=50)
```
where:  
   `q0`: The initial postion of all the particles in the system, having structure like (x1,y1,x2,y2,...,xn,yn)  
   `p0`: The initial velocity of all the particles in the system, having structure like             (vx1,vy1,vx2,vy2,...,vxn,vyn)  
   `M`: The array of mass of all particles in the system, having a shpe like (m1,m2,...)  
   `Nsteps`: Number of total steps for the simulation.  
   `tlim`: Time limit for simulation to stop. This parameter is used when one uses variable step size integrator and want simulates the motion of particle for a certain time.    
   `h`: The step size.  
   `step_function`: The type of integrator: Euler, Verlet, Leapfrog, RK4.  
   `force_function`: The type of interactive force between each body, for now only one type: G_force.  
   `adaptive`: Whether use variable step size method. Note can use with Leapfrog method.   
   `lamda`: The parameter control the adaptive step size. The larger lamda, the smaller the time step.  
### Perturbation of figure-eight orbits  
As mentioned in the dissertation, we use two criterion to measure the stability of perturbed figure-eight three-body system:  
* The time tau when maximum mutual distance exceeds 10  
* The number of pixel boxes N of the "phase transformed" orbit.  
Given the perturbation dx, dy these two criterion can be computed using call
```python
tau, Nbox = stability_check(dx, dy, M, Nsteps,tlim, h, step_function, force_function, adaptive=True, lamda=50)
```
### Search simple choreographies
If one has an initial guess _x0_ for all the coefficients in the Fourier series, using following call, one can get the optimized coefficients and thus find a choreography.
```python
res = minimize(Action_function, x0, Nnm, method='CG', options={'disp': True})
```
where:  
* `x0`  The initial starting coefficients. 1-D arry with structure (0, n, n, 0, n, n), n here representing n coefficients. so the total length is 4n+2. 
* `Nnm` A tuple (N, n, m) with N the number of bodies, n the order of harmonics and m number of terms in numerical approx to integral.  
If you want to find a figure-eight like orbits, fast fourier transform is used to generated proper initial coefficients by the following call:  
```python
x0=FFT8(n)
```
Where _n_ is the order of harmonics you would like to use.  
What's more, if you want to generate initial coefficients for any other type of orbits, use call like:
```python
x0=FFT(n,x,y)
```
where _x_ and _y_ are 1-D array storing postion of the _n_ points on the orbit.

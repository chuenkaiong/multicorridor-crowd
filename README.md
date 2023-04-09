# Crowd simulation in Multi-Corridor systems 
## Problem Description 
todo 

## How the model works 
### Basics 
- White patches are traversable. Each patch represents a 0.5 x 0.5m floor space. 2 agents can fit on one tile, represnting the upper limit on crowd density of 8 people per square meter.
- Black patches represent the walls of the corridor system, and obstacles within the corridors.
- Blue patches represent entrances to the MCS. They will spawn people up till a cap of total number of people is reached. 
- Live people are green circles. 
- Dead people are grey circles.
- The attraction is a light blue patch. 

### Floor fields 
In order to reduce the load on computation, the model uses the [Kirchner floor fields model](https://www.sciencedirect.com/science/article/abs/pii/S0378437102008579) as adapted by [Henein & White](https://link.springer.com/chapter/10.1007/978-3-540-32243-6_14) to account for forces in crowds. Further modifications are made to the Henein model to account for bidirectional pushing in crowds, which results in 0 net force on individuals but nonetheless a great amount of experienced pressure which may result in injury. 

The floor fields are divided into several components: 
- A **static** component which indicates the attractiveness of the patch
    -  TODO - nonlinear function to create greater attractiveness gradient further from the attraction 
- A **dynamic** component that updates similar to an ant pheromone model, which encourages agents to follow other agents (simulating crowd following behavior). 
- A **force** component, which indicates the force in each patch. Force propagates from one occupied patch to another, simulating the waves of force and movement often seen in large crowds. The Henein model uses a 2d vector to indicate force, and force propagation is done via vector addition. This was suitable for the situation simulated in that paper, being one-way pushing towards an exit. Our model modifies this approach for two-way pushing towards an attraction, by maintaining force fields for each cardinal direction, such that forces converging on a patch do not cancel out but accumulate instead. 

### Agent behavior 
At each timestep, agents attempt to move to patches with the highest desirability score. Patches determine their desirability score according to the Kirchner model: 

$$Score(i) = exp(k_dD_i) \times exp(k_sS_i) \times \xi_i \times \eta_i $$

Where:
- $D_i$ is the value of the dynamic field in that cell, and $S_i$ is the value of the static field in that cell; 
- $k_d$ and $k_i$ are scaling parameters governing the degree to which agents are inclined to follow others or move towards the attraction, respectively;
- $\xi_i$ is 0 for forbidden (non-pathable) cells, and 1 otherwise;
- $\eta_i$ is 1 if a cell is unoccupied, and some adjustable value between 0 and 1 otherwise. 

If an agent is unable to move to its desired cell (if the cell is occupied by a number of people as determined by the permissible crowd density slider), it will push (exert a force on) that cell. The pushing force is a constant, determined by biomechanical studies. 

At the end of every tick, each agent will push to maintain its space. It will push outwards in each cardinal direction with 0.25 of its maximum pushing force.


### Effects of force on agents 

If the force on an agent's patch exceeds its maximum pushing force, the agent's ordinary cell-selection mechanism is overridden, and it will be forced to select the cell in the direction of the force. 

Agents suffer injuries based on certain force thresholds. **TODO - readme for agents' response to pressure**.

Dead agents are identical to agents in terms of occupying space and transmitting force, but will not exert any force of their own. 

### Future improvements 
- Pushing cooldown or enthusiasm meter, because people in a crowd are not necessarily pushing with their full strength all the time (a lot of papers do this). 
- Pushing force of individuals near walls can be increased by some amount, as biomechanics papers have described the increased force output that a person can generate when braced against a hard surface. 
# Crowd simulation in Multi-Corridor systems 
## Problem Description 
todo 

## How the model works 
### Basics 
- White patches are traversable
- Black patches represent the walls of the corridor system 
- Blue patches spawn people up till a cap of total number of people is reached 
- People are circles
- The attraction is a star 

### Agent behavior 
- Agents generate a force vector based on two competing desires: 
    - Desire to get closer to the attraction (creates force directly towards attraction)
    - Desire to maintain personal space (creates force away from nearest other person within the personal space radius)
    - (For later) Desire to get to an exit, which starts when crowd forces start to get uncomfortably strong and increases when people start dying
- Each person exerts a force that is equal to the sum of these component forces, but can only generate a certain maximum amount of force (amount TBD from research)
    - We may need to implement some pushing cooldown or enthusiasm meter, because people in a crowd aren't pushing all the time (a lot of papers do this)
- Unimpeded agents proceed at a set speed (for simplicity) in the direction of their force vector 
- People which are touching another person will transmit force to them based on the other person's bearing from oneself (N,E,S,W)
    - E.g. if a person is exerting force 1N and 1E, a person touching it, to its NE, will receive 1N and 1E force, and a person touching it to its SW will receive no force. 
    - Force may need to be split among different agents if multiple are being touched. Direction of force also needs to be worked out. 
- The total force exerted by a person is the sum of the forces transmitted through them, and the voluntary force exerted by the person themself. 
- Whenever a person or wall receives a force, it will exert an equal force in the opposite direction (Newton's 2nd Law). 
- Walls also generate a normal reaction force 
    - If this is unachievable with patches, we may have to create square-shaped patches that act as walls. 
# SARSOP

This Julia package wraps the [SARSOP](http://bigbird.comp.nus.edu.sg/pmwiki/farm/appl/) software for offline POMDP planning. 
It works with the [POMDPS.jl](https://github.com/sisl/POMDPs.jl) interface.
A module for writing POMDPX files is provided through the [POMDPXFile.jl](https://github.com/sisl/POMDPXFile.jl) package, and is a dependancy for SARSOP.jl. 

At the moment, only linux and OSX are supported.

## Installation
To install the wraper and SARSOP run the following command:

```julia
Pkg.clone("https://github.com/sisl/SARSOP.jl")
```

This will download the SARSOP.jl wrapper, and build the SARSOP backend software. If you are having problems with the
software try building SARSOP from source on your own. 

You also need to donwload the POMDPXFile.jl module. This module handles writing and reading the .pomdpx files taken and
generated by the SARSOP backed solver. To do that, simply run the following command:

```julia
Pkg.clone("https://github.com/sisl/POMDPXFile.jl")
```

## Usage
### POMDPs
```julia
using SARSOP
# run Pkg.clone("https://github.com/sisl/POMDPModels.jl.git") to get this module
using POMDPModles # this contains the TigerPOMDP

# If the policy file already exists, it will be loaded by default
policy = POMDPPolicy("tiger.policy")

# If the .pomdpx file exists call: pomdpfile = POMDPFile("\path\to\file") 
pomdp = TigerPOMDP() # this comes from POMDPModels, you will want this to be your concrete POMDP type
pomdpfile = POMDPFile(pomdp, "tiger.pomdpx") # second arg is the file to which .pomdpx will be writeten

solver = SARSOPSolver()
solve(solver, pomdpfile, policy) # no need to use solve if "mypolicy.policy" already exists

# Policy can be used to map belief to actions
ns = n_states(pomdp) # implemented by user
b = initial_belief(pomdp) # implemented by user
a = action(policy, b) 

# simulate the SARSOP policy
simulator = SARSOPSimulator(5, 5)
simulate(simulator, policy, pomdpfile)

# evaluate the SARSOP policy
evaluator = SARSOPEvaluator(5, 5)
evaluate(evaluator, policy, pomdpfile)

# generates a policy graph
graphgen = PolicyGraphGenerator("Tiger.dot")
polgraph(graphgen, policy, pomdp)

```

### MODMPs
To use with mixed observability Markov decision processes (MOMDPs) make sure to clone [MOMDPs.jl](https://github.com/sisl/MOMDPs.jl). The syntax takes the following form:

```julia
using SARSOP

# If the policy file already exists, it will be loaded by default
policy = MOMDPPolicy("mypolicy.policy")

momdp = MyMOMDP() # initialize your pomdp model
pomdpfile = MOMDPFile(momdp, "\path\to\write\to") # second arg is the file to which .pomdpx will be writeten

solver = SARSOPSolver()
solve(solver, pomdpfile, policy) # no need to use solve if "mypolicy.policy" already exists

# Policy can be used to map belief to actions
ns = n_states(momdp) # implemented by user
b = initial_belief(momdp) # implemented by user
x = fully_obs_var(momdp, create_state(momdp)) # returns the fully observable varaible (implemented by user)
a = action(policy, b, x) 
```

# Project Goal
Create a tool which minimizes time required for QA while maximizing quality by:

1. Using various common sources and adapters to create a high-level model of the system under test, including
   former system states and real-world usage data
2. Identifying potential blast radius of code changes by updating and analyzing the model for each change
3. Creating tests using combinatorial methods
3. Automatically automating and executing the tests

### Keywords
Model-based Testing (MBT), Combinatorial Testing, Backward Compatability Testing, Operational Profiling, Selective Regression Testing, Agentic AI

# Motiviation
Desire to prove a viable approach to making QE optimally efficient without sacraficing quality. Especially useful in an AI code generation world. 

# Project Status
Architecting phase :)


# Approach
## Assumptions
1. System has been sufficiently tested to-date and can be used as a "source of truth" to base initial model upon (when paired with initial human review)
2. System may or may not have feature flags guarding portions of UX 
3. System may or may not have user data tracking. Where it does not, substitute with something which 
   still provides contextual value/adds weights for importance of a given path through the system for the business. 


## Steps
1. Systematically gather and store important "event" sources (requests, responses, reads, writes) in system
   - For each component, (e.g. iOS app, server):
     - Extract each function which causes inputs/outputs which cross the component boundary (e.g. app sending request to server)
       - For each of these functions, gather all event params and where they can be called from/where they call
       - include data tracking if present 
   - [Model #1] Use these to create first pass of a system model 
   - Agentic AI may be able to be deployed to automate this
2. Map how to excercise each param value (within param value range) for event from each client component
   - E.g. clicking an element with specific UI selector + rendered image on app to cause an event
     - include prerequisite events
   - account for feature flags
   - [Model #2] Encode info on model 
3. Connect data systems (or equivalent contextual weighting system) to create user journey info (sequences the above in real life)
   - [Model #3] Encode on model
4. Review model and prune as needed [Model #4]
   - record any pruning to avoid adding it back in the future
5. For system changes, automatically identify all impacted functions (+ 1 level of surrounding) and create partial model [Model #5] by extracting these from model #4. Create new partial model [Model #6] using new changes only
  - Remember to maintain old models representing former systems over time to reflect former user states 
6. Take diff between the Model #5 and Model #6 to identify areas needing to have "how to exercise" updated (delta) and create [Model #7] by writing Model #6 onto Model #5 
7. Review model and prune [Model #8]
8. Use Model #8, delta, former models, to create e2e meta tests for each function in the system
   - for now, be safe and naively include all function callers and callees in blast radius requiring testing
   - algorithm should be configurable
   - each even param should be accounted for and reasonably exercised according to configurable priority
   - tests should account for user journey info and test X most popular variations up to Y events preceding/following an event,
     where and X and Y may also depend on separately identified "user personas" which account for historical system state. Consider distance-based weighting.
9. Update "how to exercise" for newly updated/created events, creating [Model #9]
10. Automate test creation for these areas (consider user states from former system states as needed as well)
11. Execute and record video or screen shots for manual or automated visual regression review
12. Repeat #5-10 for all future changes, using the model output from #7 as input

## Additional Notes
- Need to beware of and catch/warn of new params passed through (could affect something many components downstream)
- Handle pub/sub similar to read/write and request/response
- yield

# Project Structure
### Keywords
Hexagonal Architecture, Finite State Machine


- Main program acts as a state machine/driver which sets up and drives adapters, transformers, models
  - Adapters interface with the outside world (reading source code, ingesting data, writing automation)
    - write to outputs
      - Outputs include automated tests, saved model data, reports, etc.
    - are read by transformers
  - Transformers read/write data between models (and themselves)/adapters
  - Models represent the SUT with enhanced context per model iteration


# TODOs
- Negative testing
- Refine how to determine blast radius for changes in order to improve effiency
- handle internal state changes (e.g. user is banned from platform by company)

# Why Open Source?
- Benefits the world
- Quickest path to adoption, especially since access to SUT source code makes the project more viable. 

# Contact
For questions or comments please email me at mitchell@efficientqe.com

# License
This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

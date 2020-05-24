# Service Design
## Application Style
### Event Sourcing
Outputs/Events of services are stored in a read-only log and state is reconstructed by replaying events. Events are the source of truth

### Command Sourcing
Extension of event sourcing but inputs/commands are also stored in the event log to be able to rollback and reexecute the command, e.g., after a software bug.
But commands should normally be ignored during replay, e.g., it would be wrong to create a new order with each service restart.

### Command Query Request Segregation (CQRS)
Separate write and read path and link them with an asynchronous channel.

Advantages:
- both sides can be optimized independently.
- there can be many different read-models optimized for a use case

Disadvantages:
- due to the asychronous update of the read-model it can be that the write is not immediately visible in the read-model.

## Process Management
### Choreography aka Event Collaboration: 
No single component is responsible for process. Each service acts independently.

Advantages:
- new components can be added without affecting/changing other components

Disadvantages:
- status of process is not easily visible
- not clear which component reacts on what event

### Orchestration aka Request Collaboration:
Single component (process manager) is responsible for process and delegates work to other services

Advantages:
- single point where process is visible, i.e., the sequence is easier to guess

Disadvantages:
- new component/functionality requires changing the process controller => more dependencies

## Service Communication
### Data on the inside vs. data on the outside
Good article: [Pat Holland](http://cidrdb.org/cidr2005/papers/P12.pdf)

Important points:
- data on the inside may only changed by owning service
- data on the inside is easy to change, data on the outside is hard to change

Analogy:
Your thoughts in your brain are yours and nobody should change them (data on the inside). Thoughts can change easily. If you write down your thoughts on paper (data on the outside) and give this paper to another person then it is hard to change.

### Communication
#### Events may have two different roles
- notification: Communicate fact that something has changed which might trigger some actions, e.g., TemperatureChanged, OrderCreated etc
- state Transfer: Give other components a (read-only) copy (subset) of data owned by a component, e.g., customer address

#### Style
- within Bounded Context: Communication may be event-based or request-based
- between Bounded Context: Communication should be event-based and asynchronous. Share as little as possible!

## Event Storage
Storage or specific topic belongs to a certain service. 

What should be stored? Whole fact or delta? Rule of thumb: Record what one has received in an immutable fashion

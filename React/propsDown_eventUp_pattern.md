### Understanding the Parent-Child Communication Pattern
##### lift the state up

- The parent component owns and manages the state
- The parent passes the current state value down to children as props
- The parent also passes down functions that allow children to request state changes
- Children call these functions when they need to update the state
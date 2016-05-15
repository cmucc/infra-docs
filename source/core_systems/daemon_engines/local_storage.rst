Local storage
================================================================================

A user account on a daemon engine does not have a home directory in AFS,
though AFS should still be installed on the engine and available.
Instead, home directories are located on local storage and are not synchronized.

What is the value of home directories located in AFS?
With AFS home directories,
it is possible to load-balance incoming ``ssh`` connections to several shell machines
and rely on the synchronized home directory to make those shell machines seem identical.

But when one has long-running processes, this is no longer possible automatically.

Also, using AFS as storage for daemons has a number of pain points:

- Distributed systems issues where multiple systems share the same configuration
- AFS token expiration preventing processes from accessing their storage space
- Network outages causing unnecessary disruption
- AFS just being generally slow

So, we decided not to use it for the daemon engines.

Local Configuration And Computer Club
==========================================================================

Configuration is evil!
We should try to never do it.
Likewise with customization.

We should strive to not require internal documentation.
The way to get documentation about our systems should be in most cases "Google".

Configuration, customization, and a need for internal documentation are all normally bad,
but they're *especially* bad for an organization like us.
Each bit of special sauce in our infrastructure,
each additional piece of complexity,
is one more thing that a new maintainer has to learn to get anything done,
one more hurdle that a new member has to jump over before starting a new project.

New features can be actively harmful,
by increasing the amount of stuff to integrate with for new projects.
If we really want to add something new,
we should strive to add things that *automatically* bring benefits to users and developers,
without requiring them to perform extra work.
Or things that add new, orthogonal capabilities.

Things to consider when building something new:

- Doing things The Right Way
- Absence of configuration
- Absence of customization
- Using standard things
- A preference for less abstraction
- Don't use proprietary software, obviously

These should be in some order, but I'm not sure what order...

If there's a problem with something,
some bit of a project that is raw,
something that's unfinished,
don't immediately just try and fix it with the most high-powered or obvious tool!
Leave it broken, rather than over-engineer the project!

Perhaps someone later will come in and fix this problem;
or perhaps (more likely and usefully)
you will be hit with some inspiration
as you think about the problem for long periods of time,
and will come up with a clever, simple, elegant, beautiful solution,
rather than something overengineered.

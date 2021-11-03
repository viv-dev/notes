# game-dev

## Entity Component Systems

Major engines that use an entity component system are typically implementing some variation of the following pattern:

https://gameprogrammingpatterns.com/component.html

However, this can lead to some inefficient memory layouts.

Some argue, that a better implementation of an ECS is using something more akin to the following:

https://gameprogrammingpatterns.com/data-locality.html

This allows component objects to be grouped in contigous areas of memory, where updates for components can be iterated over in a much more efficient way.
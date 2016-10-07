## XedinUnknown - DI ##
[![Build Status](https://travis-ci.org/XedinUnknown/di.svg?branch=master)](https://travis-ci.org/XedinUnknown/di)

A demonstration of how it could be possible to formalize the [delegate lookup](https://github.com/container-interop/container-interop/blob/master/docs/Delegate-lookup.md) feature
via a PHP interface, and at the same time not enforce a setter onto the implementation.

### Features
- Full working example of DI implementation;
- Standards-compliant;
- One interface with one method - all that you need to delegate;
- Lookup logic up to composite container implementation;
- Unlimited level of nesting.

### How it works
1. The container that needs to delegate lookup implements [`ParentAwareContainerInterface`](https://github.com/XedinUnknown/di/blob/master/src/ParentAwareContainerInterface.php#L8).

This allows implementations to decide how exactly to delegate lookup, while still having a contract.  
This also allows infinite nesting of containers.

2. When the delegating container implementation invokes the service definition, it [passes](https://github.com/XedinUnknown/di/blob/master/src/AbstractParentAwareContainer.php#L67) the root container to the definition.

The root container is the top-most parent.

3. The definition uses whatever is passed to it for referencing another definition.

The definition is agnostic of any nesting, of what kind of container it is registered in, or of what kind of container is passed to it.

4. If the container passed to definition is a composite container, it [uses](https://github.com/XedinUnknown/di/blob/master/src/AbstractCompositeContainer.php#L50) the [first child with matching definition](https://github.com/XedinUnknown/di/blob/master/src/AbstractCompositeContainer.php#L68).

This is what facilitates the lookup delegation to other containers.

5. The container passed to definition may also be a regular container, in which case that precise container will be used.

Like this, if the container has no parent (or is not a parent-aware container), it will perform lookup on itself.

6. This means that there can always be one root container registered with the application.

Modules can hook up their own child containers that would perform delegation. This way, all the lookup is completely transparent to the service definitions, and to the child containers themselves.

7. This also means that the containers can compliment one another.

Or, the order of [definition "matching"](https://github.com/XedinUnknown/di/blob/master/src/AbstractCompositeContainer.php#L66) on containers can be reversed, in which case definitions added later will "override" those added earlier.

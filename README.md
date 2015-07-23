# transaction-propagator-api

## Introduction

Transaction propagator helps executing code within transactional blocks
by implementing functional interfaces. The propagator supports all known
methods: _required_, _requiresNew_, _supports_, _never_, _mandatory_ and
_notSupported_.

The JTA based implementation of the API is available at
[transaction-propagator-jta][1] project.

## Usage examples

    Integer result = transactionPropagator.requiresNew(() -> {
        // Do some stuff in the new transaction
    });

## History

The project is the continuation of [transaction-helper][2]. If you want to
use older Java versions, please check the older versions
of [transaction-helper][2].

## Download

The artifact is available on [maven-central][3]

## Why not annotations, interceptors or other magic?

By using this transaction helper, the trace of the code is always clear. We
used Spring interceptors, Spring annotations, Aries interceptors in the past,
but we decided not to use such "magical" solutions anymore.

Imagine the technology uses java proxies. In that case the programmer of the
class cannot be sure in which way the call stack came. Did the call come
from the same object but via a method that was not intercepted? Did it
come via a method that was intercepted? As a solution, many programmers
start intercepting all of the public methods of their class that is a very
bad behavior.

Ok. We found out that there is ASM, CGLIB, whatever... We could use them
to inherit from the class and with that, we can override all of the public
and protected functions. Problem solved. Interception is done during all
function call. What about the private functions?

Ok. We found out that there is Weaving. Private functions can be intercepted
as well. It does not matter if the system starts up really slow. It does not
matter that the hash of the class is not the same as it is at compile time.

What if I want to wrap a block of code inside an iteration with requiresNew
transaction propagation? Do we really have to create a new function for that?
And even if We do so, We cannot be sure that the new function is intercepted
if the technology uses simple Java Proxy classes.

What about the bytecode compatibility? Will version X of ASM work with
version Y of Java? Well, we saw it a couple of times that it did not. So we
had to wait for a new version of ASM to be able to use a bugfix version of
Java.

And especially in the OSGi world: What makes you sure that the bundle that
does the weaving is started before your bundle that should be weaved?

Is it so much to ask to use an anonymous class? There will be more code, but
still the code will be much more clear! It will be yours. With Java 8, the
code will be even more nice if you use lambda expressions.

[1]: https://github.com/everit-org/transaction-propagator-jta
[2]: https://github.com/everit-org-archive/transaction-helper
[3]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22org.everit.transaction.propagator%22
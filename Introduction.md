# Introduction #

GDB is a useful tool for debugging. GDB provides a interactive command-line to help the user to communicate
with a running binary, so that people can use it to control the execution of a binary as well as to reveal run-time
details. However, multi-threaded programs are so dicult to debug, and is considered as a big challenge even
for tools as powerful as GDB. Instead, we want to tackle this problem via another useful instrumentation tool,
Pin. Instrumentation tool such as Pin is more
exible than GDB to the extent that it can intercept user-specied
instructions and allows user-dened code to be executed.
To use Pin, the user should rst write a program called Pintool, which denes where and what to insert into a
binary after each instruction inception. However, Pin doesn't have dedicated API to create an interactive interface
as GDB, neither does it have a convenient way for the user to control the execution of binaries. In order to benet
from both Pin's
exibility and GDB's ease of use, we decide to write a Pintool to do the basic multi-threaded
debug, and implement a GDB-like interactive interface in it


# Details #

We are going to write a prototype Pintool that provides several functions. As we are to design a GDB-like
interactive interface, we should dene a set of commands for each function, which can be used to invoke these
functions. In multi-threaded program debugging we often we need to specify the threads to be aected before
doing a multi-threaded-oriented debugging operation. To be consistent with this we provide a concept called
thread-set, which is a set of threads numbers. Any multi-threaded-oriented operation should be applied to a
thread-set. To simplify the work, in our Pintool we dene only a built-in thread-set variable, whose value can be
accessed and modied by a command called threadset. Any multi-threaded-oriented operation is associated with
the value of the thread-set variable at the time the operation is being executed.
The commands are summarized below:

As seen from the table above, commands are classied into two categories based their functionality. The
control commands are typically a clone of GDB commands, with threadset/setmask commands to aid multi-
threaded support. The advanced commands are what used to do the multi-threaded debugging.
Although the commands listed above forms a complete debugging system, they are far from enough when
putting into practical use. However, our objective is to create a prototype multi-threaded debugging, which is to
demonstrate some ideas rather than to build a product-level system. Nevertheless, we believe that with a more
careful design and an eort on expanding the command set, this prototype can be quite useful. We may also
add/remove commands from the set whenever we nd it necessary during the process of development.

# Plan #
The whole project is divided into 3 parts.
1. The interactive Interface In this stage we would build the basic framework of the Pintool, namely the
GDB-like interactive interface. To achieve this we will need to be able to intercept each instruction, stop at
certain instruction and write a parser for the commands. This also provides us a good opportunity to learn
the APIs and design patterns of Pintool. It is supposed to take less than one week.
2. The control commands In this stage we will implement the control commands in the Pintool. As these
funtions are just a clone of GDB with little variation, it won't take long to do so for each command.
However, as we are dealing with multi-threaded debugging, an anticipated dicult will be understanding
the mechanism that Pin adopts to understand and operate on multi-threads. This is key to identify a thread
when we try to do some operation on it. We think this would take more than one week, but less than two
weeks.
3. The advanced commands If everything goes well in the previous two stages, we will move on to implement
the two advanced commands, which are access history and ordered run. The problem we are trying to
solve associated with access history are
 How to identify an access to a certain variable?
 What kind of data structure should we use to record the history?
 How to build a dependency graph using the history information?
Among which the rst might be a dicult one. The second one is like running a program with multiple break
point, but only stops at the last one. Though the process can be a little bit complicated, but we believe that
if we do well in the previous two stage, the possibility of getting stuck here would be signicantly reduced,
since this is just a batch of breakpoint operation implemented in the second stage.
To demonstrate the power of this prototype, we would use several example to evaluate it. Though we don't
have specied examples in mind, we plan to test it with Small and simple programs as well as heavier
programs. The small and simple ones might come from toy programs written by us. The heavier ones might
come from our own research area.
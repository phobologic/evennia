Building Quick-start
====================

The default `command <Commands.html>`_ definitions coming with Evennia
follows a style `similar <UsingMUXAsAStandard.html>`_ to that of MUX, so
the commands should be familiar if you used any such code bases before.
If you haven't, you might be confused by the use of ``@`` all over. This
is just a naming convention - commands related to out-of-character or
admin-related actions tend to start with ``@``, the symbol has no
meaning of its own.

The default commands have the following style (where ``[...]`` marks
optional parts):

::

     command[/switch/switch...] [arguments ...]

A *switch* is a special, optional flag to the command to make it behave
differently. It is always put directly after the command name, and
begins with a forward slash (``/``). The *arguments* are one or more
inputs to the commands. It's common to use an equal sign (``=``) when
assigning something to an object.

Below are some examples of commands. Use ``help <command>`` for learning
more about each command and their detailed options.

Stepping down from godhood
--------------------------

If you just installed Evennia, your very first player account is called
user #1, also known as the *superuser* or *god user*. This user is very
powerful, so powerful that it will override many game restrictions such
as locks. This can be useful, but it also hides some functionality that
you might want to test.

To temporarily step down from your superuser position you can use the
``@quell`` command:

::

    > @quell

This will make you start using the permission of your current
`Character <Objects.html>`_ instead of your superuser level. If you
didn't change any settings your game Character should have an *Immortal*
level permission - high as can be without bypassing locks like the
superuser does. This will work fine for the examples on this page. Use
``@unquell`` to get back to superuser status again afterwards.

Creating an object
------------------

Basic objects can be anything -- swords, flowers and non-player
characters. They are created using the ``@create`` command:

::

    > @create box

This created a new 'box' (of the default object type) in your inventory.
Use the command ``inventory`` (or ``i``) to see it. Now, 'box' is a
rather short name, let's is give a few aliases.

::

    > @name box = very large box;box;very;crate

We now actually renamed the box to *very large box* (and this is what we
will see when looking at it), but we will also recognize it by any of
the other names we give - like *crate* or simply *box* as before. We
could have given these aliases directly after the name in the
``@create`` command, this is true for all creation commands - you can
always tag on a list of ;-separated aliases to the name of your new
object. If you had wanted to not change the name itself, but to only add
aliases, you could have used the ``@alias`` command.

We are currently carrying the box. Let's drop it.

::

    > drop box

Hey presto - there it is on the ground, in all its normality (you can
also create & drop in one go using the ``/drop`` switch, like this:
``@create/drop box``).

::

    > examine box

This will show some technical details about the box object (you can
normally just write ``ex`` as a short for ``examine``).

Try to ``look`` at the box to see the (default) description.

::

    > look box

Let's add some flavor.

::

    > @describe box = This is a large and very heavy box.

If you try the ``get`` command we will pick up the box. So far so good,
but if we really want this to be a large and heavy box, people should
*not* be able to run off with it that easily. To prevent this we need to
lock it down. This is done by assigning a *Lock* to it. Make sure the
box was dropped in the room, then try this:

::

    > @lock box = get:false()

Locks are a rather `big topic <Locks.html>`_, but for now that will do
what we want. This will lock the box so noone can lift it. The exception
is superusers, they override all locks and will pick it up anyway. Make
sure you are quelling your superuser powers and try to get the box now:

::

    > get box
    You can't get that. 

Think the default error message looks dull? The ``get`` command looks
for an `Attribute <Attributes.html>`_ named ``get_err_msg`` for
returning a nicer error message (we just happen to know this, you would
need to peek into the code for the ``get`` command to find out). You set
attributes using the ``@set`` command:

::

    > @set box/get_err_msg = It's way too heavy for you to lift. 

Try to get it now and you should see a nicer error message echoed back
to you.

Get a personality
-----------------

`Scripts <Scripts.html>`_ are powerful things that allows time-dependent
effects on objects. To try out a first script, let's put one on
ourselves. There is an example script in
``game/gamesrc/scripts/examples/bodyfunctions.py`` that is called
``BodyFunctions``. To add this to us we will use the ``@script``
command:

::

    > @script self = examples.bodyfunctions.BodyFunctions

(note that you don't have to give the full path as long as you are
pointing to a place inside the ``gamesrc/scripts`` directory). Wait a
while and you will notice yourself starting making random observations.

::

    > @script self 

This will show details about scripts on yourself (also ``examine``
works). You will see how long it is until it "fires" next. Don't be
alarmed if nothing happens when the countdown reaches zero - this
particular script has a randomizer to determine if it will say something
or not. So you will not see output every time it fires.

When you are tired of your character's "insights", kill the script with

::

    > @script/stop self = examples.bodyfunctions.BodyFunctions

Pushing your buttons
--------------------

If we get back to the box we made, there is only so much fun you can do
with it at this point. It's just a dumb generic object. If you renamed
it to ``stone`` and changed its description noone would be the wiser.
However, with the combined use of custom
`Typeclasses <Typeclasses.html>`_, `Scripts <Scripts.html>`_ and
object-based `Commands <Commands.html>`_, you could expand it and other
items to be as unique, complex and interactive as you want.

Let's take an example. So far we have only created objects that use the
default object typeclass named simply ``Object``. Let's create an object
that is a little more interesting. Under ``game/gamesrc/objects/`` there
is a directory ``examples`` with a module ``red_button.py``. It contains
the enigmatic RedButton typeclass.

Let's make us one of *those*!

::

    > @create/drop button:examples.red_button.RedButton

We import the RedButton python class the same way you would import it in
Python except Evennia defaults to looking in ``game/gamesrc/objects/``
so you don't have to write the full path every time. There you go - one
red button.

The RedButton is an example object intended to show off a few of
Evennia's features. You will find that the `Scripts <Scripts.html>`_ and
`Commands <Commands.html>`_ controlling it are scattered in
``examples``-folders all across ``game/gamesrc/``.

If you wait for a while (make sure you dropped it!) the button will
blink invitingly. Why don't you try to push it ...? Surely a big red
button is meant to be pushed. You know you want to.

Making yourself a house
-----------------------

The main command for shaping the game world is ``@dig``. For example, if
you are standing in Limbo you can dig a route to your new house location
like this:

::

    > @dig house = large red door;door;in, to the outside;out

This will create a new room named 'house'. It will also directly create
an exit from your current location named 'large red door' and a
corresponding exit named 'to the outside' in the house room leading back
to Limbo. We also define a few aliases to those exits, so people don't
have to write the full thing all the time.

If you wanted to use normal compass directions (north, west, southwest
etc), you could do that with ``@dig`` too. But Evennia also has a
limited version of ``@dig`` that helps for compass directions (and also
up/down and in/out). It's called ``@tunnel``:

::

    > @tunnel sw = cliff

This will create a new room "cliff" with an exit "southwest" leading
there and a path "northeast" leading back from the cliff to your current
location.

You can create new exits from where you are using the ``@open`` command:

::

    > @open north;n = house

This opens an exit ``north`` to the previously created room ``house``.

If you have many rooms named ``house`` you will get a list of matches
and have to select which one you want to link to. You can also give its
database (#dbref) number, which is unique to every object. This can be
found with the ``examine`` command or by looking at the latest
constructions with ``@objects``.

Follow the north exit to your 'house' or ``@teleport`` to it:

::

    > north

or:

::

    > @teleport house

To manually open an exit back to Limbo (if you didn't do so with the
``@dig`` command):

::

    > @open door = limbo

(or give limbo's dbref which is #2)

Reshuffling the world
---------------------

You can find things using the ``@find`` command. Assuming you are back
at ``Limbo``, let's teleport the *large box to our house*.

::

    > @teleport box = house
    very large box is leaving Limbo, heading for house.
    Teleported very large box -> house.

We can still find the box by using @find:

::

    > @find box
    One Match(#1-#8):
       very large box(#8) - src.objects.objects.Object

Knowing the #dbref of the box (#8 in this example), you can grab the box
and get it back here without actually yourself going to ``house`` first:

::

    > @teleport #8 = here

(You can usually use ``here`` to refer to your current location. To
refer to yourself you can use ``self`` or ``me``). The box should now be
back in Limbo with you.

We are getting tired of the box. Let's destroy it.

::

    > @destroy box

You can destroy many objects in one go by giving a comma-separated list
of objects (or their #dbrefs, if they are not in the same location) to
the command.

Adding a help entry
-------------------

An important part of building is keeping the help files updated. You can
add, delete and append to existing help entries using the ``@sethelp``
command.

::

    > @sethelp/add MyTopic = This help topic is about ... 

Adding a World
--------------

After this brief introduction to building you may be ready to see a more
fleshed-out example. Evennia comes with a tutorial world for you to
explore.

First you need to switch back to *superuser* by using the ``@unquell``
command. Next, place yourself in ``Limbo`` and run the following
command:

::

    > @batchcommand contrib.tutorial_world.build

This will take a while (be patient and don't re-run the command). You
will see all the commands used to build the world scroll by as the world
is built for you.

You will end up with a new exit from Limbo named *tutorial*. Apart from
being a little solo-adventure in its own right, the tutorial world is a
good source for learning Evennia building (and coding).

Read
`contrib/tutorial\_world/build.ev <https://code.google.com/p/evennia/source/browse/contrib/tutorial_world/build.ev>`_
to see exactly how it's built, step by step. See also more info about
the tutorial world `here <TutorialWorldIntroduction.html>`_.

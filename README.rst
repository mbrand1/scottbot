========
ScottBot
========

A "that's what she said" bot that, much like Data in Generations, keeps trying
to better itself.


Requirements
============

* Node_. I'm running 0.4.5. I assume anything that supports the libraries will
  work.

* Npm_. Then install the dependencies with:

    npm install .

* Redis_. I'm running 2.2.5. Any 2.2.x or higher is probably fine.

* A sense of humor and some patience.

* IRC mates that won't kill you.

.. _Node: http://nodejs.org/
.. _Redis: http://redis.io/
.. _Npm: http://npmjs.org/



Running
=======

Start scottbot from the command line::

    $ node scottbot.js [options]

If you want to see all the options, use the ``-h`` option. At a minimum, you'll
probably want to specify an IRC server and some channels to join.

scottbot doesn't detach from the terminal or anything fancy like that, and the
error handling is *probably* robust enough to keep anything horrible from
happening. But this isn't exactly production-quality code.

If you really want to run it as a daemon, look at forever_ or supervisord_.

.. _forever: https://github.com/indexzero/forever
.. _supervisord: http://supervisord.org/


Training
========

The most important part is going to be training. Your scottbot will start very
dumb (see TODO_) so you need to teach it.

If scottbot made a joke that was funny, or missed a joke, you can say "yes" or
"lol" and it will treat the previous statement in the channel as a positive
example. For example, if your scottbot is named ``mscott`` (the default)::

    <foo> it was really hard
    <foo> mscott: yes
    <mscott> ok!

scottbot has just trained "it was really hard" as an example of "funny". The
next time someone says "it was really hard" (or something similar, this is
Bayesian) you'll probably see::

    <foo> it was really hard
    <mscott> that's what she said

Again, if you reply with "yes" or "lol", scottbot trains. Conversely, if
scottbot makes a terrible joke, you can say "no"::

    <foo> did you close out bug 1234?
    <mscott> that's what she said
    <foo> mscott: no
    <mscott> sorry :(

Now scottbot has trained "did you close out bug 1234" as a "notfunny" example.
You don't have to wait for scottbot to try, you can say "no" and it will train
the previous statement as unfunny. (Unfortunately, right now, it still says
"sorry.")

::

    <foo> when is the freeze date for 1.2.3?
    <foo> mscott: no
    <mscott> sorry :(

scottbot trained "when is the freeze date for 1.2.3" as a "notfunny" example.

You can also train scottbot explicity by telling it a funny phase using the
following syntax::

    <foo> mscott: "foo bar" is funny
    <mscott> ok!


Using Bootstrap Data
====================

It can take a while to train scottbot, especially if you don't have time to sit
there and teach it. Now there's some training data to get your instance
started!

There is a simple script that can load or dump the data from Redis.

The data is in ``twss.json``, and the script to load it is ``data.js``. Run
``node data.js -h`` to get all the options, then do one of the following::

    # Load data into Redis.
    node data.js [options] load

    # Dump data to JSON.
    node data.js [options] dump


Sharing Training Data
---------------------

Multiple instances of scottbot can share the same Redis backend, so they can
share training data and learn together. The instances I have running on Mozilla
IRC and on Freenode share the same DB.


.. _TODO:

TODO
====

* Train/bootstrap with `data from Twitter`_.

* A better way to train correct negatives.

* Train when someone else makes a TWSS joke.

* Stemming. It stinks that "stick" and "stuck" don't get trained together.

* Stopwords. Remember that "it", "he" and "she" are crucial to these jokes.

* Don't say "sorry :(" if it didn't just make a joke.

* Potentially get *really* fancy and blend the Bayesian method with other
  tools, like grammatical analysis to look for pronouns without antecedents.
  That's definitely not a priority, though, it's going pretty well as-is.

.. _data from Twitter: http://www.cs.washington.edu/homes/brun/pubs/pubs/Kiddon11.pdf

Varbit (or variable bit width client configurations)
====================================================

.. caution::
    This information is incomplete! There may be other possible uses for the varbit config system that we are unaware of. We do however know that it is also tied into how widgets work.

What it is
----------

Object State
~~~~~~~~~~~~

The configuration mapping system known as 'varbit' is responsible for handling object state, among other things. One of the main systems utilizing
the varbit configuration system is farming. Objects which have alternate state have an array of item IDs which they can be mapped to. For example,
if I want the initial farming object to then advance to it's next state, I move the state pointer from -1 to 0 (-1 is identity state, 0 maps to that offset
in the objects alternate state array). If I want to then advance the state of the object I simply increment that number again, and the object state will change to the
next value in the array.


Variable width?
---------------

So in the client there are 2 ways of retrieving a value for a configuration key, both resolve to the client session configuration map.

In the client there is a map of 20,000 integer values which can be used for storing any arbitrary attributes. Some are just plain 32 bit integers, and some of these
are used for storing any number of bits up to 32.

If the entity with attributes needs to store more than 1 attribute, for example, 2 boolean values, then it would have 2 varbit references.

Practical Example
-----------------

.. code-block:: cpp
    :linenos:

    object_state_service *object_state_service;

    void advance_oject_state(player *player, object_ref *ref) {
        int curr_state = object_state_service->get_state(player, ref);

        if (curr_state == ref.config->alternate_states.length - 1) {
            curr_state = object_state_service->reset_state(player, ref); // set value to -1
        } else {
            curr_state == object_state_service->increment_and_get_state(player, ref);
        }

        send_event(player, new UpdateClientConfigEvent(ref->config->varbit_config_id, curr_state));
    }
..




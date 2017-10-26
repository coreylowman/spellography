# Spellography

A study in the art of writing spells

## Vision

Want to be able to draw spells in a VR enviroment.

A spell is just like writing a word or sentence on a piece of paper, except instead of on a 2d surface, it will be written in the air.

A single spell is made up of a few parts, each of which is sort of like a word in a sentence:

1. Cast time (verb) - how long will you cast this spell for?
    1. Charge: hold the spell for as long as you want
    1. Instant: instantly cast the spell
    1. Channel: Cast the spell continuously until you stop. 
1. Target (noun) - who will this spell be affecting?
    1. Yourself
    1. Direction [speed]
    1. Area [radius]
1. Activation time (adverb) - how will the spell affect the target?
    1. Instant
    1. Over time [time]
    1. Delayed [time]
    1. Trap: Activates when someone walks over it
1. Effect - what will the spell affect?
    1. Health [positive]: affect the health of the target
    1. Location [direction]: move the target to a different location
    1. Potency [positive]: affect a casters spell power
    1. Sanity [positive]: affect a person's ability to see reality

Here are some sample spells:

1. Teleport - `Instantly` cast on `yourself` an `instant` `move forwards`
1. Force pull - `Instantly` cast in a `direction forwards` a `delayed` `move backwards`
1. Rain of Ice - `Channel` in an `area` an `instant` `damaging` spell
1. Rain of Fire - `Channel` in an `area` an `over time` `damaging` spell
1. Bolt of healing - `Charge` cast in a `direction forwards` an `over time` `healing` spell
1. Curse of the Dead - `Instantly` cast in a `direction backwards` a `delayed` `insanity` spell
1. Trap of realism - `Instantly` cast in an `area` a `trap` `sanity` spell
1. Empower - `Instantly` cast in a `direction forward` an `instant` `potency increasing` spell

## Approach

### Writing a word
So we have the different parts that make a spell, so how do we actually specify them? That comes down to writing in VR.
Many VR set ups have hand held devices that can track your hands and have buttons on them (e.g. the Oculus Touch),
we can track the position of those and use those to draw spells in the air.

One thing that will need to be figured out is how to combine different paths of the hands to make 1 words.
You can think of this problem sort of like writing a 't' on paper - you can't write it all using 1 path, you have to pick
up the pencil at some point to cross the t. In the same way, you probably won't be able to draw all the spell
words all in one go, there needs to be a way to 'pick up your pencil'.

### Matching a drawn path to a word 
After we have the set of points that denote a word, we will have to figure out what each word means. This will
be done using the [Hausdorff Distance](https://en.wikipedia.org/wiki/Hausdorff_distance). The Hausdorff distance
measures the distance between two point sets. Words will be selected based on the smallest distance they have to any of the words
that are possible.

A few subtleties:
1. All of the possible words will have to be specified in some manner
2. The possible words will have to be the correct scale
3. Multiple rotations will have to be tried on the drawn word to get the most accurate word selection
4. The point set will have to be translated so it is in roughly the same area as the other words.

Roughly in pseudocode:

```python
# predefined words that you can use
words = [...]

# measure of distance between two point sets
def hausdorff_distance(p, q):
    distance = 0.0
    ...
    return distance
    
def choose_word(path, threshold, epsilon=0.1): 
    min_dist = float.max
    min_dist_i = 0
    for i in range(len(words)):
        # note don't want to rotate around the z axis, which faces forwards
        for rot_x in range(0, 360, epsilon * 360):
            for rot_y in range(0, 360, epsilon * 360):
                rotated_word = rotate(words[i], rot_x, rot_y, 0)
                dist = hausdorff_distance(path, rotated_word)
                if dist < min_dist:
                    min_dist = dist
                    min_dist_i = dist
    return min_dist_i if min_dist <= threshold else None
```

### Figuring out the spell

Once each of the words has been chosen from the written text, the sentence will have to be parsed to make sure a valid
spell has been written.

In general the structure is `cast time` `direction` `activation time` `effect`. If any of those are out of order, or anything
of them have modifiers (the parts in [] above) when they shouldn't, or the modifier is incorrect, then the spell should fail.


### Casting the spell

It'd be extra cool if the spell that was cast looked different depending on what it did. For example,
it'd be nice if some spells were fire and some were ice.

The looks can be encoded through the activation time and the effect.

Here is one possible mapping for damaging spells types:
* instant damaging: ice
* over time damaging: fire
* delayed damaging: poison
* trap damaging: fire 

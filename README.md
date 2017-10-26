# Spellography

A study in the art of writing spells

## Vision

Want to be able to draw spells in a VR enviroment.

A spell is just like writing a word or sentence on a piece of paper, except instead of on a 2d surface, it will be written in the air.

A single spell is made up of a few parts, each of which is sort of like a word in a sentence:

1. Cast time (adverb) - how long will you cast this spell for?
    1. Charge: hold the spell for as long as you want - it gets stronger the longer it is charged.
    1. Instant cast: instantly cast the spell. The spell will have reduced effects.
    1. Channel: Cast the spell continuously until you stop. The effect over the duration will be greater than an instant cast, but not as strong as if you had charged for the same duration. 
1. Modifier - what will the spell affect?
    1. Health: positively (for allies) or negatively (for enemies) affect the health of the target
    1. Location [direction]: move the target to a different location
    1. Potency: positively or negatively affect a casters spell power
    1. Sanity: positively or negatively affect a person's ability to see reality
1. Target (noun) - who will this spell be affecting?
    1. Yourself
    1. Direction [speed]
    1. Area [radius]
1. Effect activation time
    1. Instant
    1. Over time [time]
    1. Delayed [time]
    1. Trap: Activates when someone walks over it

Here are some sample spells:

1. Teleport - Instantly cast a move on yourself that happens instantly
1. Force pull - instantly cast a move in a direction that happens after a few seconds
1. Rain of Ice - Channel a damaging spell in an area causing instant damage
1. Rain of Fire - Channel a damaging spell in an area causing damage over time
1. Bolt of healing - Charge a healing spell in a direction causing healing over time
1. Curse of the Dead - Instantly cast a spell affecting sanity in a direction causing delayed insanity
1. Trap of realism - Instantly cast a sanity spell in an area causing a trap sanity

## Approach

So we have the different parts that make a spell, so how do we actually specify them? That comes down to writing in VR.
Many VR set ups have hand held devices that can track your hands and have buttons on them (e.g. the Oculus Touch),
we can track the position of those and use those to draw spells in the air.

One thing that will need to be figured out is how to combine different paths of the hands to make 1 words.
You can think of this problem sort of like writing a 't' on paper - you can't write it all using 1 path, you have to pick
up the pencil at some point to cross the t. There needs to be some way to combine the two parts.

After we have the set of points that denote the word of the spell, we will have to figure out what each word means. This will
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
    
def choose(written_word, epsilon=0.1): 
    min_dist = float.max
    min_dist_i = 0
    for i in range(len(words)):
        for rot_x in range(0, 360, epsilon * 360):
            for rot_y in range(0, 360, epsilon * 360):
                rotated_word = rotate(words[i], rot_x, rot_y, 0)
                dist = hausdorff_distance(written_word, rotated_word)
                if dist < min_dist:
                    min_dist = dist
                    min_dist_i = dist
    return min_dist_i
```
 
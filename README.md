# ansible-loops
A list of Ansible loop behaviour with examples

The Ansible documentation presents a detailed list of loop styles, often mapping the replacement of a with_<something> loop to newer loop syntax.

Most loop examples include sample variables but for some of the more complex looping structures, it's not always immediately obvious how the loop interacts. Sometime the variables used as illustrations are not populated and hence difficult for the layperson to understand or interpret.

This repo intends to present each loop style with a slighly more comprehensible set of sample variables.

# with_list
# with_items
# with_indexed_items
# with_flattened
# with_together
# with_dict
# with_sequence
# loop with subelements (formerly with_subelements)

`loop` with the `subelements` filter is useful to control looping over a data structure that combines known, fixed elements (for example a well-structured dictionary) with a variable element (for example a list that could have any number of varying elements).



# with_nested / with_cartesian
# with_random_choice

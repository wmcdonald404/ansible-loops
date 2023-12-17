# ansible-loops
> A list of Ansible loop behaviour with examples

[The Ansible documentation](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_loops.html#migrating-from-with-x-to-loop) presents a detailed list of loop styles, often mapping the replacement of an older style `with_<something>` loop to the newer `loop` with `<some_filter>` syntax.

Most loop examples include sample variables but for some of the more complex looping structures, it's not always immediately obvious how the loop interacts. Often variables used in the documented examples are not populated and hence difficult for the layperson to understand or interpret.

This repo tries to present each loop style with a more comprehensible set of sample variables.

- with_list
- with_items
- with_indexed_items
- with_flattened
- with_together
- with_dict
- with_sequence
- loop with subelements
- with_nested / with_cartesian
- with_random_choice

# loop
> Formerly with_list
[`loop`](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_loops.html#with-list) directly replaces `with_list`. This is the simplest loop structure, given a list of values, loop over each.

Given the data structure `list_one` containing sample colours:
```
    list_one:
      - cyan
      - magenta
      - yellow
      - key
```
And the loop structure:
```
    - name: with_list -> loop
      ansible.builtin.debug:
        msg: "{{ item }}"
      loop: '{{ list_one }}'
```
We get the output:
```
TASK [with_list -> loop] *************************************************************************************************************************************
ok: [localhost] => (item=cyan) => {
    "msg": "cyan"
}
ok: [localhost] => (item=magenta) => {
    "msg": "magenta"
}
ok: [localhost] => (item=yellow) => {
    "msg": "yellow"
}
ok: [localhost] => (item=key) => {
    "msg": "key"
}
```
# loop with flatten
> Formerly with_items
The [`loop` with `placeholder `flatten`](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_loops.html#with-items) will collapse the leaves of a nested list. 

Given the data structure `list_one` containing sample colours, with nested shades of each colour:
```
    list_one:
      - reds
        - carnation
        - violet
        - scarlet
      - greens
        - seafoam
        - inchworm
        - jade
      - blues
        - azure
        - cyan
        - denim
```
And the loop structure:
```
- name: with_items -> loop
  ansible.builtin.debug:
    msg: "{{ item }}"
  loop: "{{ list_one|flatten(levels=1) }}"
```
We get the output:
```
TASK [with_items -> loop] ************************************************************************************************************************************
ok: [localhost] => (item=reds - carnation - violet - scarlet) => {
    "msg": "reds - carnation - violet - scarlet"
}
ok: [localhost] => (item=greens - seafoam - inchworm - jade) => {
    "msg": "greens - seafoam - inchworm - jade"
}
ok: [localhost] => (item=blues - azure - cyan - denim) => {
    "msg": "blues - azure - cyan - denim"
}
```
# loop with flatten and loop_control.index_var
> Formerly with_indexed_items
The [`loop` with `flatten`` and `loop_control.index_var` filter](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_loops.html#with-indexed-items)

Given the data structure `list_one` containing sample colours:
```
    list_one:
      - cyan
      - magenta
      - yellow
      - key
```
And the loop structure:
```
    - name: with_indexed_items -> loop
      ansible.builtin.debug:
        msg: "{{ index }} - {{ item }}"
      loop: "{{ list_one|flatten(levels=1) }}"
      loop_control:
        index_var: index
```
We get the output:
```
TASK [with_indexed_items -> loop] ****************************************************************************************************************************
ok: [localhost] => (item=cyan) => {
    "msg": "0 - cyan"
}
ok: [localhost] => (item=magenta) => {
    "msg": "1 - magenta"
}
ok: [localhost] => (item=yellow) => {
    "msg": "2 - yellow"
}
ok: [localhost] => (item=key) => {
    "msg": "3 - key"
}
```
# with_flattened
> Formerly with_flattened
The [`loop` with `placeholder` filter](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_loops.html#with-flattened)...

Given the data structure of a dictionary `users`:
```

```
And the loop structure:
```

```
We get the output:
```

```
# loop with zip
> Formerly with_together

The [`loop` with `zip` filter](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_loops.html#with-together) will combine elements of two lists in slices across the intersection of lists.

This could prove a useful pattern where you have sources of data with a common key but disparate sets of data. For example, an inventory system and another operational data silo such as a monitoring system, backup or similar.

Note: `with_items` will output leftover values where the lists are uneven lengths. `loop` with `zip` does not do this by default.

Given the data with two lists below:
```
    list_one:
      - cyan
      - magenta
      - yellow
      - black
    list_two:
      - light
      - medium
      - dark
```
And the loop structure:
```
    - name: with_together -> loop
      ansible.builtin.debug:
        msg: "{{ item.0 }} - {{ item.1 }}"
      loop: "{{ list_one|zip(list_two)|list }}"
```
We get the output:
```
TASK [with_together -> loop] *********************************************************************************************************************************
ok: [localhost] => (item=['cyan', 'light']) => {
    "msg": "cyan - light"
}
ok: [localhost] => (item=['magenta', 'medium']) => {
    "msg": "magenta - medium"
}
ok: [localhost] => (item=['yellow', 'dark']) => {
    "msg": "yellow - dark"
}
```

Again, note that no match has been made for the 4th list item "black".

# loop with dictsort or dict2items
> Formerly with_dict

The [`loop` with `dict2items` filter](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_loops.html#with-dict)  (or `dictsort`) will loop over the keys in a dictionary.

Note: `with_dict understands how to parse a _list_ of dictionaries, where `dict2items` and `dictsort` do not natively. To loop over a list of dictionaries (as opposed to a dictionary of dictionaries), additional processing would be required.

Given the data structure of a dictionary `users`:
```
    users:
      user1:
        department: finance
        region: emea
        directories:
          - /global/finance
          - /emea/finance
      user2:
        department: security
        region: apac
        directories:
          - /apac/infosec
```
And the loop structure:
```
    - name: with_dict -> loop (option 1)
      ansible.builtin.debug:
        msg: "{{ item.key }} - {{ item.value }}"
      loop: "{{ users | dict2items }}"
```
We get the output:
```
TASK [with_dict -> loop (option 1)] **************************************************************************************************************************
ok: [localhost] => (item={'key': 'user1', 'value': {'department': 'finance', 'region': 'emea', 'directories': ['/global/finance', '/emea/finance']}}) => {
    "msg": "user1 - {'department': 'finance', 'region': 'emea', 'directories': ['/global/finance', '/emea/finance']}"
}
ok: [localhost] => (item={'key': 'user2', 'value': {'department': 'security', 'region': 'apac', 'directories': ['/apac/infosec']}}) => {
    "msg": "user2 - {'department': 'security', 'region': 'apac', 'directories': ['/apac/infosec']}"
}
```
# loop with range
> Formerly with_sequence

The [`range` filter](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_loops.html#with-sequence) can be used to create a sequence of values which can then be looped over. 

For example a series of sequential user names, group names or host names may fit this pattern.

No fixed data structure is required for this example.

Given  the loop structure:
```
    - name: with_sequence -> loop
      ansible.builtin.debug:
        msg: "{{ 'testuser%02x' | format(item) }}"
      loop: "{{ range(0, 4 + 1, 2)|list }}"
```
We get the output:
```
TASK [with_sequence -> loop] *********************************************************************************************************************************
ok: [localhost] => (item=0) => {
    "msg": "testuser00"
}
ok: [localhost] => (item=2) => {
    "msg": "testuser02"
}
ok: [localhost] => (item=4) => {
    "msg": "testuser04"
}
```
# loop with subelements
> Formerly with_subelements

The [`loop` with `subelements` filter](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_loops.html#with-subelements) is useful to control looping over a data structure that combines well-known, fixed elements with varying elements.

For example a list of dictionary entries where one key contains a nested list of a varying number of entries. 

Given the data structure a list of dictionaries `users` each elements of which includes a nested list `files`:
```
    users:
      - username: user1
        action: GET
        files:
          - file1
      - username: user2
        action: GET
        files:
          - file1
          - file2
```
And the loop structure:
```
    - name: debug the data structure
      ansible.builtin.debug:
        msg:
          - 'item.0 is: {{ item.0 }}'
          - 'item.1 is: {{ item.1 }}'
      loop: '{{ users | subelements("files") }}'
```
We get the output:
```
TASK [debug the data structure] ******************************************************************************************************************************
ok: [localhost] => (item=[{'username': 'user1', 'action': 'GET', 'files': ['file1']}, 'file1']) => {
    "msg": [
        "item.0 is: {'username': 'user1', 'action': 'GET', 'files': ['file1']}",
        "item.1 is: file1"
    ]
}
ok: [localhost] => (item=[{'username': 'user2', 'action': 'GET', 'files': ['file1', 'file2']}, 'file1']) => {
    "msg": [
        "item.0 is: {'username': 'user2', 'action': 'GET', 'files': ['file1', 'file2']}",
        "item.1 is: file1"
    ]
}
ok: [localhost] => (item=[{'username': 'user2', 'action': 'GET', 'files': ['file1', 'file2']}, 'file2']) => {
    "msg": [
        "item.0 is: {'username': 'user2', 'action': 'GET', 'files': ['file1', 'file2']}",
        "item.1 is: file2"
    ]
}
```
# loop with product
> Formerly with_nested / with_cartesian

The [`loop` with `product` filter](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_loops.html#with-nested-with-cartesian) is useful to combine two lists in a loop, resulting in a loop which gives each unique combination of both lists.

Given the data structure of two lists, `list_one` containing sample colours, `list_two` containing sample shades:
```
    list_one:
      - cyan
      - magenta
      - yellow
      - key
    list_two:
      - light
      - medium
      - dark
```
And the loop structure:
```
    - name: with_nested -> loop
      ansible.builtin.debug:
        msg: "{{ item.0 }} - {{ item.1 }}"
      loop: "{{ list_one|product(list_two)|list }}"
```
We get the output:
```
TASK [with_nested -> loop] ***********************************************************************************************************************************
ok: [localhost] => (item=['cyan', 'light']) => {
    "msg": "cyan - light"
}
ok: [localhost] => (item=['cyan', 'medium']) => {
    "msg": "cyan - medium"
}
ok: [localhost] => (item=['cyan', 'dark']) => {
    "msg": "cyan - dark"
}
ok: [localhost] => (item=['magenta', 'light']) => {
    "msg": "magenta - light"
}
ok: [localhost] => (item=['magenta', 'medium']) => {
    "msg": "magenta - medium"
}
ok: [localhost] => (item=['magenta', 'dark']) => {
    "msg": "magenta - dark"
}
ok: [localhost] => (item=['yellow', 'light']) => {
    "msg": "yellow - light"
}
ok: [localhost] => (item=['yellow', 'medium']) => {
    "msg": "yellow - medium"
}
ok: [localhost] => (item=['yellow', 'dark']) => {
    "msg": "yellow - dark"
}
ok: [localhost] => (item=['key', 'light']) => {
    "msg": "key - light"
}
ok: [localhost] => (item=['key', 'medium']) => {
    "msg": "key - medium"
}
ok: [localhost] => (item=['key', 'dark']) => {
    "msg": "key - dark"
}
```

# random
> Formerly with_random_choice

The [`random` filter](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_loops.html#with-random-choice) can be used to select a single random value from a list to target. This can be accomplished without a loop.

Given the data structure `list_one` containing sample colours:
```
    list_one:
      - cyan
      - magenta
      - yellow
      - key
```
And the loop structure:
```
    - name: with_random_choice -> loop (No loop is needed here)
      ansible.builtin.debug:
        msg: "{{ list_one |random }}"
```
We get the output:
```
TASK [with_random_choice -> loop (No loop is needed here)] ***************************************************************************************************
ok: [localhost] => {
    "msg": "cyan"
}
```

# Further Reading
- [Ansible - Using filters to manipulate data](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_loops.html#with-nested-with-cartesian)
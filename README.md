# ansible-loops
> A list of Ansible loop behaviour with examples

[The Ansible documentation](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_loops.html#migrating-from-with-x-to-loop) presents a detailed list of loop styles, often mapping the replacement of an older style `with_<something>` loop to the newer `loop` with `<some_filter>` syntax.

Most loop examples include sample variables but for some of the more complex looping structures, it's not always immediately obvious how the loop interacts. Often variables used in the documented examples are not populated and hence difficult for the layperson to understand or interpret.

This repo intends to present each loop style with a slighly more comprehensible set of sample variables.

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

# with_list
# with_items
# with_indexed_items
# with_flattened
# with_together
# with_dict
# with_sequence
# loop with subelements
> Formerly with_subelements

The [`loop` with `subelements` filter](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_loops.html#with-subelements) is useful to control looping over a data structure that combines well-known, fixed elements with varying elements.

For example a structured dictionary with a nested element like a list inside the dictionary that could have multiple varying values per-dictionary element.

Given the data structure of a dictionary `users` each elements of which includes a nested list `files`:
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

The [`random` filter](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_loops.html#with-random-choice) Can be used to select a single random value from a list to target. This can be accomplished without a loop.

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
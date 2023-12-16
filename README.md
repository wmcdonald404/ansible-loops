# ansible-loops
> A list of Ansible loop behaviour with examples

[The Ansible documentation](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_loops.html#migrating-from-with-x-to-loop) presents a detailed list of loop styles, often mapping the replacement of a with_<something> loop to newer loop syntax.

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

[The `loop` with the `subelements`](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_loops.html#with-subelements) filter is useful to control looping over a data structure that combines well-known, fixed elements (for example a structured dictionary) with a nested element (for example a list inside the dictionary that could have any number of varying elements).

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

# with_nested / with_cartesian
# with_random_choice

# Building a Personal Knowledge Management system from scratch, part 1
The minimum one could ask for such a system would be:
* to access notes written in markdown in local folders
* to create easily such notes in the right place
* to search the text of all notes and display the result of a search

# Notes in a local folder

To simplify I assume some kind of structure for the notes
```python
PKM/
├── Project1/
│   ├── note1.md
│   └── note2.md
├── Project2/
│   ├── note1.md
│   └── note2.md
└── index.md

```
The index will gather a list of all existing notes.

# Minimal implementation
## Notes and their creation
Not much to say here: the notes are simple markdown files. If needed one can automate the creation of a simple note, like so:
```python
def create_or_edit_note(
    project, note_name, project_root=DFLT_PROJECT_ROOT, content=None
):
    project_path = os.path.join(project_root, project)
    if not os.path.exists(project_path):
        os.makedirs(project_path)
    note_path = os.path.join(project_path, note_name + '.md')
    if content:
        with open(note_path, 'w') as file:
            file.write(content)
    else:
        os.system(f'open {note_path}')

# Example usage
create_or_edit_note('Project1', 'note3', 'This is a new note.')
```

Other than that, everything is done manually, in local folders.

## Indexing the notes
It is just a matter of listing all existing files, and write the result in the index.

```python
def generate_index():
    index_content = "# Index\n\n"
    for root, dirs, files in os.walk('PKM'):
        for file in files:
            if file.endswith('.md'):
                rel_path = os.path.relpath(os.path.join(root, file), 'PKM')
                index_content += f"- [{file}]({rel_path})\n"
    with open('PKM/index.md', 'w') as index_file:
        index_file.write(index_content)


# Example usage
generate_index()
```
The result looks like
```markdown
# Index

- [note3.md](Project1/note3.md)

```
## Searching the notes
The most basic search is just a text search going through all the notes.

```python
def search_notes(keyword):
    matches = []
    for root, dirnames, filenames in os.walk('PKM'):
        for filename in fnmatch.filter(filenames, '*.md'):
            note_path = os.path.join(root, filename)
            with open(note_path, 'r') as file:
                if keyword.lower() in file.read().lower():
                    matches.append(note_path)
    return matches

# Example usage
print(search_notes('new note'))
```
The result of the search is simply printed:
```bash
['PKM/Project1/note3.md']
```

# Next steps
This first iteration is just to get off the ground. A lot of work needs to be done: better search , better display of results, version control, etc, etc...


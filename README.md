# OSG School 2024 

The source files for the OSG School 2024 website.

To serve the website locally, download Docker and use the command in the root directory:

```console
docker run --rm -it -p 8000:8000 -v ${PWD}:/docs squidfunk/mkdocs-material
```

ARM
```shell
docker run --rm -it -p 8100:8000 -v  ${PWD}:/docs ghcr.io/afritzler/mkdocs-material
```

# Adding Documentation to the Website

## Adding in the Documentation

Documentation is hosted in the ```/docs/material``` directory. 

Each category of documentation is in a subdirectory at location ```/docs/material/<category>```.

When you add new documentation you should name the file like so: ```part<x>-ex<y>-<name>.md```

Documentation should be referenced in two locations:

#### [/docs/material/index.md](https://github.com/osg-htc/school-2024/blob/main/docs/materials/index.md)

This is an overview page that contains all documentation and each category’s files.

Use format below when adding to this page
```markdown
## <category>

([PDF](<category>/files/<filename>.pdf),
[PowerPoint](<category>/files/<filename>.pptx))

### <category> Exercises: 

- [Exercise 1.1: <exercise-name>](<category>/part1-ex1-<exercise-name>.md)
- [Exercise 1.2: <exercise-name>](<category>/part1-ex2-<exercise-name>.md)
```
        
#### [/mkdocs.yml](https://github.com/osg-htc/school-2024/blob/main/mkdocs.yml)

This files generates the website navigation. You should not include the category files in this navigation.

If you are adding materials it will look like so:
```yaml
nav:
  - Materials:
    - <Category0> Exercises:
      - 1.1 Short Name:               materials/<category0>/part1-ex1-<exercise-name>.md
      - 1.2 Short Name:               materials/<category0>/part1-ex2-<exercise-name>.md
    - <Category1> Exercises:
      - 1.1 Short Name:               materials/<category1>/part1-ex1-<exercise-name>.md
      - 1.2 Short Name:               materials/<category1>/part1-ex2-<exercise-name>.md
```

**Note:** This is generated in order so the order of the categories should correspond to the material overview page. 

## Adding in Additional Files

Files corresponding to each category should be in the subdirectory at location ```/docs/material/<category>/files/```.

Additionally all files should be referenced in the ```/docs/material/index.md``` file using format:
```markdown
```markdown
## <category>

([PDF](<category>/files/<filename>.pdf),
[PowerPoint](<category>/files/<filename>.pptx))

### <category> Exercises: 
 - ...
```

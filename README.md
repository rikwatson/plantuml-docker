# PlantUML in Docker

Run PlantUML in a Docker container. For use as part of a build pipeline.

![Docker Build Status](https://img.shields.io/docker/cloud/build/rikwatson/plantuml-docker?style=for-the-badge)

## Usage

### Multiple files by wildcard

Where all PlantUML files exist in the `path-to-mount` folder and have the extension .puml:

```bash
docker run -v path-to-mount:/source --rm -i rikwatson/plantuml-docker source/*.puml -o ./output
```

### Single file

```bash
cat sample.puml | docker run --rm -i rikwatson/plantuml-docker > output.png
```

### Using pre-commit git hooks

An example pre-commit `git` hook is located in `./.githooks/pre-commit` show below.

#### pre-commit

```bash
#!/bin/sh

# Check for the existence of Docker
if ! type docker > /dev/null 2>&1; then
  echo "# Docker not installed or not in PATH"
  echo "Skipping PlantUML generation"
  exit 0
fi

exec 1>&2

# Get all .puml files from the list of staged files as a flattened, quoted string
staged=$(git diff-index --cached HEAD --name-only | grep '\.puml$' | sed -e 's/^/"source\//' -e 's/$/" /' | tr -d '\n')

echo "Staged .puml files:"
echo $staged

# Generate diagrams for the staged .puml files
generate="docker run -v $(pwd):/source --rm -i rikwatson/plantuml-docker:1.1 -r -x \".git/**\" -o \"/source/docs/assets\" $staged"
eval $generate

# Add generated diagrams to the commit
git add docs/assets

exit 0
```

This hook can be enabled via:

```bash
git config --local core.hooksPath .githooks/
```

### Azure DevOps Pipeline

```yaml
trigger:
- master

pool:
  vmImage: 'ubuntu-latest'

steps:
- script: docker run -v $(Build.Repository.LocalPath)/plantuml:/source --rm -i rikwatson/plantuml-docker source/*.puml -o ./output
  displayName: 'Export PlantUML diagrams'

- task: PublishBuildArtifacts@1
  displayName: 'Publish PlantUML diagrams as artifact'
  inputs:
    PathtoPublish: '$(Build.Repository.LocalPath)/plantuml/output'
    ArtifactName: 'plantuml-output'
```

### Build

```bash
docker build -t plantuml-docker
```

### Publish

```bash
docker tag plantuml-docker rikwatson/plantuml-docker
docker push rikwatson/plantuml-docker
```

## Upcoming

- [x] Include Azure Devops sample tasks
- [ ] Include more samples for PNG and SVG, and wildcard usage
- [ ] List the benefits vs. PlantUML Server

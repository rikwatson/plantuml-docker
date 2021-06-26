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

# docker-compose-notes.md

Compose version 3 reference [here](https://docs.docker.com/compose/compose-file/compose-file-v3/).

## Default Values for Env Variables

Default values in `docker-compose.yml` files take the form:

```yaml
- ${ENVIRONMENT_VARIABLE_NAME:-defaultvalue}
```

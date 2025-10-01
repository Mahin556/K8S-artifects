In YAML, *placeholders* aren’t a built-in feature like in some programming languages, but there are several common ways people represent or simulate placeholders depending on the use case:

* **Explicit placeholder values (manual)**

  * Use `null`, `~`, or a dummy string like `"CHANGE_ME"`, `"TBD"`, `"REPLACE_WITH_VALUE"`

  ```yaml
  database:
    host: "CHANGE_ME"
    port: 5432
    username: "TBD"
    password: null
  ```

* **Environment variable substitution (with tools like Docker Compose, Ansible, Kubernetes, Helm, etc.)**

  * YAML parsers themselves don’t substitute, but many frameworks support it.
  * Example with Docker Compose:

  ```yaml
  version: "3.8"
  services:
    db:
      image: postgres
      environment:
        POSTGRES_USER: ${DB_USER}
        POSTGRES_PASSWORD: ${DB_PASSWORD}
  ```

  Here `${DB_USER}` and `${DB_PASSWORD}` are placeholders replaced by environment variables.

* **Anchors and aliases (YAML native)**

  * YAML supports *anchors* (`&`) and *aliases* (`*`), which let you reuse values.

  ```yaml
  defaults: &db_defaults
    host: localhost
    port: 5432

  database:
    <<: *db_defaults
    username: admin
    password: secret
  ```

* **Templates (with tools)**

  * Often YAML files are run through template engines like **Jinja2**, **Helm charts (Go templates)**, or **yq** to support placeholders.
  * Example with Jinja2:

  ```yaml
  database:
    host: {{ db_host }}
    port: {{ db_port }}
  ```

* **Custom placeholder syntax**

  * Some teams invent their own convention, like wrapping placeholders in `<< >>` or `@@ @@` to make them obvious.

  ```yaml
  api:
    key: "<<API_KEY>>"
  ```

👉 So, if you mean *raw YAML spec*, placeholders don’t exist beyond anchors/aliases. If you mean *real-world YAML configs*, placeholders usually mean *values to be filled in later* (via env vars, templates, or dummy text).

Do you want me to show you **YAML placeholders specifically for Kubernetes (Helm / Kustomize)**, or more **general YAML placeholder tricks**?

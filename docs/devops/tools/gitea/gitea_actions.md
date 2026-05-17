# Gitea Actions

## Workflow mittels Workflow_dispatch ausführen (manuell starten)
Ein Workflow kann seit Gitea Version 1.23.0 analog Github mit workflow_dispatch manuell gestartet werden. 

Anlog Github können auch Input Parameter mit verschiedenen Feldtypen und vordefinierten Werten mitgegeben werden (siehe Beispiel).

!!! warning
    Gemäss [Github Pull Request 28163](https://github.com/go-gitea/gitea/pull/28163) wird immer das Workflow-File aus dem Default-Branche berücksichtigt, auch wenn ein andderer Branch zum ausführen selektiert wird.

Beispiel:

```yaml
name: Docker Image CI

on:
  workflow_dispatch:
    inputs:
      logLevel:
        description: 'Log level'
        required: true
        default: 'warning'
        type: choice
        options:
        - info
        - warning
        - debug
      tags:
        description: 'Test scenario tags'
        required: false
        type: boolean
      boolean_default_true:
        description: 'Test scenario tags'
        required: true
        type: boolean
        default: true
      environment:
        description: 'Environment to run tests against'
        type: environment
        required: true
        default: 'environment values'
      number_required_1:
        description: 'number '
        type: number
        required: true
        default: '100'
      number_1:
        description: 'number'
        type: number
        required: false

env:
  inputs_logLevel:              ${{ inputs.logLevel }}
  inputs_tags:                  ${{ inputs.tags }}
  inputs_boolean_default_true:  ${{ inputs.boolean_default_true }}
  inputs_environment:           ${{ inputs.environment }}
  inputs_number_1:              ${{ inputs.number_1  }}
  inputs_number_required_1:     ${{ inputs.number_required_1  }}

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - run: ls -la
      - run: env | grep inputs
      - run: echo ${{ inputs.logLevel }}
      - run: echo ${{ inputs.boolean_default_true }}
```
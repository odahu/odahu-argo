# ODAHU Argo Templates
ODAHU extensions for Argo https://github.com/argoproj/ 

Table of Content:
1. [Install](#install)
2. [Templates](#templates)
3. [Examples](#examples)

## Install

As any Argo `WorkflowTemplate` ODAHU's set of templates can be installed via Argo CLI or Kubernetes CLI:
```shell
argo template create ./odahu.templates.yaml
```
OR 
```shell
kubectl apply -f ./odahu.templates.yaml
```

### ODAHU Credentials
As any ODAHU API clients, Argo tasks must pass through regular authentication procedure with Identity Provider (such as Keycloak). 
Find more in [Security section](https://docs.odahu.org/gen_security.html) of ODAHU documentation.

It is recommended to create a separate Service Account in Identity Provider for Argo Workflows with minimal required permissions. 
Credentials for the Service Account have to be put to k8s cluster as a secret with the following structure:
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: odahu-credentials
data:
  client_id: <b64_client_id>
  client_secret: <b64_client_secret>
  issuer_url: <b64_issuer_url>
```

A reference to this k8s Secret must be provided to any ODAHU step in `odahuCredentialsSecret` parameter, or it will default to secret named `odahu-credentials` in the same namespace where Workflow runs.

## Templates

Common inputs of all templates:
|           Input          |  Type  | Mandatory |       Default       |                                                                                                        Description                                                                                                        |
|:------------------------:|:------:|:---------:|:-------------------:|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------:|
|        `odahuURL`        | string |  **yes**  |                     |                                                                                                   Base URL of ODAHU API                                                                                                   |
| `odahuCredentialsSecret` | string |     no    | `odahu-credentials` | Reference to K8s Secret with [ODAHU Credentials](#odahu-credentials); <br>Possible formats:<br>- `odahu-credentials`<br>- `/api/v1/secrets/odahu-credentials`<br>- `/api/v1/namespaces/some-ns/secrets/odahu-credentials` |
|     `wait_completion`    | string |     no    |       `"true"`      |                 Possible values: `"true"`/`"false"`.<br>When `"true"` the step will wait for operation to successfully complete. <br>When `"false"`, the step will succeed right after request submission.                |

### Model Training Template

Training template creates a new `ModelTraining` object in ODAHU cluster.

Inputs:
| Input            | Type     | Mandatory | Default | Description                                                         |
|------------------|----------|-----------|---------|---------------------------------------------------------------------|
| trainingManifest | artifact | **yes**   |         | [YAML Training Manifest](https://docs.odahu.org/ref_trainings.html) |

Outputs:
| Output      | Type   | Description                   |
|-------------|--------|-------------------------------|
| training_id | string | ID of created `ModelTraining` |

### Model Packaging Template

Packaging template creates a new `ModelPackaging` object in ODAHU cluster.

For convenient steps pipelining Packaging template supports `fromTrainingID`. Read more in Inputs table below.

Inputs:
| Input             | Type     | Mandatory | Default | Description                                                          |
|-------------------|----------|-----------|---------|----------------------------------------------------------------------|
| packagingManifest | artifact | **yes**   |         | [YAML Packaging Manifest](https://docs.odahu.org/ref_packagers.html) |
| fromTrainingID    | string   | no        |         | `fromTrainingID` points to a training that produced a model artifact. If specified "artifactName" field will be overridden by the last artifact of referenced Training |

Outputs:
| Output       | Type   | Description                    |
|--------------|--------|--------------------------------|
| packaging_id | string | ID of created `ModelPackaging` |

### Model Deployment Template

Deployment template creates a new `ModelDeployment` object in ODAHU cluster.

For convenient steps pipelining Deployment template supports `fromPackagingID`. Read more in Inputs table below.

Inputs:
| Input              | Type     | Mandatory | Default | Description                                                             |
|--------------------|----------|-----------|---------|-------------------------------------------------------------------------|
| deploymentManifest | artifact | **yes**   |         | [YAML Deployment Manifest](https://docs.odahu.org/ref_deployments.html) |
| fromPackagingID    | string   | no        |         | `fromPackagingID` points to a packaging that produced a model image. If specified "image" field will be overridden by the result image from referenced Packaging |

Outputs:
| Output        | Type   | Description                     |
|---------------|--------|---------------------------------|
| deployment_id | string | ID of created `ModelDeployment` |

## Examples

1. [Model Train-Pack-Deploy pipeline with building dynamic Training manifest 
   in a separate Python step](examples/python-manifest-generation.workflow.yaml)

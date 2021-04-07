# ODAHU Argo Templates
ODAHU extensions for Argo https://github.com/argoproj/ 

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
To access ODAHU API any of Argo ODAHU templates require a secret with the following structure:
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

By default ODAHU steps will look for secret named `odahu-credentials`, but it can be customized by 
`odahuCredentialsSecret` parameter.

## Examples

1. [Model Train-Pack-Deploy pipeline with building dynamic Training manifest 
   in a separate Python step](examples/python-manifest-generation.workflow.yaml)

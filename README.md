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

## Examples

1. [Model Train-Pack-Deploy pipeline with building dynamic Training manifest 
   in a separate Python step](examples/python-manifest-generation.workflow.yaml)

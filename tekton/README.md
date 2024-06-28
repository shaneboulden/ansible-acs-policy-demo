## tekton

Tekton tasks and pipelines to update Red Hat Advanced Cluster Security for Kubernetes (RHACS) policies stored in a Git repository.

## getting started

Create a secret holding the RHACS URL and token:
```
$ oc create secret tkn-rhacs -n acs-central --from-literal=acs_url="acs.example.com" --from-literal=acs_token="eygh..."
```

Create pipeline resources:
```
$ oc apply --recursive -n acs-central deploy/
```

Create a pipeline run with the location of your policies:
```
$ cat pipeline-run.yaml

apiVersion: tekton.dev/v1beta1
kind: PipelineRun
metadata:
  generateName: update-rhacs-policies
spec:
  params: 
    - name: "git-repo-revision"
      value: "main"
    - name: "git-repo-url"
      value: "https://github.com/shaneboulden/ansible-acs-policy-demo.git"
    - name: "context-dir"
      value: "/"
    - name: "project-dir"
      value: "."
    - name: "rhacs-secret"
      value: "tkn-rhacs"
  pipelineRef:
    name: update-rhacs-policies
  workspaces:
    - name: ansible
      persistentVolumeClaim:
        claimName: pipeline-pvc
```
Start the pipeline:
```
$ oc create -f pipeline-run.yaml
```
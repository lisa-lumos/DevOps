# 6. Variables, JSON, YAML
In DevOps, there are various tools and technologies, that use JSON/Yaml for data exchange. 

## Variables
Temporary stores data in the ram. They live in a program. 

If I create a variable in Terminal, it lives in this Terminal, and will be gone if I close this Terminal. 

```console
(base) lisa@mac16 ~ % var1="DevOps" # note that no space before/after the = sign

(base) lisa@mac16 ~ % echo $var1 # retrieve the variable
DevOps

(base) lisa@mac16 ~ % echo "I am learning $var1" # note the double quotes
I am learning DevOps

(base) lisa@mac16 ~ % echo 'I am learning $var1' # note single quotes remove the meaning of $ sign
I am learning $var1

(base) lisa@mac16 ~ % NUM=123

(base) lisa@mac16 ~ % echo $NUM       
123
```

## Variables in Python
skipped.

## JSON & YAML
Create a dict in Python: 
```python
devops = {
    "skill": "DevOps",
    "Year": 2023,
    "Tech": {
        "Cloud": "AWS",
        "Containers": "K8s",
        "CICD": "Jenkins",
        "GitOps": [
            "GitLab",
            "ArgoCD",
            "Tekton"
        ]
    }
}
```

Copy the content (after = sign) of this devops dict into the left pane of https://jsoneditoronline.org/#left=local.zahemu&right=local.tabema. Click "copy >", and see its corresponding JSON text/tree in the right pane:
```json
{
  "skill": "DevOps",
  "Year": 2023,
  "Tech": {
    "Cloud": "AWS",
    "Containers": "K8s",
    "CICD": "Jenkins",
    "GitOps": [
      "GitLab",
      "ArgoCD",
      "Tekton"
    ]
  }
}
```

Its corresponding YAML text, which is much more cleaner:
```yml
devops:
  skill: DevOps
  Year: 2023
  Tech: 
    Cloud: AWS
    Containers: K8s
    CICD: Jenkins
    GitOps: 
      - GitLab
      - ArgoCD
      - Tekton
```

So, most of the tools/configs uses YAML to write configurations, while use JSON to read data. A devops engineer should at least be able to write YAML, and read JSON. 

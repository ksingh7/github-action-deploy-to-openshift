# github-action-deploy-to-openshift
Github Action App (python) to OpenShift

```
export SA=github-actions-sa
oc create sa $SA

# Now, we have to find the name of the secret in which the Service Account's apiserver token is stored.

export SECRETS=$(oc get sa $SA -o jsonpath='{.secrets[*].name}{"\n"}') && echo $SECRETS
# Select the one with "token" in the name - the other is for the container registry.
export SECRET_NAME=$(printf "%s\n" $SECRETS | grep "token") && echo $SECRET_NAME

# Get the token from the secret. 
export ENCODED_TOKEN=$(oc get secret $SECRET_NAME -o jsonpath='{.data.token}{"\n"}') && echo $ENCODED_TOKEN;

export OPENSHIFT_TOKEN=$(echo $ENCODED_TOKEN | base64 -d) && echo $OPENSHIFT_TOKEN

oc policy add-role-to-user edit -z $SA

export OPENSHIFT_SERVER_URL=$(oc whoami --show-server)



brew install gh
gh auth login --hostname github.com
gh secret -R github.com/ksingh7/github-action-deploy-to-openshift list

gh secret -R github.com/ksingh7/github-action-deploy-to-openshift set OPENSHIFT_SERVER_URL -b"${OPENSHIFT_SERVER_URL}"
gh secret -R github.com/ksingh7/github-action-deploy-to-openshift set OPENSHIFT_TOKEN -b"${OPENSHIFT_TOKEN}"

export QUAY_PULL_SECRET=''
export DOCKER_USERNAME=karansingh
export DOCKER_PASSWORD=
gh secret -R github.com/ksingh7/github-action-deploy-to-openshift set QUAY_PULL_SECRET -b"${QUAY_PULL_SECRET}"
gh secret -R github.com/ksingh7/github-action-deploy-to-openshift set DOCKER_USERNAME -b"${DOCKER_USERNAME}"
gh secret -R github.com/ksingh7/github-action-deploy-to-openshift set DOCKER_PASSWORD -b"${DOCKER_PASSWORD}"
gh secret -R github.com/ksingh7/github-action-deploy-to-openshift list

```
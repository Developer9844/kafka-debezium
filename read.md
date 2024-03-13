kubectl create -f 'https://strimzi.io/install/latest?namespace=kafka' -n



# create secret file for docker creds
kubectl create secret generic regcred \
    --from-file=.dockerconfigjson=~/.docker/config.json \
    --type=kubernetes.io/dockerconfigjson

# or
base64 -w 0 ~/.docker/config.json

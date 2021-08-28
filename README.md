# Build your first Operator with Kubebuilder and Okteto

This code is part of the talk I gave on how to build operators with Kubebuilder and Okteto, as part of [DevOps India Conference 2021](https://www.youtube.com/watch?v=HBVYE5BgAS4). 

This demo uses the following tech:
- GitHub Codespaces
- Kubebuilder
- Okteto
- Civo
- Go

The included codespace includes everything you need.  You only need to bring your own Kubernetes cluster (I recommend you use [Civo](https://civo.com)) and you'll be ready to roll.

# Develop the Conference Manager Operator

## Initialize the operator
1. Init your go module: `go mod init github.com/kubebuilder-talks`
2. Init the operator: `kubebuilder init --plugins go/v3 --domain example.org --owner "Ramiro Berrelleza" --skip-go-version-check`
3. Create the  `Talk`  api: `kubebuilder create api  --group conference --version v1beta1 --kind Talk` (say yes to everything) 
## Build the first version
1. Login to okteto, so we can use the remote build service: `okteto login --token $OKTETO_TOKEN` 
2. Login to civo and get your kubeconfig: `civo apikey add dev $CIVO_API && civo kubernetes config $CLUSTER_ID --save`
3. Update `api/v1beta1/talk_types.go` and add the `Title` and `Speaker` fields to `TalkSpec` 

4. Build the binaries and types, and install the CRDs: `make install` 
5. Build the image using the okteto cli (this way, we don't have to run the docker daemon in the codespace): `okteto build -t ramiro/devops-india-kubebuilder-okteto:dev`
6. Deploy the controller: `make deploy IMG=ramiro/devops-india-kubebuilder-okteto:dev`

## Create a talk

1. Update `config/samples/conference_v1beta1_talk.yaml` and add a `title` and a `speaker` to the sample yaml.
2. Create it `kubectl apply -f `config/samples/conference_v1beta1_talk.yaml` 
3. Check the resource: `kubectl get talk-sample -oyaml`

## Extra points

Add business logic inside the reconciliaton loop to check if a talk has finished, and update the status accordingly.

FROM golang:1.17-buster as build

WORKDIR /builder

ENV KUBEBUILDER_VERSION 3.1.0
ENV CIVO_VERSION 0.7.30
ENV OKTETO_VERSION 1.13.6
ENV KUBECTL_VERSION 1.21.0
ENV HELM_VERSION 3.6.3
ENV KUSTOMIZE_VERSION 4.1.2


# installing kubectl
RUN curl -sLf --retry 3 -o kubectl https://storage.googleapis.com/kubernetes-release/release/v${KUBECTL_VERSION}/bin/linux/amd64/kubectl && \
    cp kubectl /usr/local/bin/kubectl && \
    chmod +x /usr/local/bin/kubectl && \
    /usr/local/bin/kubectl version --client=true

# installing helm
RUN curl -sLf --retry 3 -o helm.tar.gz https://get.helm.sh/helm-v${HELM_VERSION}-linux-amd64.tar.gz && \
    mkdir -p helm && tar -C helm -xf helm.tar.gz && \
    cp helm/linux-amd64/helm /usr/local/bin/helm && \
    chmod +x /usr/local/bin/helm && \
    /usr/local/bin/helm version

# installing kustomize
RUN curl -sLf --retry 3 -o kustomize.tar.gz https://github.com/kubernetes-sigs/kustomize/releases/download/kustomize%2Fv${KUSTOMIZE_VERSION}/kustomize_v${KUSTOMIZE_VERSION}_linux_amd64.tar.gz && \
  tar -xvzf kustomize.tar.gz -C /usr/local/bin && \
  chmod +x /usr/local/bin/kustomize && \
  /usr/local/bin/kustomize version 

# installing okteto
RUN curl -sLf --retry 3 -o okteto https://github.com/okteto/okteto/releases/download/${OKTETO_VERSION}/okteto-Linux-x86_64 && \
    cp okteto /usr/local/bin/okteto && \
    chmod +x /usr/local/bin/okteto && \
    /usr/local/bin/okteto version

# installing kubebuilder
RUN curl -sLf --retry 3 -o kubebuilder https://github.com/kubernetes-sigs/kubebuilder/releases/download/v${KUBEBUILDER_VERSION}/kubebuilder_linux_amd64 && \
    chmod +x kubebuilder && \
    mv kubebuilder /usr/local/bin/ && \
    /usr/local/bin/kubebuilder version

# installing civo
RUN curl -sLf --retry 3 -o civo.tar.gz https://github.com/civo/cli/releases/download/v${CIVO_VERSION}/civo-${CIVO_VERSION}-linux-amd64.tar.gz  \
    && tar -xvf civo.tar.gz \ 
    && chmod +x civo \
    && mv civo /usr/local/bin \
    && /usr/local/bin/civo version

FROM okteto/golang:1 

COPY --from=build /usr/local/bin/okteto /usr/local/bin/okteto
COPY --from=build /usr/local/bin/kubectl /usr/local/bin/kubectl
COPY --from=build /usr/local/bin/helm /usr/local/bin/helm
COPY --from=build /usr/local/bin/kustomize /usr/local/bin/kustomize
COPY --from=build /usr/local/bin/civo /usr/local/bin/civo
COPY --from=build /usr/local/bin/kubebuilder /usr/local/bin/kubebuilder
COPY --from=docker:20 /usr/local/bin/docker /usr/local/bin/docker
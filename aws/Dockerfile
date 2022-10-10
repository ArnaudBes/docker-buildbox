FROM python:3.10.7-slim-buster as base
LABEL maintainer="Frank Pavageau <fpavageau@ekino.com>"

ARG TASKFILE_VERSION
ARG HELM_DIFF_VERSION
ARG HELM_VERSION
ARG KUBECTL_VERSION
ARG KUBENT_VERSION
ARG KUBESCORE_VERSION
ARG KUSTOMIZATION_VERSION
ARG TRIVY_VERSION

FROM base as base-amd64
ARG AWSCLI_ARCH="x86_64"
ARG HELM_ARCH="linux-amd64"
ARG KUBECTL_ARCH="linux/amd64"
ARG KUBENT_ARCH="linux-amd64"
ARG KUBESCORE_ARCH="linux_amd64"
ARG KUSTOMIZATION_ARCH="linux_amd64"
ARG TRIVY_ARCH="Linux-64bit"

FROM base as base-arm64
ARG AWSCLI_ARCH="aarch64"
ARG HELM_ARCH="linux-arm64"
ARG KUBECTL_ARCH="linux/arm64"
ARG KUBENT_ARCH="linux-arm64"
ARG KUBESCORE_ARCH="linux_arm64"
ARG KUSTOMIZATION_ARCH="linux_arm64"
ARG TRIVY_ARCH="Linux-ARM64"

FROM base-$TARGETARCH
ARG AWSCLI_URL="https://awscli.amazonaws.com/awscli-exe-linux-${AWSCLI_ARCH}.zip"

RUN echo "Installing AWS Cli" && \
    apt-get update -qq && apt-get install -qq -y curl wget git make rsync zip jq gcc && \
    pip install -U pip && \
    pip install pipenv boto3 awsebcli PyYAML && \
    curl -sSL https://taskfile.dev/install.sh | sh -s v${TASKFILE_VERSION} && \
    curl ${AWSCLI_URL} -o "awscliv2.zip" && \
    unzip awscliv2.zip && \
    ./aws/install && \
    rm -f awscliv2.zip && rm -rf aws && \
    echo "Done installing AWS Cli" && \
    apt-get -qq -y autoremove && \
    apt-get -qq clean && apt-get -qq purge && \
    rm -rf /var/lib/apt/lists/* /var/lib/dpkg/*-old && \
    echo "Adding an up to date mime-types definition file" && \
    curl -sSL https://salsa.debian.org/debian/mime-support/raw/master/mime.types -o /etc/mime.types

RUN curl -LO https://storage.googleapis.com/kubernetes-release/release/v${KUBECTL_VERSION}/bin/${KUBECTL_ARCH}/kubectl && \
    chmod +x ./kubectl && \
    mv ./kubectl /usr/bin/kubectl

RUN wget https://github.com/kubernetes-sigs/kustomize/releases/download/kustomize%2Fv${KUSTOMIZATION_VERSION}/kustomize_v${KUSTOMIZATION_VERSION}_${KUSTOMIZATION_ARCH}.tar.gz && \
    tar -xf kustomize_v${KUSTOMIZATION_VERSION}_${KUSTOMIZATION_ARCH}.tar.gz && \
    rm kustomize_v${KUSTOMIZATION_VERSION}_${KUSTOMIZATION_ARCH}.tar.gz && \
    mv ./kustomize /usr/bin/kustomize

RUN wget https://github.com/zegl/kube-score/releases/download/v${KUBESCORE_VERSION}/kube-score_${KUBESCORE_VERSION}_${KUBESCORE_ARCH} && \
    chmod +x kube-score_${KUBESCORE_VERSION}_${KUBESCORE_ARCH}  && \
    mv kube-score_${KUBESCORE_VERSION}_${KUBESCORE_ARCH} /usr/bin/kube-score

RUN wget https://github.com/doitintl/kube-no-trouble/releases/download/${KUBENT_VERSION}/kubent-${KUBENT_VERSION}-${KUBENT_ARCH}.tar.gz && \
    tar xf kubent-${KUBENT_VERSION}-${KUBENT_ARCH}.tar.gz && \
    rm kubent-${KUBENT_VERSION}-${KUBENT_ARCH}.tar.gz && \
    mv kubent /usr/bin/kubent

RUN wget https://get.helm.sh/helm-v${HELM_VERSION}-${HELM_ARCH}.tar.gz && \
    tar xf helm-v${HELM_VERSION}-${HELM_ARCH}.tar.gz && rm helm-v${HELM_VERSION}-${HELM_ARCH}.tar.gz && \
    mv ${HELM_ARCH}/helm /usr/local/bin/helm

RUN wget https://github.com/aquasecurity/trivy/releases/download/v${TRIVY_VERSION}/trivy_${TRIVY_VERSION}_${TRIVY_ARCH}.tar.gz && \
    tar xf trivy_${TRIVY_VERSION}_${TRIVY_ARCH}.tar.gz && \
    mv trivy /usr/bin/trivy

RUN helm plugin install --version $HELM_DIFF_VERSION https://github.com/databus23/helm-diff

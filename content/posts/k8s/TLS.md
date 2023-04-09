# TLS

了解如何使用传输层安全性（ TLS ）保护集群中的流量。

---

## [管理集群中的 TLS 认证](https://kubernetes.io/zh-cn/docs/tasks/tls/managing-tls-in-a-cluster/)
Kubernetes 提供 `certificates.k8s.io` API，可让你配置由你控制的证书颁发机构（CA） 签名的 TLS 证书。 你的工作负载可以使用这些 CA 和证书来建立信任。

`certificates.k8s.io` API使用的协议类似于 [ACME 草案](https://github.com/ietf-wg-acme/acme/)。

**说明：**

使用 `certificates.k8s.io` API 创建的证书由指定 [CA](https://kubernetes.io/zh-cn/docs/tasks/tls/managing-tls-in-a-cluster/#configuring-your-cluster-to-provide-signing) 颁发。 将集群配置为使用集群根目录 CA 可以达到这个目的，但是你永远不要依赖这一假定。 不要以为这些证书将针对群根目录 CA 进行验证。

### 准备开始[](https://kubernetes.io/zh-cn/docs/tasks/tls/managing-tls-in-a-cluster/#%E5%87%86%E5%A4%87%E5%BC%80%E5%A7%8B)

你必须拥有一个 Kubernetes 的集群，同时你的 Kubernetes 集群必须带有 kubectl 命令行工具。 建议在至少有两个节点的集群上运行本教程，且这些节点不作为控制平面主机。 如果你还没有集群，你可以通过 [Minikube](https://minikube.sigs.k8s.io/docs/tutorials/multi_node/) 构建一个你自己的集群，或者你可以使用下面任意一个 Kubernetes 工具构建：

- [Killercoda](https://killercoda.com/playgrounds/scenario/kubernetes)
- [玩转 Kubernetes](http://labs.play-with-k8s.com/)

你需要 `cfssl` 工具。 你可以从 [https://github.com/cloudflare/cfssl/releases](https://github.com/cloudflare/cfssl/releases) 下载 `cfssl`。

本文中某些步骤使用 `jq` 工具。如果你没有 `jq`，你可以通过操作系统的软件源安装， 或者从 [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/) 获取。

### 集群中的 TLS 信任[](https://kubernetes.io/zh-cn/docs/tasks/tls/managing-tls-in-a-cluster/#%E9%9B%86%E7%BE%A4%E4%B8%AD%E7%9A%84-tls-%E4%BF%A1%E4%BB%BB)

信任 Pod 中运行的应用程序所提供的[自定义 CA](https://kubernetes.io/zh-cn/docs/tasks/tls/managing-tls-in-a-cluster/#configuring-your-cluster-to-provide-signing) 通常需要一些额外的应用程序配置。 你需要将 CA 证书包添加到 TLS 客户端或服务器信任的 CA 证书列表中。 例如，你可以使用 Golang TLS 配置通过解析证书链并将解析的证书添加到 [`tls.Config`](https://pkg.go.dev/crypto/tls#Config) 结构中的 `RootCAs` 字段中。

**说明：**

即使自定义 CA 证书可能包含在文件系统中（在 ConfigMap `kube-root-ca.crt` 中）， 除了验证内部 Kubernetes 端点之外，你不应将该证书颁发机构用于任何目的。 内部 Kubernetes 端点的一个示例是默认命名空间中名为 `kubernetes` 的服务。

如果你想为你的工作负载使用自定义证书颁发机构，你应该单独生成该 CA， 并使用你的 Pod 有读权限的 [ConfigMap](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-pod-configmap) 分发该 CA 证书。

### 请求证书[](https://kubernetes.io/zh-cn/docs/tasks/tls/managing-tls-in-a-cluster/#%E8%AF%B7%E6%B1%82%E8%AF%81%E4%B9%A6)

以下部分演示如何为通过 DNS 访问的 Kubernetes 服务创建 TLS 证书。

**说明：** 本教程使用 CFSSL：Cloudflare's PKI 和 TLS 工具包 [点击此处](https://blog.cloudflare.com/introducing-cfssl/)了解更多信息。

### 创建证书签名请求[](https://kubernetes.io/zh-cn/docs/tasks/tls/managing-tls-in-a-cluster/#%E5%88%9B%E5%BB%BA%E8%AF%81%E4%B9%A6%E7%AD%BE%E5%90%8D%E8%AF%B7%E6%B1%82)

通过运行以下命令生成私钥和证书签名请求（或 CSR）:

```shell
cat <<EOF | cfssl genkey - | cfssljson -bare server
{
  "hosts": [
    "my-svc.my-namespace.svc.cluster.local",
    "my-pod.my-namespace.pod.cluster.local",
    "192.0.2.24",
    "10.0.34.2"
  ],
  "CN": "my-pod.my-namespace.pod.cluster.local",
  "key": {
    "algo": "ecdsa",
    "size": 256
  }
}
EOF
```

其中 `192.0.2.24` 是服务的集群 IP，`my-svc.my-namespace.svc.cluster.local` 是服务的 DNS 名称，`10.0.34.2` 是 Pod 的 IP，而 `my-pod.my-namespace.pod.cluster.local` 是 Pod 的 DNS 名称。 你能看到的输出类似于：

```
2022/02/01 11:45:32 [INFO] generate received request
2022/02/01 11:45:32 [INFO] received CSR
2022/02/01 11:45:32 [INFO] generating key: ecdsa-256
2022/02/01 11:45:32 [INFO] encoded CSR
```

此命令生成两个文件；它生成包含 PEM 编码 [PKCS#10](https://tools.ietf.org/html/rfc2986) 证书请求的 `server.csr`， 以及 PEM 编码密钥的 `server-key.pem`，用于待生成的证书。

### 创建证书签名请求（CSR）对象发送到 Kubernetes API[](https://kubernetes.io/zh-cn/docs/tasks/tls/managing-tls-in-a-cluster/#%E5%88%9B%E5%BB%BA%E8%AF%81%E4%B9%A6%E7%AD%BE%E5%90%8D%E8%AF%B7%E6%B1%82-csr-%E5%AF%B9%E8%B1%A1%E5%8F%91%E9%80%81%E5%88%B0-kubernetes-api)

你可以使用以下命令创建 CSR 清单（YAML 格式），并发送到 API 服务器：

```shell
cat <<EOF | kubectl apply -f -
apiVersion: certificates.k8s.io/v1
kind: CertificateSigningRequest
metadata:
  name: my-svc.my-namespace
spec:
  request: $(cat server.csr | base64 | tr -d '\n')
  signerName: example.com/serving
  usages:
  - digital signature
  - key encipherment
  - server auth
EOF
```

请注意，在步骤 1 中创建的 `server.csr` 文件是 base64 编码并存储在 `.spec.request` 字段中的。你还要求提供 “digital signature（数字签名）”， “密钥加密（key encipherment）” 和 “服务器身份验证（server auth）” 密钥用途， 由 `example.com/serving` 示例签名程序签名的证书。 你也可以要求使用特定的 `signerName`。更多信息可参阅 [支持的签署者名称](https://kubernetes.io/zh-cn/docs/reference/access-authn-authz/certificate-signing-requests/#signers)。

在 API server 中可以看到这些 CSR 处于 Pending 状态。执行下面的命令你将可以看到：

```shell
kubectl describe csr my-svc.my-namespace
```

```none
Name:                   my-svc.my-namespace
Labels:                 <none>
Annotations:            <none>
CreationTimestamp:      Tue, 01 Feb 2022 11:49:15 -0500
Requesting User:        yourname@example.com
Signer:                 example.com/serving
Status:                 Pending
Subject:
        Common Name:    my-pod.my-namespace.pod.cluster.local
        Serial Number:
Subject Alternative Names:
        DNS Names:      my-pod.my-namespace.pod.cluster.local
                        my-svc.my-namespace.svc.cluster.local
        IP Addresses:   192.0.2.24
                        10.0.34.2
Events: <none>
```

### 批准证书签名请求（CSR）[](https://kubernetes.io/zh-cn/docs/tasks/tls/managing-tls-in-a-cluster/#get-the-certificate-signing-request-approved)

[证书签名请求](https://kubernetes.io/zh-cn/docs/reference/access-authn-authz/certificate-signing-requests/) 的批准或者是通过自动批准过程完成的，或由集群管理员一次性完成。 如果你被授权批准证书请求，你可以使用 `kubectl` 来手动完成此操作；例如：

```shell
kubectl certificate approve my-svc.my-namespace
```

```none
certificatesigningrequest.certificates.k8s.io/my-svc.my-namespace approved
```

你现在应该能看到如下输出：

```shell
kubectl get csr
```

```none
NAME                  AGE   SIGNERNAME            REQUESTOR              REQUESTEDDURATION   CONDITION
my-svc.my-namespace   10m   example.com/serving   yourname@example.com   <none>              Approved
```

这意味着证书请求已被批准，并正在等待请求的签名者对其签名。

### 签名证书签名请求（CSR）[](https://kubernetes.io/zh-cn/docs/tasks/tls/managing-tls-in-a-cluster/#sign-the-certificate-signing-request)

接下来，你将扮演证书签署者的角色，颁发证书并将其上传到 API 服务器。

签名者通常会使用其 `signerName` 查看对象的 CertificateSigningRequest API， 检查它们是否已被批准，为这些请求签署证书，并使用已颁发的证书更新 API 对象状态。

#### 创建证书颁发机构[](https://kubernetes.io/zh-cn/docs/tasks/tls/managing-tls-in-a-cluster/#%E5%88%9B%E5%BB%BA%E8%AF%81%E4%B9%A6%E9%A2%81%E5%8F%91%E6%9C%BA%E6%9E%84)

你需要授权在新证书上提供数字签名。

首先，通过运行以下命令创建签名证书：

```shell
cat <<EOF | cfssl gencert -initca - | cfssljson -bare ca
{
  "CN": "My Example Signer",
  "key": {
    "algo": "rsa",
    "size": 2048
  }
}
EOF
```

你应该看到类似于以下的输出：

```none
2022/02/01 11:50:39 [INFO] generating a new CA key and certificate from CSR
2022/02/01 11:50:39 [INFO] generate received request
2022/02/01 11:50:39 [INFO] received CSR
2022/02/01 11:50:39 [INFO] generating key: rsa-2048
2022/02/01 11:50:39 [INFO] encoded CSR
2022/02/01 11:50:39 [INFO] signed certificate with serial number 263983151013686720899716354349605500797834580472
```

这会产生一个证书颁发机构密钥文件（`ca-key.pem`）和证书（`ca.pem`）。

#### 颁发证书[](https://kubernetes.io/zh-cn/docs/tasks/tls/managing-tls-in-a-cluster/#%E9%A2%81%E5%8F%91%E8%AF%81%E4%B9%A6)

[`tls/server-signing-config.json`](https://raw.githubusercontent.com/kubernetes/website/main/content/zh-cn/examples/tls/server-signing-config.json) ![](https://d33wubrfki0l68.cloudfront.net/0901162ab78eb4ff2e9e5dc8b17c3824befc91a6/44ccd/images/copycode.svg "Copy tls/server-signing-config.json to clipboard")

```json
{
    "signing": {
        "default": {
            "usages": [
                "digital signature",
                "key encipherment",
                "server auth"
            ],
            "expiry": "876000h",
            "ca_constraint": {
                "is_ca": false
            }
        }
    }
}
```

使用 `server-signing-config.json` 签名配置、证书颁发机构密钥文件和证书来签署证书请求：

```shell
kubectl get csr my-svc.my-namespace -o jsonpath='{.spec.request}' | \
  base64 --decode | \
  cfssl sign -ca ca.pem -ca-key ca-key.pem -config server-signing-config.json - | \
  cfssljson -bare ca-signed-server
```

你应该看到类似于以下的输出：

```
2022/02/01 11:52:26 [INFO] signed certificate with serial number 576048928624926584381415936700914530534472870337
```

这会生成一个签名的服务证书文件，`ca-signed-server.pem`。

#### 上传签名证书[](https://kubernetes.io/zh-cn/docs/tasks/tls/managing-tls-in-a-cluster/#%E4%B8%8A%E4%BC%A0%E7%AD%BE%E5%90%8D%E8%AF%81%E4%B9%A6)

最后，在 API 对象的状态中填充签名证书：

```shell
kubectl get csr my-svc.my-namespace -o json | \
  jq '.status.certificate = "'$(base64 ca-signed-server.pem | tr -d '\n')'"' | \
  kubectl replace --raw /apis/certificates.k8s.io/v1/certificatesigningrequests/my-svc.my-namespace/status -f -
```

**说明：**

这使用命令行工具 [`jq`](https://stedolan.github.io/jq/) 在 `.status.certificate` 字段中填充 base64 编码的内容。 如果你没有 `jq` 工具，你还可以将 JSON 输出保存到文件中，手动填充此字段，然后上传结果文件。

批准 CSR 并上传签名证书后，运行：

```shell
kubectl get csr
```

输入类似于：

```none
NAME                  AGE   SIGNERNAME            REQUESTOR              REQUESTEDDURATION   CONDITION
my-svc.my-namespace   20m   example.com/serving   yourname@example.com   <none>              Approved,Issued
```

### 下载证书并使用它[](https://kubernetes.io/zh-cn/docs/tasks/tls/managing-tls-in-a-cluster/#%E4%B8%8B%E8%BD%BD%E8%AF%81%E4%B9%A6%E5%B9%B6%E4%BD%BF%E7%94%A8%E5%AE%83)

现在，作为请求用户，你可以通过运行以下命令下载颁发的证书并将其保存到 `server.crt` 文件中：

CSR 被签署并获得批准后，你应该看到以下内容：

```shell
kubectl get csr my-svc.my-namespace -o jsonpath='{.status.certificate}' \
    | base64 --decode > server.crt
```

现在你可以将 `server.crt` 和 `server-key.pem` 填充到 [Secret](https://kubernetes.io/zh-cn/docs/concepts/configuration/secret/) 中， 稍后你可以将其挂载到 Pod 中（例如，用于提供 HTTPS 的网络服务器）。

```shell
kubectl create secret tls server --cert server.crt --key server-key.pem
```

```none
secret/server created
```

最后，你可以将 `ca.pem` 填充到 [ConfigMap](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-pod-configmap/) 并将其用作信任根来验证服务证书：

```shell
kubectl create configmap example-serving-ca --from-file ca.crt=ca.pem
```

```none
configmap/example-serving-ca created
```

### 批准证书签名请求（CSR）[](https://kubernetes.io/zh-cn/docs/tasks/tls/managing-tls-in-a-cluster/#approving-certificate-signing-requests)

Kubernetes 管理员（具有适当权限）可以使用 `kubectl certificate approve` 和 `kubectl certificate deny` 命令手动批准（或拒绝）证书签名请求（CSR）。 但是，如果你打算大量使用此 API，则可以考虑编写自动化的证书控制器。

**注意：**

批准证书 CSR 的能力决定了在你的环境中谁信任谁。 不应广泛或轻率地授予批准 CSR 的能力。

在授予 `approve` 权限之前，你应该确保自己充分了解批准人的验证要求**和**颁发特定证书的后果。

无论上述机器或人使用 kubectl，“批准者”的作用是验证 CSR 满足如下两个要求：

1. CSR 的 subject 控制用于签署 CSR 的私钥。这解决了伪装成授权主体的第三方的威胁。 在上述示例中，此步骤将验证该 Pod 控制了用于生成 CSR 的私钥。
2. CSR 的 subject 被授权在请求的上下文中执行。 这点用于处理不期望的主体被加入集群的威胁。 在上述示例中，此步骤将是验证该 Pod 是否被允许加入到所请求的服务中。

当且仅当满足这两个要求时，审批者应该批准 CSR，否则拒绝 CSR。

有关证书批准和访问控制的更多信息， 请阅读[证书签名请求](https://kubernetes.io/zh-cn/docs/reference/access-authn-authz/certificate-signing-requests/)参考页。

### 给集群管理员的一个建议[](https://kubernetes.io/zh-cn/docs/tasks/tls/managing-tls-in-a-cluster/#%E7%BB%99%E9%9B%86%E7%BE%A4%E7%AE%A1%E7%90%86%E5%91%98%E7%9A%84%E4%B8%80%E4%B8%AA%E5%BB%BA%E8%AE%AE)

本页面假设已经为 certificates API 配置了签名者。 Kubernetes 控制器管理器提供了一个签名者的默认实现。要启用它，请为控制器管理器设置 `--cluster-signing-cert-file` 和 `--cluster-signing-key-file` 参数， 使之取值为你的证书机构的密钥对的路径。
## [手动轮换 CA 证书](https://kubernetes.io/zh-cn/docs/tasks/tls/manual-rotation-of-ca-certificates/)
本页展示如何手动轮换证书机构（CA）证书。

### 准备开始[](https://kubernetes.io/zh-cn/docs/tasks/tls/manual-rotation-of-ca-certificates/#%E5%87%86%E5%A4%87%E5%BC%80%E5%A7%8B)

你必须拥有一个 Kubernetes 的集群，同时你的 Kubernetes 集群必须带有 kubectl 命令行工具。 建议在至少有两个节点的集群上运行本教程，且这些节点不作为控制平面主机。 如果你还没有集群，你可以通过 [Minikube](https://minikube.sigs.k8s.io/docs/tutorials/multi_node/) 构建一个你自己的集群，或者你可以使用下面任意一个 Kubernetes 工具构建：

- [Killercoda](https://killercoda.com/playgrounds/scenario/kubernetes)
- [玩转 Kubernetes](http://labs.play-with-k8s.com/)

- 要了解 Kubernetes 中用户认证的更多信息，参阅 [认证](https://kubernetes.io/zh-cn/docs/reference/access-authn-authz/authentication)；
- 要了解与 CA 证书最佳实践有关的更多信息， 参阅[单根 CA](https://kubernetes.io/zh-cn/docs/setup/best-practices/certificates/#single-root-ca)。

### 手动轮换 CA 证书[](https://kubernetes.io/zh-cn/docs/tasks/tls/manual-rotation-of-ca-certificates/#rotate-the-ca-certificates-manually)

**注意：**

确保备份你的证书目录、配置文件以及其他必要文件。

这里的方法假定 Kubernetes 的控制面通过运行多个 API 服务器以高可用配置模式运行。 另一假定是 API 服务器可体面地终止，因而客户端可以彻底地与一个 API 服务器断开 连接并连接到另一个 API 服务器。

如果集群中只有一个 API 服务器，则在 API 服务器重启期间会经历服务中断期。

1. 将新的 CA 证书和私钥（例如：`ca.crt`、`ca.key`、`front-proxy-ca.crt` 和 `front-proxy-client.key`）分发到所有控制面节点，放在其 Kubernetes 证书目录下。

2. 更新 [kube-controller-manager](https://kubernetes.io/zh-cn/docs/reference/command-line-tools-reference/kube-controller-manager/) 的 `--root-ca-file` 标志，使之同时包含老的和新的 CA，之后重启 kube-controller-manager。
    
    自此刻起，所创建的所有[ServiceAccount](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-service-account/) 都会获得同时包含老的 CA 和新的 CA 的 Secret。
    
    **说明：**
    
    kube-controller-manager 标志 `--client-ca-file` 和 `--cluster-signing-cert-file` 所引用的文件不能是 CA 证书包。如果这些标志和 `--root-ca-file` 指向同一个 `ca.crt` 包文件 （包含老的和新的 CA 证书），你将会收到出错信息。 要解决这个问题，可以将新的 CA 证书复制到单独的文件中，并将 `--client-ca-file` 和 `--cluster-signing-cert-file` 标志指向该副本。一旦 `ca.crt` 不再是证书包文件， 就可以恢复有问题的标志指向 `ca.crt` 并删除该副本。
    
    kubeadm 的 [Issue 1350](https://github.com/kubernetes/kubeadm/issues/1350) 在跟踪一个导致 kube-controller-manager 无法接收 CA 证书包的问题。
    

3. 等待该控制器管理器更新服务账号 Secret 中的 `ca.crt`，使之同时包含老的和新的 CA 证书。
    
    如果在 API 服务器使用新的 CA 之前启动了新的 Pod，这些新的 Pod 也会获得此更新并且同时信任老的和新的 CA 证书。
    

4. 重启所有使用集群内配置的 Pod（例如：kube-proxy、CoreDNS 等），以便这些 Pod 能够使用与 ServiceAccount 相关联的 Secret 中的、已更新的证书机构数据。
    
    - 确保 CoreDNS、kube-proxy 和其他使用集群内配置的 Pod 都正按预期方式工作。
5. 将老的和新的 CA 都追加到 `kube-apiserver` 配置的 `--client-ca-file` 和 `--kubelet-certificate-authority` 标志所指的文件。
    
6. 将老的和新的 CA 都追加到 `kube-scheduler` 配置的 `--client-ca-file` 标志所指的文件。
    

7. 通过替换 `client-certificate-data` 和 `client-key-data` 中的内容，更新用户账号的证书。
    
    有关为独立用户账号创建证书的更多信息，可参阅 [为用户帐号配置证书](https://kubernetes.io/zh-cn/docs/setup/best-practices/certificates/#configure-certificates-for-user-accounts)。
    
    另外，还要更新 kubeconfig 文件中的 `certificate-authority-data` 节， 使之包含 Base64 编码的老的和新的证书机构数据。
    

8. 更新 [云控制器管理器（Cloud Controller Manager）](https://kubernetes.io/zh-cn/docs/concepts/architecture/cloud-controller/) 的 `--root-ca-file` 标志值，使之同时包含老的和新的 CA，之后重新启动 cloud-controller-manager。
    
    **说明：**
    
    如果你的集群中不包含 cloud-controller-manager，你可以略过这一步。
    

9. 遵循下列步骤执行滚动更新
    
    1. 重新启动所有其他[被聚合的 API 服务器](https://kubernetes.io/zh-cn/docs/concepts/extend-kubernetes/api-extension/apiserver-aggregation/) 或者 Webhook 处理程序，使之信任新的 CA 证书。
        
    2. 在所有节点上更新 kubelet 配置中的 `clientCAFile` 所指文件以及 `kubelet.conf` 中的 `certificate-authority-data` 并重启 kubelet 以同时使用老的和新的 CA 证书。
        
        如果你的 kubelet 并未使用客户端证书轮换，则在所有节点上更新 `kubelet.conf` 中 `client-certificate-data` 和 `client-key-data` 以及 kubelet 客户端证书文件（通常位于 `/var/lib/kubelet/pki` 目录下）
        
    
    3. 使用用新的 CA 签名的证书 （`apiserver.crt`、`apiserver-kubelet-client.crt` 和 `front-proxy-client.crt`） 来重启 API 服务器。 你可以使用现有的私钥，也可以使用新的私钥。 如果你改变了私钥，则要将更新的私钥也放到 Kubernetes 证书目录下。
    
    ```
      由于集群中的 Pod 既信任老的 CA 也信任新的 CA，Pod 中的客户端会经历短暂的连接断开状态，
    ```
    
    之后再使用新的 CA 所签名的证书连接到新的 API 服务器。
    
    ```
      * 重启 kube-scheduler 以使用并信任新的
     CA 证书。
    ```
    
    - 确保控制面组件的日志中没有 TLS 相关的错误信息。
    
    **说明：**
    
    ```
      要使用 `openssl` 命令行为集群生成新的证书和私钥，可参阅
      [证书（`openssl`）](/zh-cn/docs/tasks/administer-cluster/certificates/#openssl)。
      你也可以使用[`cfssl`](/zh-cn/docs/tasks/administer-cluster/certificates/#cfssl).
    ```
    
    4. 为 Daemonset 和 Deployment 添加注解，从而触发较安全的滚动更新，替换 Pod。
    
    ```shell
    for namespace in $(kubectl get namespace -o jsonpath='{.items[*].metadata.name}'); do
        for name in $(kubectl get deployments -n $namespace -o jsonpath='{.items[*].metadata.name}'); do
            kubectl patch deployment -n ${namespace} ${name} -p '{"spec":{"template":{"metadata":{"annotations":{"ca-rotation": "1"}}}}}';
        done
        for name in $(kubectl get daemonset -n $namespace -o jsonpath='{.items[*].metadata.name}'); do
            kubectl patch daemonset -n ${namespace} ${name} -p '{"spec":{"template":{"metadata":{"annotations":{"ca-rotation": "1"}}}}}';
        done
    done
    ```
    
    **说明：**
    
    ```
      要限制应用可能受到的并发干扰数量，
      可以参阅[配置 Pod 干扰预算](/zh-cn/docs/tasks/run-application/configure-pdb/)。
    ```
    
    ```
      取决于你在如何使用 StatefulSet，你可能需要对其执行类似的滚动替换操作。
    ```
    

10. 如果你的集群使用启动引导令牌来添加节点，则需要更新 `kube-public` 名字空间下的 ConfigMap `cluster-info`，使之包含新的 CA 证书。
    
    ```shell
    base64_encoded_ca="$(base64 -w0 /etc/kubernetes/pki/ca.crt)"
    
    kubectl get cm/cluster-info --namespace kube-public -o yaml | \
       /bin/sed "s/\(certificate-authority-data:\).*/\1 ${base64_encoded_ca}/" | \
       kubectl apply -f -
    ```
    

11. 验证集群的功能正常。
    
    1. 检查控制面组件以及 `kubelet` 和 `kube-proxy` 的日志，确保其中没有抛出 TLS 错误， 参阅[查看日志](https://kubernetes.io/zh-cn/docs/tasks/debug/debug-cluster/#looking-at-logs)。
        
    2. 验证被聚合的 API 服务器的日志，以及所有使用集群内配置的 Pod 的日志。
        

12. 完成集群功能的检查之后：
    
    1. 更新所有的服务账号令牌，使之仅包含新的 CA 证书。
        
        - 使用集群内 kubeconfig 的 Pod 最终也需要被重启，以获得新的服务账号 Secret 数据，这样就不会有 Pod 再依赖老的集群 CA。
    2. 从 kubeconfig 文件和 `--client-ca-file` 以及 `--root-ca-file` 标志所指向的文件 中去除老的 CA 数据，之后重启控制面组件。
        
    3. 在每个节点上，移除 `clientCAFile` 标志所指向的文件，以删除老的 CA 数据，并从 kubelet kubeconfig 文件中去掉老的 CA，重启 kubelet。 你应该用滚动更新的方式来执行这一步骤的操作。
        
        如果你的集群允许你执行这一变更，你也可以通过替换节点而不是重新配置节点的方式来将其上线。
## [为 kubelet 配置证书轮换](https://kubernetes.io/zh-cn/docs/tasks/tls/certificate-rotation/)

本文展示如何在 kubelet 中启用并配置证书轮换。

**特性状态：** `Kubernetes v1.19 [stable]`

### 准备开始[](https://kubernetes.io/zh-cn/docs/tasks/tls/certificate-rotation/#%E5%87%86%E5%A4%87%E5%BC%80%E5%A7%8B)

- 要求 Kubernetes 1.8.0 或更高的版本

### 概述[](https://kubernetes.io/zh-cn/docs/tasks/tls/certificate-rotation/#%E6%A6%82%E8%BF%B0)

Kubelet 使用证书进行 Kubernetes API 的认证。 默认情况下，这些证书的签发期限为一年，所以不需要太频繁地进行更新。

Kubernetes 包含特性 [kubelet 证书轮换](https://kubernetes.io/zh-cn/docs/reference/access-authn-authz/kubelet-tls-bootstrapping/)， 在当前证书即将过期时， 将自动生成新的秘钥，并从 Kubernetes API 申请新的证书。 一旦新的证书可用，它将被用于与 Kubernetes API 间的连接认证。

### 启用客户端证书轮换[](https://kubernetes.io/zh-cn/docs/tasks/tls/certificate-rotation/#%E5%90%AF%E7%94%A8%E5%AE%A2%E6%88%B7%E7%AB%AF%E8%AF%81%E4%B9%A6%E8%BD%AE%E6%8D%A2)

`kubelet` 进程接收 `--rotate-certificates` 参数，该参数决定 kubelet 在当前使用的 证书即将到期时，是否会自动申请新的证书。

`kube-controller-manager` 进程接收 `--cluster-signing-duration` 参数 （在 1.19 版本之前为 `--experimental-cluster-signing-duration`），用来 控制签发证书的有效期限。

### 理解证书轮换配置[](https://kubernetes.io/zh-cn/docs/tasks/tls/certificate-rotation/#%E7%90%86%E8%A7%A3%E8%AF%81%E4%B9%A6%E8%BD%AE%E6%8D%A2%E9%85%8D%E7%BD%AE)

当 kubelet 启动时，如被配置为自举（使用`--bootstrap-kubeconfig` 参数），kubelet 会使用其初始证书连接到 Kubernetes API ，并发送证书签名的请求。 可以通过以下方式查看证书签名请求的状态：

```shell
kubectl get csr
```

最初，来自节点上 kubelet 的证书签名请求处于 `Pending` 状态。 如果证书签名请求满足特定条件， 控制器管理器会自动批准，此时请求会处于 `Approved` 状态。 接下来，控制器管理器会签署证书， 证书的有效期限由 `--cluster-signing-duration` 参数指定，签署的证书会被附加到证书签名请求中。

Kubelet 会从 Kubernetes API 取回签署的证书，并将其写入磁盘，存储位置通过 `--cert-dir` 参数指定。 然后 kubelet 会使用新的证书连接到 Kubernetes API。

当签署的证书即将到期时，kubelet 会使用 Kubernetes API，自动发起新的证书签名请求。 该请求会发生在证书的有效时间剩下 30% 到 10% 之间的任意时间点。 同样地，控制器管理器会自动批准证书请求，并将签署的证书附加到证书签名请求中。 Kubelet 会从 Kubernetes API 取回签署的证书，并将其写入磁盘。 然后它会更新与 Kubernetes API 的连接，使用新的证书重新连接到 Kubernetes API。
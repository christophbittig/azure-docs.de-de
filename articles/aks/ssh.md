---
title: Zugreifen per SSH auf Azure Kubernetes Service-Clusterknoten (AKS)
description: Erfahren Sie, wie Sie eine SSH-Verbindung mit Azure Kubernetes Service-Clusterknoten (AKS) zur Problembehandlung und für Wartungsaufgaben erstellen.
services: container-service
ms.topic: article
ms.date: 05/17/2021
ms.custom: contperf-fy21q4
ms.openlocfilehash: 87e548cd39c7c064b11131c28790d8335bd942fb
ms.sourcegitcommit: 34aa13ead8299439af8b3fe4d1f0c89bde61a6db
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/18/2021
ms.locfileid: "122418411"
---
# <a name="connect-with-ssh-to-azure-kubernetes-service-aks-cluster-nodes-for-maintenance-or-troubleshooting"></a>Herstellen einer SSH-Verbindung mit Azure Kubernetes Service-Clusterknoten (AKS) zur Wartung oder Problembehandlung

Während des Lebenszyklus des Azure Kubernetes Service-Clusters (AKS) müssen Sie möglicherweise auf einen AKS-Knoten zugreifen. Dieser Zugriff kann zur Wartung, Protokollsammlung oder für andere Vorgänge der Problembehandlung erforderlich sein. Sie können mithilfe von SSH (Secure Shell) auf AKS-Knoten zugreifen, einschließlich Windows Server-Knoten. Außerdem können Sie [RDP-Verbindungen (Remotedesktopprotokoll) zum Herstellen einer Verbindung mit Windows Server-Knoten verwenden][aks-windows-rdp]. Aus Sicherheitsgründen werden die AKS-Knoten nicht im Internet verfügbar gemacht. Zum Herstellen einer SSH-Verbindung mit den AKS-Knoten verwenden Sie `kubectl debug` oder die private IP-Adresse.

In diesem Artikel wird gezeigt, wie Sie eine SSH-Verbindung mit einem AKS-Knoten erstellen.

## <a name="before-you-begin"></a>Voraussetzungen

Es wird vorausgesetzt, dass Sie über ein AKS-Cluster verfügen. Wenn Sie einen AKS-Cluster benötigen, erhalten Sie weitere Informationen im AKS-Schnellstart. Verwenden Sie dafür entweder die [Azure CLI][aks-quickstart-cli] oder das [Azure-Portal][aks-quickstart-portal].

In diesem Artikel wird auch vorausgesetzt, dass Sie über einen SSH-Schlüssel verfügen. Sie können einen SSH-Schlüssel mit [macOS oder Linux][ssh-nix] bzw. [Windows][ssh-windows] erstellen. Speichern Sie das Schlüsselpaar in einem OpenSSH-Format anstelle des PuTTY-Standardformats für private Schlüssel, wenn Sie PuttyGen zum Erstellen des Schlüsselpaars verwenden.

Außerdem muss mindestens die Version 2.0.64 der Azure-Befehlszeilenschnittstelle installiert und konfiguriert sein. Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI][install-azure-cli].

## <a name="create-the-ssh-connection-to-a-linux-node"></a>Erstellen der SSH-Verbindung mit einem Linux-Knoten

Verwenden Sie `kubectl debug`, um einen privilegierten Container auf Ihrem Knoten ausführen und so eine SSH-Verbindung mit einem AKS-Knoten zu erstellen. Verwenden Sie zum Auflisten Ihrer Knoten `kubectl get nodes`:

```output
$ kubectl get nodes -o wide

NAME                                STATUS   ROLES   AGE     VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                         KERNEL-VERSION     CONTAINER-RUNTIME
aks-nodepool1-12345678-vmss000000   Ready    agent   13m     v1.19.9   10.240.0.4    <none>        Ubuntu 18.04.5 LTS               5.4.0-1046-azure   containerd://1.4.4+azure
aks-nodepool1-12345678-vmss000001   Ready    agent   13m     v1.19.9   10.240.0.35   <none>        Ubuntu 18.04.5 LTS               5.4.0-1046-azure   containerd://1.4.4+azure
aksnpwin000000                      Ready    agent   87s     v1.19.9   10.240.0.67   <none>        Windows Server 2019 Datacenter   10.0.17763.1935    docker://19.3.1
```

Verwenden Sie `kubectl debug`, um ein Containerimage auf dem Knoten ausführen und eine Verbindung mit ihm herzustellen.

```azurecli-interactive
kubectl debug node/aks-nodepool1-12345678-vmss000000 -it --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
```

Mit diesem Befehl wird ein privilegierter Container auf Ihrem Knoten gestartet und über SSH eine Verbindung damit herstellt.

```output
$ kubectl debug node/aks-nodepool1-12345678-vmss000000 -it --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
Creating debugging pod node-debugger-aks-nodepool1-12345678-vmss000000-bkmmx with container debugger on node aks-nodepool1-12345678-vmss000000.
If you don't see a command prompt, try pressing enter.
root@aks-nodepool1-12345678-vmss000000:/#
```

Dieser privilegierte Container gewährt Zugriff auf den Knoten.

## <a name="create-the-ssh-connection-to-a-windows-node"></a>Erstellen der SSH-Verbindung mit einem Windows-Knoten

Derzeit können Sie mithilfe von SSH keine direkte Verbindung mit einem Windows Server-Knoten mittels `kubectl debug` herstellen. Stattdessen müssen Sie zunächst eine Verbindung mit einem anderen Knoten im Cluster herstellen und dann von diesem Knoten über SSH eine Verbindung mit dem Windows Server-Knoten herstellen. Alternativ können Sie eine [Verbindung mit Windows Server-Knoten über RDP-Verbindungen (Remotedesktopprotokoll) herstellen][aks-windows-rdp], anstatt über SSH.

Verwenden Sie `kubectl debug`, um eine Verbindung mit einem anderen Knoten im Cluster herzustellen. Weitere Informationen finden Sie unter [Erstellen der SSH-Verbindung mit einem Linux-Knoten][ssh-linux-kubectl-debug].

Verwenden Sie die SSH-Schlüssel, die sie beim Erstellen des AKS-Clusters erhalten haben, und die interne IP-Adresse des Windows Server-Knotens, um die SSH-Verbindung mit dem Windows Server-Knoten von einem anderen Knoten aus herzustellen.

Öffnen Sie ein neues Terminalfenster, und verwenden Sie `kubectl get pods`, um den Namen des Pods zu erhalten, der von gestartet `kubectl debug` wurde.

```output
$ kubectl get pods

NAME                                                    READY   STATUS    RESTARTS   AGE
node-debugger-aks-nodepool1-12345678-vmss000000-bkmmx   1/1     Running   0          21s
```

Im obigen Beispiel ist *node-debugger-aks-nodepool1-12345678-vmss000000-bkmmx* der Name des Pods, der von `kubectl debug` gestartet wurde.

Mit `kubectl port-forward` können Sie eine Verbindung mit dem bereitgestellten Pod öffnen:

```
$ kubectl port-forward node-debugger-aks-nodepool1-12345678-vmss000000-bkmmx 2022:22
Forwarding from 127.0.0.1:2022 -> 22
Forwarding from [::1]:2022 -> 22
```

Im obigen Beispiel wird der Netzwerkdatenverkehr von Port 2022 auf Ihrem Entwicklungscomputer an Port 22 auf dem bereitgestellten Pod weitergeleitet. Wenn Sie mit `kubectl port-forward` eine Verbindung öffnen und den Netzwerkdatenverkehr weiterleiten, bleibt die Verbindung offen, bis Sie den Befehl `kubectl port-forward` beenden.

Öffnen Sie ein neues Terminal, und zeigen Sie mit `kubectl get nodes` die interne IP-Adresse des Windows Server-Knotens an:

```output
$ kubectl get nodes -o wide

NAME                                STATUS   ROLES   AGE     VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                         KERNEL-VERSION     CONTAINER-RUNTIME
aks-nodepool1-12345678-vmss000000   Ready    agent   13m     v1.19.9   10.240.0.4    <none>        Ubuntu 18.04.5 LTS               5.4.0-1046-azure   containerd://1.4.4+azure
aks-nodepool1-12345678-vmss000001   Ready    agent   13m     v1.19.9   10.240.0.35   <none>        Ubuntu 18.04.5 LTS               5.4.0-1046-azure   containerd://1.4.4+azure
aksnpwin000000                      Ready    agent   87s     v1.19.9   10.240.0.67   <none>        Windows Server 2019 Datacenter   10.0.17763.1935    docker://19.3.1
```

Im obigen Beispiel ist *10.240.0.67* die interne IP-Adresse des Windows Server-Knotens.

Erstellen Sie mithilfe der internen IP-Adresse eine SSH-Verbindung mit dem Windows Server-Knoten. Auch hier lautet der Standardbenutzername für AKS-Knoten *azureuser*. Bestätigen Sie die Aufforderung zum Fortsetzen der Verbindungsherstellung. Sie erhalten dann die Bash-Eingabeaufforderung Ihres Windows Server-Knotens:

```output
$ ssh -o 'ProxyCommand ssh -p 2022 -W %h:%p azureuser@127.0.0.1' azureuser@10.240.0.67

The authenticity of host '10.240.0.67 (10.240.0.67)' can't be established.
ECDSA key fingerprint is SHA256:1234567890abcdefghijklmnopqrstuvwxyzABCDEFG.
Are you sure you want to continue connecting (yes/no)? yes

[...]

Microsoft Windows [Version 10.0.17763.1935]
(c) 2018 Microsoft Corporation. All rights reserved.

azureuser@aksnpwin000000 C:\Users\azureuser>
```

Im obigen Beispiel wird über Port 2022 auf Ihrem Entwicklungscomputer eine Verbindung mit Port 22 auf dem Windows Server-Knoten hergestellt.

> [!NOTE]
> Wenn Sie die Kennwortauthentifizierung bevorzugen, verwenden Sie `-o PreferredAuthentications=password`. Zum Beispiel:
>
> ```console
>  ssh -o 'ProxyCommand ssh -p 2022 -W %h:%p azureuser@127.0.0.1' -o PreferredAuthentications=password azureuser@10.240.0.67
> ```

## <a name="remove-ssh-access"></a>Entfernen des SSH-Zugriffs

Wenn Sie fertig sind, führen Sie `exit` für die SSH-Sitzung aus, beenden Sie jegliche Portweiterleitung, und führen Sie dann `exit` für die interaktive Containersitzung aus. Nach dem Schließen dieser interaktiven Containersitzung wird der Pod gelöscht, der für den SSH-Zugriff vom AKS-Cluster verwendet wurde.

## <a name="next-steps"></a>Nächste Schritte

Falls Sie weitere Daten für die Problembehandlung benötigen, können Sie [die Kubelet-Protokolle anzeigen][view-kubelet-logs] oder die [Kubernetes-Masterknotenprotokolle anzeigen][view-master-logs].


<!-- INTERNAL LINKS -->
[view-kubelet-logs]: kubelet-logs.md
[view-master-logs]: monitor-aks-reference.md#resource-logs
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-windows-rdp]: rdp.md
[ssh-nix]: ../virtual-machines/linux/mac-create-ssh-keys.md
[ssh-windows]: ../virtual-machines/linux/ssh-from-windows.md
[ssh-linux-kubectl-debug]: #create-the-ssh-connection-to-a-linux-node
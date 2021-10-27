---
title: FAQ zu Windows Server-Knotenpools
titleSuffix: Azure Kubernetes Service
description: Hier finden Sie häufig gestellte Fragen zum Ausführen von Windows Server-Knotenpools und Anwendungsworkloads in Azure Kubernetes Service (AKS).
ms.topic: article
ms.date: 10/12/2020
ms.openlocfilehash: 9839b9075c3747c6c803e95c5622416da85d34d4
ms.sourcegitcommit: 5361d9fe40d5c00f19409649e5e8fed660ba4800
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/18/2021
ms.locfileid: "130138473"
---
# <a name="frequently-asked-questions-for-windows-server-node-pools-in-aks"></a>Häufig gestellte Fragen zu Windows Server-Knotenpools in AKS

In Azure Kubernetes Service (AKS) können Sie einen Knotenpool erstellen, der Windows Server als Gastbetriebssystem auf den Knoten ausführt. Auf diesen Knoten können native Windows-Containeranwendungen ausgeführt werden, z. B. die in .NET Framework integrierten Anwendungen. Es gibt Unterschiede in der Containerunterstützung unter Linux und Windows. Einige gängige Kubernetes- und podbezogene Features sind unter Linux zurzeit für Windows Server-Knotenpools nicht verfügbar.

In diesem Artikel werden einige häufig gestellten Fragen und Betriebssystemkonzepte für Windows Server-Knoten in AKS erläutert.

## <a name="which-windows-operating-systems-are-supported"></a>Welche Windows-Betriebssysteme werden unterstützt?

AKS verwendet Windows Server 2019 als Host-Betriebssystemversion und unterstützt nur die Prozessisolation. Mit anderen Windows Server-Versionen erstellte Containerimages werden nicht unterstützt. Weitere Informationen finden Sie unter [Versionskompatibilität von Windows-Containern][windows-container-compat].

## <a name="is-kubernetes-different-on-windows-and-linux"></a>Gibt es bei Kubernetes Unterschiede zwischen Windows und Linux?

Bei der Unterstützung für Windows Server-Knotenpools gelten einige Einschränkungen, die Teil des Upstreamprojekts „Windows Server in Kubernetes“ sind. Diese Einschränkungen sind nicht spezifisch für AKS. Weitere Informationen zu dieser Upstreamunterstützung für Windows Server in Kubernetes finden Sie im Abschnitt [Supported functionality and limitations][upstream-limitations] (Unterstützte Funktionen und Einschränkungen) des Dokuments [Windows containers in Kubernetes][intro-windows] (Windows-Container in Kubernetes).

Kubernetes ist von jeher auf Linux ausgerichtet. Viele auf der Upstreamwebsite [Kubernetes.io][kubernetes] verwendete Beispiele sind zur Verwendung auf Linux-Knoten vorgesehen. Wenn Sie Bereitstellungen erstellen, die Windows Server-Container verwenden, müssen Sie folgende Aspekte auf Betriebssystemebene beachten:

- **Identität**: Linux identifiziert einen Benutzer anhand einer ganzzahligen Benutzer-ID (UID). Ein Benutzer hat auch einen alphanumerischen Benutzernamen für die Anmeldung, der von Linux in die UID des Benutzers übersetzt wird. Auf ähnliche Weise identifiziert Linux eine Benutzergruppe anhand einer ganzzahligen Gruppen-ID (GID) und übersetzt einen Gruppennamen in die entsprechende GID.
    Windows Server verwendet eine umfangreichere binäre Sicherheits-ID (SID), die in der Windows Security Access Manager-Datenbank (SAM) gespeichert wird. Diese Datenbank wird weder zwischen dem Host und den Containern noch zwischen den einzelnen Containern freigegeben.
- **Dateiberechtigungen**: Windows-Server verwendet eine Zugriffssteuerungsliste basierend auf SIDs anstelle einer Bitmaske aus Berechtigungen und UID plus GID.
- **Dateipfade**: Die Konvention unter Windows Server ist die Verwendung von „\“ anstelle von „/“. 
    Geben Sie in Podspezifikationen, in denen Volumes eingebunden werden, den Pfad für Windows Server-Container korrekt ein. Geben Sie beispielsweise anstelle des Bereitstellungspunkts */mnt/volume* in einem Linux-Container einen Laufwerkbuchstaben und Speicherort ein, z. B. */K/Volume* zum Einbinden als Laufwerk *K:* .

## <a name="what-kind-of-disks-are-supported-for-windows"></a>Welche Typen von Datenträgern werden für Windows unterstützt?

Azure-Datenträger und Azure Files sind die unterstützten Volumetypen. Diese werden als NTFS-Volumes im Windows Server-Container aufgerufen.

## <a name="can-i-run-windows-only-clusters-in-aks"></a>Kann ich nur Windows-basierte Cluster in AKS ausführen?

Die Masterknoten (Steuerungsebene) in einem AKS-Cluster werden vom AKS-Dienst gehostet. Sie werden nicht für das Betriebssystem der Knoten verfügbar gemacht, die die Masterkomponenten hosten. Alle AKS-Cluster werden mit einem standardmäßig verfügbaren ersten Knotenpool erstellt, der auf Linux basiert. Dieser Knotenpool enthält Systemdienste, die für die Funktionsweise des Clusters erforderlich sind. Es wird empfohlen, mindestens zwei Knoten im ersten Knotenpool auszuführen, um die Zuverlässigkeit des Clusters und die Fähigkeit zum Ausführen von Clustervorgängen sicherzustellen. Der erste Linux-basierte Knotenpool kann nur gelöscht werden, wenn der AKS-Cluster selbst gelöscht wird.

## <a name="how-do-i-patch-my-windows-nodes"></a>Wie kann ich meine Windows-Knoten patchen?

Sie können entweder den [Knotenpool][nodepool-upgrade] oder das [Knotenimage][upgrade-node-image] aktualisieren, um die neuesten Patches für Windows-Knoten zu erhalten. Windows-Updates sind auf Knoten in AKS nicht aktiviert. AKS gibt neue Knotenpoolimages frei, sobald Patches verfügbar sind. Die Kunden sind dafür verantwortlich, Knotenpools zu aktualisieren, um in Bezug auf Patches und Hotfixes auf dem neuesten Stand zu bleiben. Dies gilt auch für die verwendete Kubernetes-Version. In den [AKS-Versionshinweisen][aks-release-notes] wird angegeben, wann neue Versionen verfügbar sind. Weitere Informationen zum Upgrade eines gesamten Windows Server-Knotenpools finden Sie unter [Durchführen eines Upgrades für einen Knotenpool][nodepool-upgrade]. Wenn Sie nur das Knotenimage aktualisieren möchten, finden Sie weitere Informationen unter [Vorschau – Upgrades für AKS-Knotenimages (Azure Kubernetes Service)][upgrade-node-image].

> [!NOTE]
> Das aktualisierte Windows Server-Image wird nur verwendet, wenn vor dem Upgrade des Knotenpools ein Clusterupgrade (ein Upgrade der Steuerungsebene) durchgeführt wird.
>

## <a name="what-network-plug-ins-are-supported"></a>Welche Netzwerk-Plug-Ins werden unterstützt?

AKS-Cluster mit Windows-Knotenpools müssen das (erweiterte) Netzwerkmodell des Azure Container Networking Interface (Azure CNI) verwenden. Kubenet Basic-Netzwerke werden nicht unterstützt. Weitere Informationen zu den Unterschieden der Netzwerkmodelle finden Sie unter [Netzwerkkonzepte für Anwendungen in AKS][azure-network-models]. Das Azure CNI-Netzwerkmodell erfordert zusätzliche Planung und Überlegungen in Bezug auf die Verwaltung von IP-Adressen. Weitere Informationen zum Planen und Implementieren von Azure CNI finden Sie unter [Konfigurieren von Azure CNI-Netzwerken in AKS][configure-azure-cni].

Auf Windows-Knoten in AKS-Clustern ist zudem standardmäßig [Direct Server Return (DSR)][dsr] aktiviert, wenn Calico aktiviert ist.

## <a name="is-preserving-the-client-source-ip-supported"></a>Wird das Beibehalten der Client-Quell-IP unterstützt?

Derzeit wird [die Beibehaltung der Client-Quell-IP][client-source-ip] bei Windows-Knoten nicht unterstützt.

## <a name="can-i-change-the-maximum-number-of-pods-per-node"></a>Kann ich die maximale Anzahl von Pods pro Knoten ändern?

Ja. Die Auswirkungen einer solchen Änderung sowie die verfügbaren Optionen finden Sie unter [Maximale Pods pro Knoten][maximum-number-of-pods].

## <a name="why-am-i-seeing-an-error-when-i-try-to-create-a-new-windows-agent-pool"></a>Warum wird mir ein Fehler angezeigt, wenn ich versuche, einen neuen Windows-Agentpool zu erstellen?

Wenn Sie Ihren Cluster vor Februar 2020 erstellt und noch kein Clusterupgrade durchgeführt haben, verwendet der Cluster immer noch ein altes Windows-Image. Möglicherweise wurde Ihnen dann bereits eine Fehlermeldung angezeigt, die in etwa wie folgt lautet:

"The following list of images referenced from the deployment template is not found: Herausgeber: MicrosoftWindowsServer, Offer: WindowsServer, Sku: 2019-datacenter-core-smalldisk-2004, Version: latest. Weitere Informationen zu verfügbaren Images finden Sie unter [Suchen und Verwenden von VM-Images im Azure Marketplace mit Azure PowerShell](../virtual-machines/windows/cli-ps-findimage.md).“

So beheben Sie diesen Fehler

1. Aktualisieren Sie die [Clustersteuerungsebene][upgrade-cluster-cp], um das Imageangebot und den Herausgeber zu aktualisieren.
1. Erstellen Sie neue Windows-Agentpools.
1. Verschieben Sie Windows-Pods aus vorhandenen Windows-Agentpools in neue Windows-Agentpools.
1. Löschen Sie alte Windows-Agentpools.

## <a name="how-do-i-rotate-the-service-principal-for-my-windows-node-pool"></a>Wie rotiere ich den Dienstprinzipal für meinen Windows-Knotenpool?

Windows-Knotenpools unterstützen die Rotation von Dienstprinzipalen nicht. Erstellen Sie einen neuen Windows-Knotenpool, und migrieren Sie Ihre Pods vom älteren Pool zum neuen, um den Dienstprinzipal zu aktualisieren. Nachdem die Pods zum neuen Pool migriert wurden, löschen Sie den älteren Knotenpool.

Verwenden Sie statt Dienstprinzipalen verwaltete Identitäten, bei denen es sich im Grunde um Wrapper um Dienstprinzipale handelt. Weitere Informationen finden Sie unter [Verwenden verwalteter Identitäten in Azure Kubernetes Service][managed-identity].

## <a name="how-do-i-change-the-administrator-password-for-windows-server-nodes-on-my-cluster"></a>Wie ändere ich das Administratorkennwort für Windows Server-Knoten in meinem Cluster?

Wenn Sie Ihren AKS-Cluster erstellen, geben Sie die Parameter `--windows-admin-password` und `--windows-admin-username` an, um die Administratoranmeldeinformationen für alle Windows Server-Knoten im Cluster festzulegen. Wenn Sie beim Erstellen eines Clusters über das Azure-Portal oder beim Festlegen von `--vm-set-type VirtualMachineScaleSets` und `--network-plugin azure` über die Azure CLI keine Administratoranmeldeinformationen angegeben haben, wird der Benutzername standardmäßig auf *azureuser* und ein zufälliges Kennwort festgelegt.

Verwenden Sie den `az aks update`-Befehl, um das Administratorkennwort zu ändern:

```azurecli
az aks update \
    --resource-group $RESOURCE_GROUP \
    --name $CLUSTER_NAME \
    --windows-admin-password $NEW_PW
```

> [!IMPORTANT]
> Mit dem Vorgang `az aks update` werden Upgrades nur für Windows Server-Knotenpools ausgeführt. Linux-Knotenpools sind nicht betroffen.
> 
> Wenn Sie `--windows-admin-password` ändern, muss das neue Kennwort mindestens 14 Zeichen lang sein und die [Windows Server-Kennwortanforderungen][windows-server-password] erfüllen.

## <a name="how-many-node-pools-can-i-create"></a>Wie viele Knotenpools kann ich erstellen?

Der AKS-Cluster kann maximal 100 Knotenpools umfassen. In diesen Knotenpools können insgesamt höchstens 1.000 Knoten enthalten sein. Weitere Informationen finden Sie unter [Einschränkungen][nodepool-limitations].

## <a name="what-can-i-name-my-windows-node-pools"></a>Wie kann ich meine Windows-Knotenpools benennen?

Verwenden Sie Namen, die maximal sechs Zeichen lang sind. Dies ist die aktuelle Beschränkung von AKS.

## <a name="are-all-features-supported-with-windows-nodes"></a>Werden alle Features für Windows-Knoten unterstützt?

Kubenet wird derzeit nicht für Windows-Knoten unterstützt.

## <a name="can-i-run-ingress-controllers-on-windows-nodes"></a>Kann ich Eingangscontroller auf Windows-Knoten ausführen?

Ja, ein Eingangsdatencontroller, der Windows Server-Container unterstützt, kann auf Windows-Knoten in AKS ausgeführt werden.

## <a name="can-my-windows-server-containers-use-gmsa"></a>Können meine Windows Server-Container gMSA verwenden?

Die Unterstützung von gruppenverwalteten Dienstkonten (Group-Managed Service Account, gMSA) ist in AKS derzeit nicht verfügbar.

## <a name="can-i-use-azure-monitor-for-containers-with-windows-nodes-and-containers"></a>Kann ich Azure Monitor für Container mit Windows-Knoten und -Containern verwenden?

Ja, das ist möglich. Allerdings befindet sich die Azure Monitor-Funktion zum Erfassen von Protokollen (stdout, stderr) und Metriken aus Windows-Containern in der öffentlichen Vorschau. Sie können ebenso an den Livestream von stdout-Protokollen aus einem Windows-Container anfügen.

## <a name="are-there-any-limitations-on-the-number-of-services-on-a-cluster-with-windows-nodes"></a>Gibt es Einschränkungen in Bezug auf die Anzahl von Diensten in einem Cluster mit Windows-Knoten?

Ein Cluster mit Windows-Knoten kann ungefähr 500 Dienste enthalten, bevor es zu einer Portauslastung kommt.

## <a name="can-i-use-azure-hybrid-benefit-with-windows-nodes"></a>Kann ich den Azure-Hybridvorteil mit Windows-Knoten verwenden?

Ja. Der Azure-Hybridvorteil für Windows Server senkt die Betriebskosten, da Sie Ihre lokale Windows Server-Lizenz in AKS-Windows-Knoten nutzen können.

Der Azure-Hybridvorteil kann für Ihren gesamten AKS-Cluster oder für einzelne Knoten verwendet werden. Für einzelne Knoten müssen Sie zur [Knotenressourcengruppe][resource-groups] wechseln und den Azure-Hybridvorteil direkt auf die Knoten anwenden. Weitere Informationen zum Anwenden des Azure-Hybridvorteils auf einzelne Knoten finden Sie unter [Azure-Hybridvorteil für Windows Server][hybrid-vms]. 

Verwenden Sie das Argument `--enable-ahub`, um den Azure-Hybridvorteil für einen neuen AKS-Cluster zu verwenden.

```azurecli
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-sku Standard \
    --windows-admin-password 'Password1234$' \
    --windows-admin-username azure \
    --network-plugin azure
    --enable-ahub
```

Um den Azure-Hybridvorteil für einen vorhandenen AKS-Cluster zu verwenden, aktualisieren Sie den Cluster mit dem Argument `--enable-ahub`.

```azurecli
az aks update \
    --resource-group myResourceGroup
    --name myAKSCluster
    --enable-ahub
```

Verwenden Sie den folgenden Befehl, um zu überprüfen, ob der Azure-Hybridvorteil für den Cluster festgelegt ist:

```azurecli
az vmss show --name myAKSCluster --resource-group MC_CLUSTERNAME
```

Ist der Azure-Hybridvorteil für den Cluster aktiviert, sieht die Ausgabe von `az vmss show` in etwa wie folgt aus:

```console
"platformFaultDomainCount": 1,
  "provisioningState": "Succeeded",
  "proximityPlacementGroup": null,
  "resourceGroup": "MC_CLUSTERNAME"
```

## <a name="can-i-use-the-kubernetes-web-dashboard-with-windows-containers"></a>Kann ich das Kubernetes-Webdashboard für Windows-Container verwenden?

Ja, Sie können das [Kubernetes-Webdashboard][kubernetes-dashboard] verwenden, um auf Informationen zu Windows-Containern zugreifen. Zurzeit können Sie jedoch *kubectl exec* nicht direkt aus dem Kubernetes-Webdashboard in einem ausgeführten Windows-Container ausführen. Weitere Informationen zum Herstellen einer Verbindung mit einem ausgeführten Windows-Container finden Sie unter [Herstellen einer RDP-Verbindung mit Windows Server-Knoten in Azure Kubernetes Service-Clustern (AKS) zur Wartung oder Problembehandlung][windows-rdp].

## <a name="how-do-i-change-the-time-zone-of-a-running-container"></a>Wie kann ich die Zeitzone eines ausgeführten Containers ändern?

Stellen Sie eine Verbindung mit dem ausgeführten Container mithilfe einer PowerShell-Sitzung her, um die Zeitzone eines ausgeführten Windows Server-Containers zu ändern. Beispiel:
    
```azurecli-interactive
kubectl exec -it CONTAINER-NAME -- powershell
```

Verwenden Sie im ausgeführten Container [Set-TimeZone](/powershell/module/microsoft.powershell.management/set-timezone), um die Zeitzone des ausgeführten Containers festzulegen. Beispiel:

```powershell
Set-TimeZone -Id "Russian Standard Time"
```

Mithilfe von [Get-TimeZone](/powershell/module/microsoft.powershell.management/get-timezone) können Sie die aktuelle Zeitzone des ausgeführten Containers oder eine verfügbare Liste von Zeitzonen anzeigen.

## <a name="can-i-maintain-session-affinity-from-client-connections-to-pods-with-windows-containers"></a>Kann ich die Sitzungsaffinität zwischen Clientverbindungen und Pods mit Windows Containern beibehalten?

Das Beibehalten der Sitzungsaffinität zwischen Clientverbindungen und Pods mit Windows-Containern wird zwar in der Windows Server 2022-Betriebssystemversion unterstützt, aber Sie erzielen diese Affinität per Client-IP-Adresse derzeit nur, indem Sie den gewünschten Pod auf die Ausführung einer einzelnen Instanz pro Knoten beschränken und Ihren Kubernetes-Dienst so konfigurieren, dass Datenverkehr an den Pod auf dem lokalen Knoten gesendet wird. 

Verwenden Sie die folgende Konfiguration: 

1. Verwenden Sie einen AKS-Cluster mit mindestens Version 1.20.
1. Beschränken Sie Ihren Pod so, dass nur eine Instanz pro Windows Knoten zugelassen wird. Dies können Sie durch Verwenden von Antiaffinität in Ihrer Bereitstellungskonfiguration erreichen.
1. Legen Sie in Ihrer Kubernetes-Dienstkonfiguration **externalTrafficPolicy=Local** fest. Damit wird sichergestellt, dass der Kubernetes-Dienst Datenverkehr nur an Pods auf dem lokalen Knoten sendet.
1. Legen Sie in Ihrer Kubernetes-Dienstkonfiguration **sessionAffinity: ClientIP** fest. Damit wird sichergestellt, dass der Azure Load Balancer mit Sitzungsaffinität konfiguriert wird.

## <a name="what-if-i-need-a-feature-thats-not-supported"></a>Wie sieht es aus, wenn ich ein Feature benötige, das nicht unterstützt wird?

Sollten Ihnen Features fehlen, bietet das Open-Source-Upstreamprojekt [aks-engine][aks-engine] eine einfache und vollständig anpassbare Möglichkeit zur Ausführung von Kubernetes in Azure, einschließlich Windows-Unterstützung. Weitere Informationen finden Sie in der [AKS-Roadmap][aks-roadmap].

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Einstieg in Windows Server-Container in AKS finden Sie unter [Erstellen eines Windows Server-Containers in einem AKS-Cluster mithilfe der Azure CLI][windows-node-cli].

<!-- LINKS - external -->
[kubernetes]: https://kubernetes.io
[aks-engine]: https://github.com/azure/aks-engine
[upstream-limitations]: https://kubernetes.io/docs/setup/production-environment/windows/intro-windows-in-kubernetes/#supported-functionality-and-limitations
[intro-windows]: https://kubernetes.io/docs/setup/production-environment/windows/intro-windows-in-kubernetes/
[aks-roadmap]: https://github.com/Azure/AKS/projects/1
[aks-release-notes]: https://github.com/Azure/AKS/releases

<!-- LINKS - internal -->
[azure-network-models]: concepts-network.md#azure-virtual-networks
[configure-azure-cni]: configure-azure-cni.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[windows-node-cli]: windows-container-cli.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[upgrade-cluster]: upgrade-cluster.md
[upgrade-cluster-cp]: use-multiple-node-pools.md#upgrade-a-cluster-control-plane-with-multiple-node-pools
[azure-outbound-traffic]: ../load-balancer/load-balancer-outbound-connections.md#defaultsnat
[nodepool-limitations]: use-multiple-node-pools.md#limitations
[windows-container-compat]: /virtualization/windowscontainers/deploy-containers/version-compatibility?tabs=windows-server-2019%2Cwindows-10-1909
[maximum-number-of-pods]: configure-azure-cni.md#maximum-pods-per-node
[azure-monitor]: ../azure-monitor/containers/container-insights-overview.md#what-does-azure-monitor-for-containers-provide
[client-source-ip]: concepts-network.md#ingress-controllers
[kubernetes-dashboard]: kubernetes-dashboard.md
[windows-rdp]: rdp.md
[upgrade-node-image]: node-image-upgrade.md
[managed-identity]: use-managed-identity.md
[hybrid-vms]: ../virtual-machines/windows/hybrid-use-benefit-licensing.md
[resource-groups]: faq.md#why-are-two-resource-groups-created-with-aks
[dsr]: ../load-balancer/load-balancer-multivip-overview.md#rule-type-2-backend-port-reuse-by-using-floating-ip
[windows-server-password]: /windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements#reference

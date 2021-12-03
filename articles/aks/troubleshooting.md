---
title: Problembehandlung bei allgemeinen Problemen mit Azure Kubernetes Service
description: Erfahren Sie, wie Sie allgemeine Probleme bei der Verwendung von Azure Kubernetes Service (AKS) beheben und lösen können
services: container-service
ms.topic: troubleshooting
ms.date: 09/24/2021
ms.openlocfilehash: c21c5a981f091c9c4b0e340f3c0ae1481cd486c6
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131455908"
---
# <a name="aks-troubleshooting"></a>AKS-Problembehandlung

Beim Erstellen oder Verwalten von Clustern mit Azure Kubernetes Service (AKS) können gelegentlich Probleme auftreten. In diesem Artikel werden einige allgemeine Probleme und Schritte zur Problembehandlung ausführlich erläutert.

## <a name="in-general-where-do-i-find-information-about-debugging-kubernetes-problems"></a>Wo finde ich grundsätzlich Informationen zum Debuggen von Problemen mit Kubernetes?

Lesen Sie den [offiziellen Leitfaden zur Problembehandlung von Kubernetes-Clustern](https://kubernetes.io/docs/tasks/debug-application-cluster/troubleshooting/).
Es gibt auch einen [Leitfaden zur Problembehandlung](https://github.com/feiskyer/kubernetes-handbook/blob/master/en/troubleshooting/index.md), der von einem Microsoft-Techniker veröffentlicht wurde und sich mit der Problembehandlung von Pods, Knoten, Clustern usw. befasst.

## <a name="im-getting-a-quota-exceeded-error-during-creation-or-upgrade-what-should-i-do"></a>Während der Erstellung oder Aktualisierung erhalte ich den Fehler `quota exceeded`. Wie sollte ich vorgehen? 

 [Fordern Sie weitere Kerne an](../azure-portal/supportability/regional-quota-requests.md).

## <a name="im-getting-an-insufficientsubnetsize-error-while-deploying-an-aks-cluster-with-advanced-networking-what-should-i-do"></a>Bei der Bereitstellung eines AKS-Clusters mit erweitertem Netzwerk erhalte ich den Fehler `insufficientSubnetSize`. Wie sollte ich vorgehen?

Dieser Fehler gibt an, dass ein für einen Cluster verwendetes Subnetz im CIDR-Bereich keine weiteren verfügbaren IP-Adressen für eine erfolgreiche Ressourcenzuweisung umfasst. Bei Kubenet-Clustern ist ein ausreichender IP-Adressraum für jeden Knoten im Cluster erforderlich. Bei Azure CNI-Clustern ist ein ausreichender IP-Adressraum für jeden Knoten und Pod im Cluster erforderlich.
Weitere Informationen zum [Design von Azure CNI für die Zuweisung von IP-Adressen zu Pods](configure-azure-cni.md#plan-ip-addressing-for-your-cluster) finden Sie hier.

Diese Fehler werden auch in der [AKS-Diagnose](concepts-diagnostics.md) ausgegeben, die Probleme wie z. B. eine unzureichende Subnetzgröße proaktiv ermittelt.

In den folgenden drei (3) Fällen tritt ein Fehler aufgrund unzureichender Subnetzgröße auf:

1. AKS-Skalierung oder AKS-Knotenpoolskalierung
   1. Bei Verwendung von Kubenet tritt dieser Fehler auf, wenn `number of free IPs in the subnet` **kleiner ist als** `number of new nodes requested`.
   1. Bei Verwendung von Azure CNI tritt dieser Fehler auf, wenn `number of free IPs in the subnet` **kleiner ist als** `number of nodes requested times (*) the node pool's --max-pod value`.

1. AKS-Upgrade oder AKS-Knotenpoolupgrade
   1. Bei Verwendung von Kubenet tritt dieser Fehler auf, wenn `number of free IPs in the subnet` **kleiner ist als** `number of buffer nodes needed to upgrade`.
   1. Bei Verwendung von Azure CNI tritt dieser Fehler auf, wenn `number of free IPs in the subnet` **kleiner ist als** `number of buffer nodes needed to upgrade times (*) the node pool's --max-pod value`.
   
   Standardmäßig ist für AKS-Cluster der maximale Anstiegswert (Upgradepuffer) „1“ festgelegt. Dieses Upgradeverhalten kann jedoch durch Festlegen des maximalen Anstiegswerts eines Knotenpools angepasst werden, wodurch die Anzahl der zum Durchführen eines Upgrades erforderlichen verfügbaren IP-Adressen erhöht wird.

1. AKS-Erstellung oder Hinzufügen eines AKS-Knotenpools
   1. Bei Verwendung von Kubenet tritt dieser Fehler auf, wenn `number of free IPs in the subnet` **kleiner ist als** `number of nodes requested for the node pool`.
   1. Bei Verwendung von Azure CNI tritt dieser Fehler auf, wenn `number of free IPs in the subnet` **kleiner ist als** `number of nodes requested times (*) the node pool's --max-pod value`.

Die folgenden Abhilfemaßnahmen können durch Erstellen neuer Subnetze ergriffen werden. Die Berechtigung zum Erstellen eines neuen Subnetzes ist zur Abhilfe erforderlich, da der CIDR-Bereich eines vorhandenen Subnetzes nicht aktualisiert werden kann.

1. Erstellen Sie ein neues Subnetz mit einem größeren CIDR-Bereich, der für die vorgesehenen Vorgänge ausreichend ist:
   1. Erstellen Sie ein neues Subnetz mit einem neuen gewünschten nicht überlappenden Bereich.
   1. Erstellen Sie einen neuen Knotenpool im neuen Subnetz.
   1. Entfernen Sie Pods aus dem alten Knotenpool, der sich in dem zu ersetzenden Subnetz befindet.
   1. Löschen Sie das alte Subnetz und den alten Knotenpool.

## <a name="my-pod-is-stuck-in-crashloopbackoff-mode-what-should-i-do"></a>Mein Pod ist im Modus CrashLoopBackOff hängen geblieben. Wie sollte ich vorgehen?

Es kann verschiedene Gründe dafür geben, dass der Pod in diesem Modus hängen bleibt. Untersuchen Sie Folgendes:

* Den Pod selbst mithilfe von `kubectl describe pod <pod-name>`
* Die Protokolle mit `kubectl logs <pod-name>`

Weitere Informationen zur Behandlung von Podproblemen finden Sie unter [Debuggen von Pods](https://kubernetes.io/docs/tasks/debug-application-cluster/debug-application/#debugging-pods) in der Kubernetes-Dokumentation.

## <a name="im-receiving-tcp-timeouts-when-using-kubectl-or-other-third-party-tools-connecting-to-the-api-server"></a>`TCP timeouts` wird ausgegeben, wenn ich über `kubectl` oder andere Drittanbietertools eine Verbindung mit dem API-Server herstelle.
AKS verfügt über Steuerungsebenen mit Hochverfügbarkeit, die vertikal nach der Anzahl der Kerne skaliert werden, um die Servicelevelziele (Service Level Objectives, SLOs) und Vereinbarungen zum Servicelevel (Service Level Agreements, SLAs) sicherstellen zu können. Wenn beim Herstellen der Verbindung ein Timeout auftritt, überprüfen Sie Folgendes:

- **Tritt das Timeout durchgehend bei allen oder nur bei einigen API-Befehlen auf?** Wenn es nur einige Befehle betrifft, wird der Pod `tunnelfront` oder der Pod `aks-link`, die für die Kommunikation zwischen Knoten und Steuerungsebene verwendet werden, möglicherweise nicht ausgeführt. Stellen Sie sicher, dass die Knoten, die diese Pods hosten, nicht überlastet oder starker Belastung ausgesetzt sind. Sie können diese Pods in separate [`system`-Knotenpools](use-system-pools.md) verschieben.
- **Haben Sie alle erforderlichen Ports, vollqualifizierten Domänennamen und IP-Adressen geöffnet, die in der Dokumentation zum [Einschränken des ausgehenden Datenverkehrs in AKS](limit-egress-traffic.md) angegeben sind?** Andernfalls können bei mehreren Befehlsaufrufen Fehler auftreten.
- **Liegt Ihre aktuelle IP-Adresse in den [vom API-Server autorisierten Adressbereichen](api-server-authorized-ip-ranges.md)?** Wenn Sie diese Funktion verwenden und Ihre IP-Adresse nicht in den Bereichen enthalten ist, werden die Aufrufe blockiert. 
- **Gibt es einen Client oder eine Anwendung, die eigene Aufrufe an den API-Server weiterleiten?** Stellen Sie sicher, dass Sie Überwachungen anstelle von häufigen Get-Aufrufen verwenden und dass in Drittanbieteranwendungen keine Aufrufe weitergegeben werden. Ein Fehler im Istio-Mixer bewirkt beispielsweise, dass bei jedem internen Lesen eines Geheimnisses eine neue Überwachungsverbindung mit dem API-Server erstellt wird. Da dieses Verhalten in regelmäßigen Abständen auftritt, häufen sich die Überwachungsverbindungen schnell an, und schließlich wird der API-Server unabhängig vom Skalierungsmuster überlastet. https://github.com/istio/istio/issues/19481
- **Umfassen Ihre Helm-Bereitstellungen viele Releases?** In diesem Szenario nutzt Tiller eventuell zu viel Speicher in den Knoten sowie eine große Menge an `configmaps` und verursacht dadurch unnötige Spitzen auf dem API-Server. Es empfiehlt sich, `--history-max` in `helm init` zu konfigurieren und das neue Toolset Helm 3 zu verwenden. Weitere Informationen zu Problemen finden Sie hier: 
    - https://github.com/helm/helm/issues/4821
    - https://github.com/helm/helm/issues/3500
    - https://github.com/helm/helm/issues/4543
- **[Wird interner Datenverkehr zwischen Knoten blockiert?](#im-receiving-tcp-timeouts-such-as-dial-tcp-node_ip10250-io-timeout)**

## <a name="im-receiving-tcp-timeouts-such-as-dial-tcp-node_ip10250-io-timeout"></a>Ich erhalte `TCP timeouts`, etwa `dial tcp <Node_IP>:10250: i/o timeout`.

Diese Timeouts können damit zusammenhängen, dass interner Datenverkehr zwischen Knoten blockiert wird. Vergewissern Sie sich, dass dieser Datenverkehr nicht blockiert wird, etwa von [Netzwerksicherheitsgruppen](concepts-security.md#azure-network-security-groups) im Subnetz für die Clusterknoten.

## <a name="im-trying-to-enable-kubernetes-role-based-access-control-kubernetes-rbac-on-an-existing-cluster-how-can-i-do-that"></a>Ich versuche, rollenbasierte Zugriffssteuerung in Kubernetes (Kubernetes RBAC) in einem vorhandenen Cluster zu aktivieren. Wie gehe ich dazu vor?

Eine Aktivierung der rollenbasierten Zugriffssteuerung in Kubernetes (Kubernetes RBAC) in vorhandenen Clustern wird derzeit nicht unterstützt. Sie muss beim Erstellen neuer Cluster eingestellt werden. Kubernetes RBAC ist standardmäßig aktiviert, wenn Sie die CLI, das Portal oder eine höhere API-Version als `2020-03-01` verwenden.

## <a name="i-cant-get-logs-by-using-kubectl-logs-or-i-cant-connect-to-the-api-server-im-getting-error-from-server-error-dialing-backend-dial-tcp-what-should-i-do"></a>Ich kann Protokolle nicht mithilfe von kubectl-Protokollen abrufen, oder ich kann keine Verbindung mit dem API-Server herstellen. Ich erhalte „Fehler vom Server: Fehler beim Anwählen des Back-Ends: Wählen Sie tcp…“. Wie sollte ich vorgehen?

Stellen Sie sicher, dass die Ports 22, 9000 und 1194 für die Verbindung mit dem API-Server offen sind. Überprüfen Sie mit dem `kubectl get pods --namespace kube-system`-Befehl, ob der `tunnelfront`- oder `aks-link`-Pod im *kube-system*-Namespace ausgeführt wird. Falls nicht, erzwingen Sie das Löschen des Pods. Er wird anschließend neu gestartet.

## <a name="im-getting-tls-client-offered-only-unsupported-versions-from-my-client-when-connecting-to-aks-api-what-should-i-do"></a>Beim Herstellen einer Verbindung mit der AKS-API erhalte ich von meinem Client die Meldung `"tls: client offered only unsupported versions"`. Wie sollte ich vorgehen?

Die unterstützte TLS-Mindestversion in AKS ist TLS 1.2.

## <a name="my-application-is-failing-with-argument-list-too-long"></a>Fehler `argument list too long` ist bei meiner Anwendung aufgetreten

Unter Umständen wird in etwa folgende Fehlermeldung angezeigt:

```
standard_init_linux.go:228: exec user process caused: argument list too long
```

Es gibt zwei mögliche Ursachen:
- Die für die ausführbare Datei bereitgestellte Argumentliste ist zu lang.
- Der Satz von Umgebungsvariablen, die für die ausführbare Datei bereitgestellt werden, ist zu groß.

Wenn Sie viele Dienste in einem Namespace bereitgestellt haben, kann dies dazu führen, dass die Umgebungsvariablenliste zu groß wird, und die obige Fehlermeldung wird ausgegeben, wenn Kubelet versucht, die ausführbare Datei auszuführen. Der Fehler wird dadurch verursacht, dass Kubelet Umgebungsvariablen einfügt, die den Host und Port für jeden aktiven Dienst aufzeichnen, sodass Dienste diese Informationen verwenden können, um einander zu suchen (weitere Informationen hierzu finden Sie [in der Kubernetes-Dokumentation](https://kubernetes.io/docs/concepts/services-networking/connect-applications-service/#accessing-the-service)). 

Als Problemumgehung können Sie dieses Kubelet-Verhalten deaktivieren, indem Sie `enableServiceLinks: false` in Ihrer [Podspezifikation](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.21/#podspec-v1-core) festlegen. Wenn Ihr Dienst **jedoch** auf diese Umgebungsvariablen angewiesen ist, um andere Dienste zu finden, führt dies zu einem Fehler. Eine Problembehebung besteht darin, DNS für die Dienstauflösung anstelle von Umgebungsvariablen (mit [CoreDNS](https://kubernetes.io/docs/tasks/administer-cluster/coredns/)) zu verwenden. Eine weitere Möglichkeit besteht darin, die Anzahl der aktiven Dienste zu reduzieren.

## <a name="im-trying-to-upgrade-or-scale-and-am-getting-a-changing-property-imagereference-is-not-allowed-error-how-do-i-fix-this-problem"></a>Ich möchte ein Upgrade oder eine Skalierung ausführen und erhalte eine Fehlermeldung `"Changing property 'imageReference' is not allowed"`. Wie kann ich dieses Problem beheben?

Möglicherweise erhalten Sie diese Fehlermeldung, da Sie die Tags in den Agent-Knoten innerhalb des AKS-Clusters geändert haben. Das Ändern oder Löschen von Tags und anderen Eigenschaften von Ressourcen in der Ressourcengruppe MC_* kann zu unerwarteten Ergebnissen führen. Durch das Ändern der Ressourcen unter der Gruppe „MC_*“ im AKS-Cluster wird das Servicelevelziel (SLO) unterbrochen.

## <a name="im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed"></a>Ich erhalte Fehler, dass sich mein Cluster in einem fehlerhaften Zustand befindet und ein Upgrade oder eine Skalierung nicht funktioniert, bis der Fehler behoben wurde.

*Diese Unterstützung bei der Problembehandlung wurde von https://aka.ms/aks-cluster-failed weitergeleitet.*

Dieser Fehler tritt auf, wenn Cluster aus mehreren Gründen in einen fehlerhaften Zustand übergehen. Führen Sie die folgenden Schritte aus, um den Fehlerzustand Ihres Clusters zu beheben, bevor Sie den zuvor fehlgeschlagenen Vorgang erneut versuchen:

1. Bis der Cluster den `failed`-Zustand verlassen hat, schlagen `upgrade`- und `scale`-Vorgänge fehl. Häufige Probleme und Lösungen sind unter anderem:
    * Skalieren mit einem **nicht ausreichenden Computekontingent (CRP)** . Um das Problem zu beheben, skalieren Sie zuerst Ihren Cluster wieder auf einen stabilen Zielzustand innerhalb des Kontingents. Befolgen Sie dann diese [Schritte, um eine Erhöhung des Computekontingents anzufordern](../azure-portal/supportability/regional-quota-requests.md), bevor Sie versuchen, den Wert erneut über die anfänglichen Kontingentgrenzen hinaus hochzuskalieren.
    * Skalieren eines Clusters mit erweiterter Netzwerkunterstützung und **nicht ausreichenden Subnetzressourcen (Netzwerk)** . Um das Problem zu beheben, skalieren Sie zuerst Ihren Cluster wieder auf einen stabilen Zielzustand innerhalb des Kontingents. Befolgen Sie dann diese [Schritte, um eine Erhöhung des Ressourcenkontingents anzufordern](../azure-resource-manager/templates/error-resource-quota.md#solution), bevor Sie versuchen, den Wert erneut über die anfänglichen Kontingentgrenzen hinaus hochzuskalieren.
2. Nachdem die zugrunde liegende Ursache des Upgradefehlers behoben wurde, wiederholen Sie den ursprünglichen Vorgang. Durch diesen Wiederholungsvorgang sollte Ihr Cluster den Status „Erfolgreich“ haben. 

## <a name="im-receiving-errors-when-trying-to-upgrade-or-scale-that-state-my-cluster-is-being-upgraded-or-has-failed-upgrade"></a>Wenn ich versuche, meinen Cluster zu aktualisieren oder zu skalieren, erhalte ich Fehlermeldungen, die besagen, dass mein Cluster aktualisiert wird oder bei dessen Upgrade ein Fehler aufgetreten ist.

*Diese Unterstützung bei der Problembehandlung wurde von https://aka.ms/aks-pending-upgrade weitergeleitet.*

 Es ist nicht möglich, einen Cluster oder Knotenpool gleichzeitig zu aktualisieren und zu skalieren. Stattdessen muss jeder Vorgangstyp für die Zielressource abgeschlossen sein, bevor eine neue Anforderung an dieselbe Ressource gerichtet werden kann. Vorgänge sind daher eingeschränkt, wenn aktive Upgrade- oder Skalierungsvorgänge stattfinden oder gestartet wurden. 

Um das Problem zu diagnostizieren, führen Sie `az aks show -g myResourceGroup -n myAKSCluster -o table` aus, um den detaillierten Status Ihres Clusters abzurufen. Basierend auf dem Ergebnis:

* Wenn ein aktives Upgrade des Clusters durchgeführt wird, warten Sie, bis der Vorgang beendet ist. Wenn dies erfolgreich war, versuchen Sie den zuvor fehlgeschlagenen Vorgang erneut.
* Wenn das Upgrade des Clusters fehlgeschlagen ist, führen Sie die im voranstehenden Abschnitt beschriebenen Schritte aus.

## <a name="can-i-move-my-cluster-to-a-different-subscription-or-my-subscription-with-my-cluster-to-a-new-tenant"></a>Kann ich meinen Cluster in ein anderes Abonnement verschieben oder mein Abonnement mit meinem Cluster in einen anderen Mandanten?

Wenn Sie Ihren AKS-Cluster in ein anderes Abonnement oder das Abonnement des Clusters in einen neuen Mandanten verschoben haben, funktioniert der Cluster nicht, weil Berechtigungen für die Clusteridentität fehlen. Aufgrund dieser Einschränkung **unterstützt AKS das Verschieben von Clustern über Abonnements oder Mandanten nicht**.

## <a name="im-receiving-errors-trying-to-use-features-that-require-virtual-machine-scale-sets"></a>Ich erhalte Fehler, wenn ich versuche, Funktionen zu verwenden, die VM-Skalierungsgruppen erfordern

*Diese Unterstützung bei der Problembehandlung wurde von „aka.ms/aks-vmss-enablement“ weitergeleitet.*

Möglicherweise erhalten Sie Fehlermeldungen, die angeben, dass sich Ihr AKS-Cluster nicht in einer VM-Skalierungsgruppe befindet. Beispiel:

**Agent-Pool `<agentpoolname>` hat die Autoskalierung aktiviert, befindet sich aber nicht in einer VM-Skalierungsgruppe.**

Funktionen wie die automatische Clusterskalierung oder mehrere Knotenpools erfordern VM-Skalierungsgruppen als `vm-set-type`.

Führen Sie die Schritte unter *Voraussetzungen* im entsprechenden Dokument aus, um einen AKS-Cluster korrekt zu erstellen:

* [Verwenden der automatischen Clusterskalierung](cluster-autoscaler.md)
* [Erstellen und Verwenden mehrerer Knotenpools](use-multiple-node-pools.md)
 
## <a name="what-naming-restrictions-are-enforced-for-aks-resources-and-parameters"></a>Welche Benennungseinschränkungen gelten für AKS-Ressourcen und -Parameter?

*Diese Unterstützung bei der Problembehandlung wurde von „aka.ms/aks-naming-rules“ weitergeleitet.*

Benennungseinschränkungen werden sowohl von der Azure-Plattform als auch AKS implementiert. Verletzt ein Ressourcenname oder Parameter eine dieser Einschränkungen, wird ein Fehler zurückgegeben, der Sie auffordert, eine andere Eingabe zu machen. Es gelten die folgenden allgemeinen Benennungsrichtlinien:

* Clusternamen müssen zwischen 1 und 63 Zeichen lang sein. Die einzigen zulässigen Zeichen sind Buchstaben, Ziffern, Bindestriche und Unterstriche. Das erste und das letzte Zeichen müssen ein Buchstabe oder eine Zahl sein.
* Der Name der AKS-Knoten-/*MC_* -Ressourcengruppe ist eine Kombination aus Ressourcengruppenname und Ressourcenname. Die automatisch generierte Syntax von `MC_resourceGroupName_resourceName_AzureRegion` darf nicht mehr als 80 Zeichen umfassen. Kürzen Sie bei Bedarf Ihren Ressourcengruppennamen oder AKS-Clusternamen. Sie können auch [den Namen der Knotenressourcengruppe anpassen](cluster-configuration.md#custom-resource-group-name).
* Das *dnsPrefix* muss mit alphanumerischen Werten beginnen und enden und zwischen 1 und 54 Zeichen lang sein. Gültige Zeichen sind alphanumerische Werte und Bindestriche (-). Das *dnsPrefix* darf keine Sonderzeichen wie z.B. einen Punkt (.) enthalten.
* In den Namen von AKS-Knotenpools dürfen nur Kleinbuchstaben verwendet werden, und Linux-Knotenpools dürfen 1 bis 11 Zeichen und Windows-Knotenpools 1 bis 6 Zeichen lang sein. Der Name muss mit einem Buchstaben beginnen, und die einzigen zulässigen Zeichen sind Buchstaben und Ziffern.
* Der *admin-username*, der den Administratorbenutzernamen für Linux-Knoten festlegt, muss mit einem Buchstaben beginnen, darf nur Buchstaben, Ziffern, Bindestriche und Unterstriche enthalten und maximal 64 Zeichen lang sein.

## <a name="im-receiving-errors-when-trying-to-create-update-scale-delete-or-upgrade-cluster-that-operation-is-not-allowed-as-another-operation-is-in-progress"></a>Wenn ich versuche, einen Cluster zu erstellen, zu aktualisieren, zu skalieren, zu löschen oder zu aktualisieren, erhalte ich die Fehlermeldung, dass dieser Vorgang nicht erlaubt ist, da ein anderer Vorgang ausgeführt wird.

*Diese Unterstützung bei der Problembehandlung wurde von „aka.ms/aks-pending-operations“ weitergeleitet*

Clustervorgänge sind nur eingeschränkt möglich, wenn ein vorheriger Vorgang noch im Gange ist. Um den detaillierten Status Ihres Clusters abzurufen, verwenden Sie den Befehl `az aks show -g myResourceGroup -n myAKSCluster -o table`. Verwenden Sie bei Bedarf Ihre eigene Ressourcengruppe und Ihren Namen für den AKS-Cluster.

Basierend auf der Ausgabe des Status des Clusters:

* Wenn sich der Cluster in einem anderen Bereitstellungsstatus als *Erfolgreich* oder *Fehler* befindet, warten Sie, bis der Vorgang (*Upgrade/Aktualisieren/Erstellen/Skalieren/Löschen/Migrieren*) abgeschlossen ist. Sobald der vorherige Vorgang abgeschlossen ist, wiederholen Sie Ihren letzten Clustervorgang.

* Wenn im Cluster ein fehlerhaftes Upgrade erfolgt, befolgen Sie die Anweisungen unter [Ich erhalte Fehler, dass sich mein Cluster in einem fehlerhaften Zustand befindet und ein Upgrade oder eine Skalierung nicht funktioniert, bis der Fehler behoben wurde](#im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed).

## <a name="received-an-error-saying-my-service-principal-wasnt-found-or-is-invalid-when-i-try-to-create-a-new-cluster"></a>Beim Versuch, einen neuen Cluster zu erstellen, erhalte ich die Fehlermeldung, der Dienstprinzipal sei nicht gefunden worden oder ungültig.

Beim Erstellen eines AKS-Clusters ist ein Dienstprinzipal oder eine verwaltete Identität erforderlich, um Ressourcen in Ihrem Auftrag zu erstellen. AKS kann beim Erstellen eines Clusters automatisch einen neuen Dienstprinzipal erstellen oder einen vorhandenen Dienstprinzipal empfangen. Wenn Sie einen automatisch erstellten Dienstprinzipal verwenden, muss Azure Active Directory ihn an jede Region weitergeben, damit die Erstellung erfolgreich ist. Wenn diese Weitergabe zu lange dauert, tritt bei der Überprüfung zum Erstellen des Clusters ein Fehler auf, da kein verfügbarer Dienstprinzipal dafür gefunden werden kann. 

Verwenden Sie in diesem Fall folgende Problemumgehungen:
* Verwenden Sie einen vorhandenen Dienstprinzipal, der bereits über Regionen weitergegeben wurde und zum Zeitpunkt der Clustererstellung für die Übergabe an AKS vorhanden ist.
* Bei Verwendung von Automatisierungsskripts fügen Sie Zeitverzögerungen zwischen der Erstellung des Dienstprinzipals und der Erstellung des AKS-Clusters ein.
* Wenn Sie das Azure-Portal verwenden, kehren Sie während der Erstellung zu den Clustereinstellungen zurück, und wiederholen Sie die Überprüfung nach einigen Minuten.

## <a name="im-getting-aadsts7000215-invalid-client-secret-is-provided-when-using-aks-api-what-should-i-do"></a>Ich erhalte bei Verwendung der AKS-API die Meldung `"AADSTS7000215: Invalid client secret is provided."`.   Wie sollte ich vorgehen?

Dieses Problem wird durch den Ablauf von Anmeldeinformationen des Dienstprinzipals verursacht. [Aktualisieren Sie die Anmeldeinformationen für einen AKS-Cluster.](update-credentials.md)

## <a name="i-cant-access-my-cluster-api-from-my-automationdev-machinetooling-when-using-api-server-authorized-ip-ranges-how-do-i-fix-this-problem"></a>Ich kann von meinem Automatisierungs-/Entwicklungs-/Toolssystem aus nicht auf meine Cluster-API zugreifen, wenn ich vom API-Server autorisierte IP-Adressbereiche verwende. Wie kann ich dieses Problem beheben?

Zum Lösen dieses Problems stellen Sie sicher, dass `--api-server-authorized-ip-ranges` die IP-Adressen oder IP-Adressbereiche der verwendeten Automatisierungs-/Entwicklungs-/Toolsysteme einschließt. Lesen Sie im Abschnitt „Ermitteln meiner IP-Adresse“ unter [Sicherer Zugriff auf den API-Server mit autorisierten IP-Adressbereichen](api-server-authorized-ip-ranges.md) nach.

## <a name="im-unable-to-view-resources-in-kubernetes-resource-viewer-in-azure-portal-for-my-cluster-configured-with-api-server-authorized-ip-ranges-how-do-i-fix-this-problem"></a>Ich kann keine Ressourcen im Kubernetes-Ressourcen-Viewer in Azure-Portal für meinen Cluster anzeigen, der mit vom API-Server autorisierten IP-Adressbereichen konfiguriert ist. Wie kann ich dieses Problem beheben?

Der [Kubernetes-Ressourcen-Viewer](kubernetes-portal.md) erfordert `--api-server-authorized-ip-ranges`, um den Zugriff für den lokalen Clientcomputer oder IP-Adressbereich (aus dem das Portal aufgerufen wird) einzubeziehen. Lesen Sie im Abschnitt „Ermitteln meiner IP-Adresse“ unter [Sicherer Zugriff auf den API-Server mit autorisierten IP-Adressbereichen](api-server-authorized-ip-ranges.md) nach.

## <a name="im-receiving-errors-after-restricting-egress-traffic"></a>Ich erhalte Fehlermeldungen, nachdem mein ausgehender Datenverkehr eingeschränkt wurde.

Wenn Sie von einem AKS-Cluster ausgehenden Datenverkehr einschränken, gibt es für AKS [erforderliche und optionale empfohlene](limit-egress-traffic.md) Regeln für ausgehende Ports/das Netzwerk sowie für den FQDN/die Anwendung. Wenn Ihre Einstellungen mit einer dieser Regeln in Konflikt stehen, funktionieren bestimmte `kubectl`-Befehle möglicherweise nicht. Beim Erstellen eines AKS-Clusters werden eventuell auch Fehler angezeigt.

Stellen Sie sicher, dass Ihre Einstellungen nicht mit den erforderlichen oder optionalen empfohlenen Regeln für ausgehende Ports/das Netzwerk sowie für den FQDN/die Anwendung in Konflikt stehen.

## <a name="im-receiving-429---too-many-requests-errors"></a>Ich erhalte Fehlermeldungen „429 – zu viele Anforderungen“.

Wenn ein Kubernetes-Cluster in Azure (AKS oder nicht) häufig zentral hoch- oder herunterskaliert wird oder die automatische Clusterskalierung verwendet, können diese Vorgänge zu einer großen Anzahl von HTTP-Aufrufen führen, die wiederum das zugewiesene Abonnement Kontingent überschreiten, was zu einem Fehler führt. Die Fehler sehen aus wie

```
Service returned an error. Status=429 Code=\"OperationNotAllowed\" Message=\"The server rejected the request because too many requests have been received for this subscription.\" Details=[{\"code\":\"TooManyRequests\",\"message\":\"{\\\"operationGroup\\\":\\\"HighCostGetVMScaleSet30Min\\\",\\\"startTime\\\":\\\"2020-09-20T07:13:55.2177346+00:00\\\",\\\"endTime\\\":\\\"2020-09-20T07:28:55.2177346+00:00\\\",\\\"allowedRequestCount\\\":1800,\\\"measuredRequestCount\\\":2208}\",\"target\":\"HighCostGetVMScaleSet30Min\"}] InnerError={\"internalErrorCode\":\"TooManyRequestsReceived\"}"}
```

Diese Drosselungsfehler werden ausführlich [hier](../azure-resource-manager/management/request-limits-and-throttling.md) und [hier](/troubleshoot/azure/virtual-machines/troubleshooting-throttling-errors) beschrieben.

Die Empfehlung des AKS-Engineering-Teams lautet: Stellen Sie sicher, dass Sie mindestens Version 1.18.x ausführen, die zahlreiche Verbesserungen enthält. Weitere Informationen zu diesen Verbesserungen finden Sie [hier](https://github.com/Azure/AKS/issues/1413) und [hier](https://github.com/kubernetes-sigs/cloud-provider-azure/issues/247).

Angenommen, diese Drosselungsfehler werden auf Abonnementsebene gemessen, dann können sie auch noch weiterhin auftreten, wenn:
- Es sind Drittanbieteranwendungen vorhanden, die GET-Anforderungen ausführen (beispielsweise Überwachungsanwendungen und ähnliches). Es wird empfohlen, die Häufigkeit dieser Aufrufe zu verringern.
- Es sind viele AKS-Cluster bzw. -Knotenpools vorhanden, die VM-Skalierungsgruppen verwenden. Versuchen Sie, die Cluster auf verschiedene Abonnements aufzuteilen, insbesondere dann, wenn Sie mit hoher Aktivität rechnen (beispielsweise bei aktiver Clusterautoskalierung) oder viele Clients verwalten müssen (z. B. Rancher, Terraform usw.).

## <a name="my-clusters-provisioning-status-changed-from-ready-to-failed-with-or-without-me-performing-an-operation-what-should-i-do"></a>Der Bereitstellungsstatus meines Clusters wurde unabhängig davon, ob ich einen Vorgang ausgeführt habe, von „Bereit“ in „Fehlerhaft“ geändert.   Wie sollte ich vorgehen?

Wenn der Bereitstellungsstatus Ihres Clusters unabhängig davon, ob Sie einen Vorgang ausführen, von *Bereit* in *Fehlerhaft* geändert wird, die Anwendungen im Cluster jedoch weiterhin ausgeführt werden, wird dieses Problem möglicherweise automatisch vom Dienst aufgelöst, und Ihre Anwendungen sollten nicht beeinträchtigt werden.

Wenn der Bereitstellungsstatus Ihres Clusters weiterhin *Fehlerhaft* lautet oder wenn die Anwendungen in Ihrem Cluster nicht mehr funktionieren, [erstellen Sie eine Supportanfrage](https://azure.microsoft.com/support/options/#submit).

## <a name="my-watch-is-stale-or-azure-ad-pod-identity-nmi-is-returning-status-500"></a>Mein Überwachungselement ist veraltet, oder die NMI der Azure AD-Podidentität gibt Status 500 zurück

Wenn Sie Azure Firewall verwenden – wie in diesem [Beispiel](limit-egress-traffic.md#restrict-egress-traffic-using-azure-firewall) –, kann dieses Problem auftreten, da langlebige TCP-Verbindungen über die Firewall, für die Anwendungsregeln verwendet werden, zurzeit einen Fehler aufweisen, der dazu führt, dass Go-`keepalives` in der Firewall beendet werden (dieser Fehler soll im 1. Quartal des Kalenderjahrs 2021 behoben werden). Bis dieses Problem behoben ist, können Sie es entschärfen, indem Sie der IP-Adresse des AKS-API-Servers eine Netzwerkregel (anstelle einer Anwendungsregel) hinzufügen.

## <a name="when-resuming-my-cluster-after-a-stop-operation-why-is-my-node-count-not-in-the-autoscaler-min-and-max-range"></a>Warum liegt meine Knotenanzahl beim Fortsetzen meines Clusters nach einem Beendigungsvorgang nicht im definierten Bereich der Autoskalierung?

Wenn Sie automatische Clusterskalierung verwenden, liegt Ihre aktuelle Knotenanzahl beim Starten des Clusters möglicherweise nicht zwischen den von Ihnen festgelegten Mindest- und Höchstwerten für den Bereich. Dies ist das erwartete Verhalten. Der Cluster beginnt mit der Anzahl von Knoten, die er zum Ausführen seiner Workloads benötigt. Dies hat keine Auswirkungen auf Ihre Einstellungen für automatische Skalierung. Wenn Ihr Cluster Skalierungsvorgänge ausführt, wirken sich die Mindest- und Höchstwerte auf Ihre aktuelle Knotenanzahl aus. Der Cluster wird schließlich in den gewünschten Bereich gelangen und dort verbleiben, bis Sie ihn beenden.

## <a name="azure-storage-and-aks-troubleshooting"></a>Problembehandlung für Azure Storage und AKS

### <a name="failure-when-setting-uid-and-gid-in-mountoptions-for-azure-disk"></a>Fehler beim Festlegen von UID und `GID` in „mountOptions“ für Azure Disk

Azure Disk verwendet standardmäßig das Dateisystem „ext4,xfs“ sowie mountOptions wie „uid=x, gid=x“ können zum Zeitpunkt des Einbindens nicht festgelegt werden. Wenn Sie z. B. versucht haben, die mountOptions „uid=999,gid=999“ festzulegen, würde ein Fehler angezeigt wie:

```console
Warning  FailedMount             63s                  kubelet, aks-nodepool1-29460110-0  MountVolume.MountDevice failed for volume "pvc-d783d0e4-85a1-11e9-8a90-369885447933" : azureDisk - mountDevice:FormatAndMount failed with mount failed: exit status 32
Mounting command: systemd-run
Mounting arguments: --description=Kubernetes transient mount for /var/lib/kubelet/plugins/kubernetes.io/azure-disk/mounts/m436970985 --scope -- mount -t xfs -o dir_mode=0777,file_mode=0777,uid=1000,gid=1000,defaults /dev/disk/azure/scsi1/lun2 /var/lib/kubelet/plugins/kubernetes.io/azure-disk/mounts/m436970985
Output: Running scope as unit run-rb21966413ab449b3a242ae9b0fbc9398.scope.
mount: wrong fs type, bad option, bad superblock on /dev/sde,
       missing codepage or helper program, or other error
```

Nutzen Sie eine der folgenden Optionen, um das Problem zu minimieren:

* [Konfigurieren Sie den Sicherheitskontext für eine Pod](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/), indem Sie die UID in „runAsUser“ und die GID in „fsGroup“ festlegen. Mit der folgenden Einstellung wird z. B. die Pod-Ausführung auf „root“ festgelegt, sodass alle Dateien darauf zugreifen können:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: security-context-demo
spec:
  securityContext:
    runAsUser: 0
    fsGroup: 0
```

  >[!NOTE]
  > Da GID und UID standardmäßig als „root“ oder „0“ eingebunden werden. Wenn GID oder UID auf einen anderen Wert als „root“ festgelegt werden (z. B. 1000), verwendet Kubernetes `chown`, um alle Verzeichnisse und Dateien unterhalb dieses Datenträgers zu ändern. Dieser Vorgang kann zeitaufwändig sein und das Einbinden des Datenträgers stark verlangsamen.

* Verwenden Sie `chown` in „initContainers“, um `GID` und `UID` festzulegen. Beispiel:

```yaml
initContainers:
- name: volume-mount
  image: mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
  command: ["sh", "-c", "chown -R 100:100 /data"]
  volumeMounts:
  - name: <your data volume>
    mountPath: /data
```

### <a name="large-number-of-azure-disks-causes-slow-attachdetach"></a>Eine große Anzahl von Azure Disks führt zu langsamem Anfügen/Trennen.

Wenn die Anzahl von Azure Disk-Anfügungs-/Trennungsvorgängen für eine VM mit einem einzelnen Knoten größer als 10 ist – oder größer als 3, wenn ein einzelner VM-Skalierungsgruppenpool als Ziel festgelegt ist – sind sie möglicherweise langsamer als erwartet, da sie nacheinander ausgeführt werden. Dieses Problem ist eine bekannte Einschränkung des bauminternen Azure-Disk-Treibers. Der [Azure Disk CSI-Treiber](https://github.com/kubernetes-sigs/azuredisk-csi-driver) hat dieses Problem mit dem Anhängen/Entfernen von Festplatten im Batchbetrieb gelöst.

### <a name="azure-disk-detach-failure-leading-to-potential-node-vm-in-failed-state"></a>Fehler beim Trennen von Azure Disks führt zu potenziellem Fehlerzustand von Knoten-VM.

In einigen Randfällen kann es zu einem teilweisen Fehlschlagen des Trennens von Azure Disk kommen, wodurch die Knoten-VM in einem Fehlerzustand verbleibt.

Wenn sich Ihr Knoten in einem ausgefallenen Zustand befindet, können Sie den VM-Status mit einer der folgenden Methoden manuell aktualisieren:

* Für einen auf Verfügbarkeitsgruppen basierenden Cluster:
    ```azurecli
    az vm update -n <VM_NAME> -g <RESOURCE_GROUP_NAME>
    ```

* Für einen VMSS-basierenden Cluster:
    ```azurecli
    az vmss update-instances -g <RESOURCE_GROUP_NAME> --name <VMSS_NAME> --instance-id <ID>
    ```

## <a name="azure-files-and-aks-troubleshooting"></a>Problembehandlung für Azure Files und AKS

### <a name="what-are-the-default-mountoptions-when-using-azure-files"></a>Wie lauten die standardmäßigen „mountOptions“ bei Verwendung von Azure Files?

Empfohlene Einstellungen:

| Kubernetes-Version | fileMode- und dirMode-Wert|
|--|:--:|
| 1.12.2 und höher | 0777 |

Einfügeoptionen können für das Speicherklassenobjekt angegeben werden. Im folgenden Beispiel wird *0777* festgelegt:

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
  - mfsymlinks
  - nobrl
  - cache=none
parameters:
  skuName: Standard_LRS
```

Einige zusätzliche nützliche *mountOptions*-Einstellungen:

* `mfsymlinks` aktiviert Unterstützung bei der Einbindung symbolischer Links (CIFS) in Azure Files.
* `nobrl` verhindert das Senden von Anforderungen für Byte-Bereichssperrungen an den Server. Diese Einstellung ist für bestimmte Anwendungen erforderlich, die obligatorische Byte-Bereichssperren im CIFS-Stil verletzen. Die meisten CIFS-Server unterstützen noch keine Anforderung von Byte-Bereichssperrenempfehlungen. Wenn Sie *nobrl* nicht verwenden, verursachen Anwendungen, die obligatorische Byte-Bereichssperren im CIFS-Stil verletzen, möglicherweise ähnliche Fehlermeldungen wie diese:
    ```console
    Error: SQLITE_BUSY: database is locked
    ```

### <a name="error-could-not-change-permissions-when-using-azure-files"></a>Fehler „Berechtigungen konnten nicht geändert werden“ bei Verwendung von Azure Files.

Bei der Ausführung von PostgreSQL auf dem Azure Files-Plug-In wird möglicherweise ein ähnlicher Fehler wie der folgende angezeigt:

```console
initdb: could not change permissions of directory "/var/lib/postgresql/data": Operation not permitted
fixing permissions on existing directory /var/lib/postgresql/data
```

Dieser Fehler wird vom Azure Files-Plug-In verursacht, wenn es das CISF/SMB-Protokoll verursacht. Bei Verwendung des CIFS/SMB-Protokolls konnten die Datei- und Verzeichnisberechtigungen nach der Bereitstellung nicht geändert werden.

Um dieses Problem zu beheben, verwenden Sie `subPath` zusammen mit dem Azure Disk-Plug-In. 

> [!NOTE] 
> Beim Datenträgertyp „ext3/4“ gibt es ein Verzeichnis „lost+found“, nachdem der Datenträger formatiert wurde.

### <a name="azure-files-has-high-latency-compared-to-azure-disk-when-handling-many-small-files"></a>Bei der Verarbeitung vieler kleiner Dateien kommt es in Azure Files zu hohen Wartezeiten im Vergleich mit Azure Disk.

In manchen Fällen, z. B. bei der Verarbeitung vieler kleiner Dateien, kann es bei der Verwendung von Azure Files im Vergleich zu Azure Disk zu hohen Wartezeiten kommen.

### <a name="error-when-enabling-allow-access-allow-access-from-selected-network-setting-on-storage-account"></a>Fehler beim Aktivieren der Einstellung „Zugriff aus ausgewähltem Netzwerk zulassen“ für das Speicherkonto.

Wenn Sie *Zugriff aus ausgewähltem Netzwerk zulassen* für ein Speicherkonto aktivieren, das für die dynamische Bereitstellung in AKS verwendet wird, erhalten Sie eine Fehlermeldung, wenn AKS eine Dateifreigabe erstellt:

```console
persistentvolume-controller (combined from similar events): Failed to provision volume with StorageClass "azurefile": failed to create share kubernetes-dynamic-pvc-xxx in account xxx: failed to create file share, err: storage: service returned error: StatusCode=403, ErrorCode=AuthorizationFailure, ErrorMessage=This request is not authorized to perform this operation.
```

Dieser Fehler ist darauf zurückzuführen, dass sich der *persistentvolume-controller *von Kubernetes nicht im ausgewählten Netzwerk befindet, wenn* Zugriff aus ausgewähltem Netzwerk zulassen* festgelegt wird.

Sie können das Problem minimieren, indem Sie [statische Bereitstellung mit Azure Files ](azure-files-volume.md) verwenden.

### <a name="azure-files-mount-fails-because-of-storage-account-key-changed"></a>Bei der Azure Files-Einbindung tritt ein Fehler auf, weil sich der Speicherkontoschlüssel geändert hat.

Wenn Ihr Speicherkontoschlüssel geändert wurde, kommt es möglicherweise zu Azure Files-Einbindungsfehlern.

Sie können das Problem minimieren, indem Sie das Feld `azurestorageaccountkey` manuell mit Ihrem base64-codierten Speicherkontoschlüssel in einem Azure File-Geheimnis aktualisieren.

Um Ihren Speicherkontoschlüssel in Base64 zu codieren, können Sie `base64` verwenden. Beispiel:

```console
echo X+ALAAUgMhWHL7QmQ87E1kSfIqLKfgC03Guy7/xk9MyIg2w4Jzqeu60CVw2r/dm6v6E0DWHTnJUEJGVQAoPaBc== | base64
```

Verwenden Sie zum Aktualisieren Ihrer Azure-Geheimnisdatei `kubectl edit secret`. Beispiel:

```console
kubectl edit secret azure-storage-account-{storage-account-name}-secret
```

Nach ein paar Minuten versucht der Agent-Knoten die Azure Files-Einbindung erneut mit dem aktualisierten Speicherschlüssel.


### <a name="cluster-autoscaler-fails-to-scale-with-error-failed-to-fix-node-group-sizes"></a>Fehler beim Skalieren der automatischen Clusterskalierung aufgrund eines Fehlers beim Beheben von Knotengruppengrößen

Wenn die automatische Clusterskalierung nicht hoch- oder herunterskaliert wird, wird ein Fehler wie der folgende in den [Protokollen der automatischen Clusterskalierung][view-master-logs] angezeigt.

```console
E1114 09:58:55.367731 1 static_autoscaler.go:239] Failed to fix node group sizes: failed to decrease aks-default-35246781-vmss: attempt to delete existing nodes
```

Dieser Fehler ist auf eine Upstream-Racebedingung der automatischen Clusterskalierung zurückzuführen. In einem solchen Fall endet die automatische Clusterskalierung mit einem anderen Wert als dem, der sich tatsächlich im Cluster befindet. Um aus diesem Zustand herauszukommen, deaktivieren und reaktivieren Sie die [automatische Clusterskalierung][cluster-autoscaler].


### <a name="why-do-upgrades-to-kubernetes-116-fail-when-using-node-labels-with-a-kubernetesio-prefix"></a>Warum tritt bei Upgrades auf Kubernetes 1.16 ein Fehler auf, wenn Knotenbezeichnungen mit dem Präfix „kubernetes.io“verwendet werden?

Ab Kubernetes 1.16 kann [nur eine definierte Teilmenge von Bezeichnungen mit dem Präfix kubernetes.io](https://v1-18.docs.kubernetes.io/docs/concepts/overview/working-with-objects/labels/) von „kubelet“ auf Knoten angewendet werden. AKS kann ohne Ihre Zustimmung keine aktiven Bezeichnungen in Ihrem Auftrag entfernen, da dies Ausfallzeiten für betroffene Workloads verursachen kann.

Daher können Sie zum Entschärfen des Problems eine der folgenden Maßnahmen durchführen:

1. Aktualisieren der Clustersteuerungsebene auf Version 1.16 oder höher
2. Hinzufügen eines neuen Knotenpools in Version 1.16 oder höher ohne die nicht unterstützte Bezeichnung „kubernetes.io“
3. Löschen des älteren Knotenpools

AKS untersucht derzeit, ob aktive Bezeichnungen in einem Knotenpool mutiert werden können, um diese Entschärfung zu verbessern.



<!-- LINKS - internal -->
[view-master-logs]: monitor-aks-reference.md#resource-logs
[cluster-autoscaler]: cluster-autoscaler.md

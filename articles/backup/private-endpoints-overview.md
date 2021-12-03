---
title: Übersicht über private Endpunkte
description: Erfahren Sie mehr über die Verwendung privater Endpunkte für Azure Backup und die Szenarien, in denen private Endpunkte dazu beitragen, die Sicherheit Ihrer Ressourcen zu gewährleisten.
ms.topic: conceptual
ms.date: 11/09/2021
ms.custom: devx-track-azurepowershell
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: c9ff432c1de5ebdb44ae8f5fa0d4889026a32252
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132136469"
---
# <a name="overview-and-concepts-of-private-endpoints-for-azure-backup"></a>Übersicht und Konzepte privater Endpunkte für Azure Backup

Azure Backup ermöglicht Ihnen die sichere Sicherung und Wiederherstellung Ihrer Daten aus Ihren Recovery Services-Tresoren unter Verwendung [privater Endpunkte](../private-link/private-endpoint-overview.md). Private Endpunkte verwenden eine oder mehrere private IP-Adressen Ihres Azure Virtual Network* (VNet), sodass der Dienst effektiv in Ihr VNet integriert wird.

In diesem Artikel erfahren Sie, wie private Endpunkte für Azure Backup funktionieren und in welchen Szenarien sie dazu beitragen, die Sicherheit Ihrer Ressourcen zu gewährleisten.

## <a name="before-you-start"></a>Vorbereitung

- Private Endpunkte können nur für neue Recovery Services-Tresore erstellt werden (bei denen keine Elemente im Tresor registriert sind). Daher müssen private Endpunkte erstellt werden, bevor Sie versuchen, Elemente im Tresor zu schützen.
- Ein virtuelles Netzwerk kann private Endpunkte für mehrere Recovery Services-Tresore enthalten. Außerdem kann ein Recovery Services-Tresor über private Endpunkte in mehreren virtuellen Netzwerken verfügen. Die maximale Anzahl privater Endpunkte, die für einen Tresor erstellt werden können, ist jedoch 12.
- Sobald ein privater Endpunkt für einen Tresor erstellt ist, wird der Tresor gesperrt. Der Zugriff (für Sicherungen und Wiederherstellungen) ist nur aus Netzwerken möglich, die einen privaten Endpunkt für den Tresor enthalten. Wenn alle privaten Endpunkte für den Tresor entfernt werden, können alle Netzwerke auf den Tresor zugreifen.
- Eine Verbindung mit privaten Endpunkten für die Sicherung verwendet insgesamt 11 private IP-Adressen in Ihrem Subnetz einschließlich der von Azure Backup zur Sicherung verwendeten. Diese Anzahl kann für bestimmte Azure-Regionen höher sein (bis zu 25). Wir empfehlen daher, genügend private IP-Adressen verfügbar zu haben, wenn Sie versuchen, private Endpunkte für die Sicherung zu erstellen.
- Wenngleich ein Recovery Services-Tresor von sowohl Azure Backup als auch Azure Site Recovery verwendet wird, beschränkt sich die Erörterung in diesem Artikel auf die Verwendung privater Endpunkte für Azure Backup.
- Private Endpunkte für Backup enthalten keinen Zugriff auf Azure Active Directory (Azure AD), und dies muss separat sichergestellt werden. Daher muss für IP-Adressen und FQDNs, die für den Betrieb von Azure AD in einer Region erforderlich sind, ein ausgehender Zugriff aus dem abgesicherten Netzwerk gestattet werden, wenn eine Sicherung von Datenbanken in Azure-VMs und eine Sicherung mit dem MARS-Agent erfolgt. Sie können ggf. auch NSG- und Azure Firewall-Tags verwenden, um Zugriff auf Azure AD zuzulassen.
- Virtuelle Netzwerke mit Netzwerkrichtlinien werden für private Endpunkte nicht unterstützt. Sie müssen [Netzwerkrichtlinien deaktivieren](../private-link/disable-private-endpoint-network-policy.md), ehe Sie fortfahren können.
- Sie müssen den Recovery Services-Ressourcenanbieter erneut mit dem Abonnement registrieren, wenn Sie ihn vor dem 1. Mai 2020 registriert haben. Um den Anbieter erneut zu registrieren, wechseln Sie im Azure-Portal zu Ihrem Abonnement, navigieren Sie in der linken Navigationsleiste zu **Ressourcenanbieter** und wählen Sie **Microsoft.RecoveryServices** und dann **Erneut registrieren** aus.
- [Regionsübergreifende Wiederherstellungen](backup-create-rs-vault.md#set-cross-region-restore) für SQL- und SAP HANA-Datenbanksicherungen werden nicht unterstützt, wenn für den Tresor private Endpunkte aktiviert sind.
- Wenn Sie einen Recovery Services-Tresor, der bereits private Endpunkte verwendet, auf einen neuen Mandanten verschieben, müssen Sie den Recovery Services-Tresor aktualisieren, um die verwaltete Identität des Tresors neu zu erstellen und zu konfigurieren, und bei Bedarf neue private Endpunkte erstellen (die sich auf dem neuen Mandanten befinden sollen). Wenn dies nicht getan wird, kommt es zu Fehlern bei den Sicherungs- und Wiederherstellungsvorgängen. Außerdem müssen alle Azure RBAC-Berechtigungen (Azure Role-based Access Control), die innerhalb des Abonnements eingerichtet wurden, neu konfiguriert werden.

## <a name="recommended-and-supported-scenarios"></a>Empfohlene und unterstützte Szenarien

Solange private Endpunkte für den Tresor aktiviert sind, werden sie nur für die Sicherung und Wiederherstellung von SQL- und SAP HANA-Workloads in einer Sicherung in einer Azure-VM und mit dem MARS-Agent verwendet. Sie können den Tresor auch für die Sicherung anderer Workloads einsetzen (die allerdings keine privaten Endpunkte benötigen). Neben der Sicherung von SQL- und SAP HANA-Workloads und einer Sicherung mit dem MARS-Agent werden private Endpunkte bei einer Azure-VM-Sicherung auch für die Dateiwiederherstellung verwendet. Weitere Informationen finden Sie in der Tabelle unten:

| Szenarien | Empfehlungen |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Sicherung von Workloads in Azure-VM (SQL, SAP HANA), Sicherung mit MARS-Agent | Die Verwendung von privaten Endpunkten wird empfohlen, um die Sicherung und Wiederherstellung zu ermöglichen, ohne dass Sie IPs/FQDNs für Azure Backup oder Azure Storage aus Ihren virtuellen Netzwerken zu einer Zulassungsliste hinzufügen müssen. Stellen Sie in diesem Szenario sicher, dass VMs, die SQL-Datenbanken hosten, Azure AD-IPs oder FQDNs erreichen können. |
| **Azure-VM-Sicherung**                                         | Für die VM-Sicherung ist es nicht erforderlich, Zugriff auf IP-Adressen oder FQDNs zu gewähren. Es werden also keine privaten Endpunkte für eine Sicherung und Wiederherstellung von Datenträgern benötigt.  <br><br>   Die Dateiwiederherstellung aus einem Tresor mit privaten Endpunkten wäre jedoch auf virtuelle Netzwerke beschränkt, die einen privaten Endpunkt für den Tresor enthalten. <br><br>    Wenn Sie nicht verwaltete Datenträger mit Zugriffssteuerungslisten (ACLs) verwenden, stellen Sie sicher, dass das Speicherkonto mit den Datenträgern den Zugriff auf **vertrauenswürdige Microsoft-Dienste** erlaubt, wenn eine ACL vorhanden ist. |
| **Azure Files-Sicherung**                                      | Azure Files-Sicherungen werden im lokalen Speicherkonto gespeichert. Es werden daher keine privaten Endpunkte für eine Sicherung und Wiederherstellung benötigt. |

>[!Note]
>Private Endpunkte werden für DPM- und MABS-Server nicht unterstützt. 

## <a name="difference-in-network-connections-due-to-private-endpoints"></a>Unterschied bei Netzwerkverbindungen aufgrund privater Endpunkte

Wie bereits erwähnt, sind private Endpunkte besonders nützlich für die Sicherung von Workloads (SQL, SAP HANA) in Azure-VMs und MARS-Agent-Sicherungen. In allen Szenarien (mit oder ohne private Endpunkte) führen sowohl die Workloaderweiterungen (für die Sicherung von SQL- als auch SAP HANA-Instanzen, die auf Azure-VMs ausgeführt werden) und der MARS-Agent Verbindungsaufrufe an AAD aus (zu FQDNs, die in den Abschnitten 56 und 59 in [Microsoft 365 Common und Office Online](/office365/enterprise/urls-and-ip-address-ranges#microsoft-365-common-and-office-online) erwähnt werden).

Zusätzlich zu diesen Verbindungen, wenn die Workloaderweiterung oder der MARS-Agent für den Recovery Services-Tresor _ohne private Endpunkte_ installiert wird, ist auch eine Verbindung mit den folgenden Domänen erforderlich:

| Dienst | Domänennamen |
| ------- | ------------ |
| Azure Backup  | *.backup.windowsazure.com |
| Azure Storage | *.blob.core.windows.net <br> *.queue.core.windows.net <br> *.blob.storage.azure.net |

Wenn die Workloaderweiterung oder der MARS-Agent für den Recovery Services-Tresor mit privatem Endpunkt installiert ist, werden die folgenden Endpunkte erreicht:

| Dienst | Domänennamen |
| ------- | ------------ |
| Azure Backup  | `*.privatelink.<geo>.backup.windowsazure.com` |
| Azure Storage | *.blob.core.windows.net <br> *.queue.core.windows.net <br> *.blob.storage.azure.net |

>[!Note]
>Im oben stehenden Text bezieht sich `<geo>` auf den Regionscode (z. B. **eus** für „USA, Osten“ und **ne** für „Europa, Norden“). In den folgenden Listen finden Sie die Regionscodes:
>- [Alle öffentlichen Clouds](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/AzureRegionCodesList.docx)
>- [China](/azure/china/resources-developer-guide#check-endpoints-in-azure)
>- [Deutschland](../germany/germany-developer-guide.md#endpoint-mapping)
>- [US Gov](../azure-government/documentation-government-developer-guide.md)

Die in beiden Szenarien betroffenen Speicher-FQDNs sind identisch. Bei einem Recovery Services-Tresor mit Einrichtung eines privaten Endpunkts sollte die Namensauflösung für diese jedoch eine private IP-Adresse zurückgeben. Dies kann erreicht werden, indem private DNS-Zonen verwendet, DNS-Einträge für das Speicherkonto in Hostdateien erstellt oder bedingte Weiterleitungen an das benutzerdefinierte DNS mit den entsprechenden DNS-Einträgen verwendet werden. Die privaten IP-Zuordnungen für das Speicherkonto sind auf dem Blatt „Privater Endpunkt“ für das Speicherkonto im Portal aufgeführt.

>Die privaten Endpunkte für Blobs und Warteschlangen folgen einem Standardbenennungsmuster, sie beginnen mit  **\<the name of the private endpoint>_ecs** oder **\<the name of the private endpoint>_prot** und haben das Suffix  **\_blob**  bzw.  **\_queue** .

Die Endpunkte für den Azure Backup-Dienst werden für Tresore mit aktivierten privaten Endpunkten geändert.  
Wenn Sie einen DNS-Proxyserver mit Proxyservern und Firewalls von Drittanbietern konfiguriert haben, müssen die oben genannten Domänennamen zugelassen und an einen benutzerdefinierten DNS (mit Zuordnungen privater IP-Adressen) oder an 168.63.129.16 mit einer virtuellen Netzwerkverbindung zu einer privaten DNS-Zone mit diesen privaten IP-Adressenzuordnungen umgeleitet werden.

Das folgende Beispiel zeigt, wie die Azure-Firewall als DNS-Proxy verwendet wird, um die Domänennamenabfragen für Recovery Services Vault, Blob, Queues und AAD an 168.63.129.16 umzuleiten.

:::image type="content" source="./media/private-endpoints-overview/azure-firewall-used-as-dns-proxy-inline.png" alt-text="Diagramm, das die Verwendung von Azure Firewall als DNS-Proxy zum Umleiten der Domänennamenabfragen zeigt." lightbox="./media/private-endpoints-overview/azure-firewall-used-as-dns-proxy-expanded.png":::

Weitere Informationen finden Sie unter [Erstellen und Verwenden privater Endpunkte](private-endpoints.md).

## <a name="network-connectivity-setup-for-vault-with-private-endpoints"></a>Einrichten der Netzwerkkonnektivität für den Tresor mit privaten Endpunkten

Der private Endpunkt für Wiederherstellungsdienste ist einer Netzwerkschnittstelle (NIC) zugeordnet, die über eine private IP-Adresse verfügt. Damit private Endpunktverbindungen funktionieren (Weiterleiten des gesamten Datenverkehrs an den Dienst über den Azure-Backbone und Einschränken des Dienstzugriffs auf Clients in Ihrem VNET), muss der gesamte Kommunikationsdatenverkehr für den Dienst an diese Netzwerkschnittstelle umgeleitet werden. Dies kann durch die Verwendung von DNS erreicht werden, das mit VNET oder Hostdateieinträgen auf dem Computer verbunden ist, auf dem die Erweiterung bzw. der Agent ausgeführt wird.

Die Workloadsicherungserweiterung und der MARS-Agent werden auf einem virtuellen Azure-Computer in einem VNET oder einem lokalen virtuellen Computer ausgeführt, der mittels Peering mit VNET verbunden ist. Bei der Registrierung bei einem Recovery Services-Tresor mit einem privaten Endpunkt, der mit diesem VNET verknüpft ist, ändert sich die Dienst-URL der Azure Backup Clouddienste für die Erweiterung und den Agent von `<azure_backup_svc >.<geo>.backup.windowsazure.com` in `<vault_id>.<azure_backup_svc>.privatelink.<geo>.backup`.windowsazure.com**.

>[!Note]
>Im oben stehenden Text bezieht sich `<geo>` auf den Regionscode (z. B. **eus** für „USA, Osten“ und **ne** für „Europa, Norden“). In den folgenden Listen finden Sie die Regionscodes:
>- [Alle öffentlichen Clouds](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/AzureRegionCodesList.docx)
>- [China](/azure/china/resources-developer-guide#check-endpoints-in-azure)
>- [Deutschland](../germany/germany-developer-guide.md#endpoint-mapping)
>- [US Gov](../azure-government/documentation-government-developer-guide.md)

Die geänderten URLs sind spezifisch für einen Tresor.  Siehe `<vault_id>` im URL-Namen Nur bei diesem Tresor registrierte Erweiterungen und Agents können über diese Endpunkte mit Azure Backup kommunizieren. Dadurch wird der Zugriff auf die Clients in diesem VNET beschränkt. Die Erweiterung/der Agent kommuniziert über `*.privatelink.<geo>.backup.windowsazure.com`, was die entsprechende private IP-Adresse in der NIC auflösen muss.

Wenn der private Endpunkt für Recovery Services-Tresore über Azure-Portal mit der Option **In private DNS-Zone integrieren** erstellt wird, werden die erforderlichen DNS-Einträge für private IP-Adressen für Azure Backup-Dienste (`*.privatelink.<geo>backup.windowsazure.com`) automatisch erstellt, wenn die Ressource zugeordnet wird. Andernfalls müssen Sie die DNS-Einträge für diese FQDNs manuell im benutzerdefinierten DNS oder in den Hostdateien erstellen.

Informationen zur manuellen Verwaltung von DNS-Einträgen nach der VM-Ermittlung für den Kommunikationskanal – Blob/Warteschlange finden Sie unter [ DNS-Einträge für Blobs und Warteschlangen (nur für benutzerdefinierte DNS-Server/Hostdateien) nach der ersten Registrierung](./private-endpoints.md#dns-records-for-blobs-and-queues-only-for-custom-dns-servershost-files-after-the-first-registration). Informationen zur manuellen Verwaltung von DNS-Einträgen nach der ersten Sicherung für das Sicherungsspeicherkontoblob finden Sie unter [ DNS-Einträge für Blobs (nur für benutzerdefinierte DNS-Server/Hostdateien) nach der ersten Sicherung](./private-endpoints.md#dns-records-for-blobs-only-for-custom-dns-servershost-files-after-the-first-backup).

>Die privaten IP-Adressen für die FQDNs finden Sie auf dem Blatt „Privater Endpunkt“ für den privaten Endpunkt, der für den Recovery Services-Tresor erstellt wurde.

Das folgende Diagramm zeigt, wie die Auflösung funktioniert, wenn eine private DNS-Zone zur Auflösung dieser geänderten Dienst-FQDNs verwendet wird. 

:::image type="content" source="./media/private-endpoints-overview/use-private-dns-zone-to-resolve-modified-service-fqdns-inline.png" alt-text="Das Diagramm zeigt, wie die Auflösung funktioniert, wenn eine private DNS-Zone zur Auflösung dieser geänderten Dienst-FQDNs verwendet wird." lightbox="./media/private-endpoints-overview/use-private-dns-zone-to-resolve-modified-service-fqdns-expanded.png":::

Zusätzlich zur Verbindung mit Azure Backup-Clouddiensten erfordern die Workloaderweiterung und der Agent eine Verbindung mit Azure-Speicherkonten und Azure Active Directory. Die Workloaderweiterung, die auf einem virtuellen Azure-Computer ausgeführt wird, erfordert eine Verbindung mit mindestens zwei Speicherkonten. Das erste wird als Kommunikationskanal (über Warteschlangennachrichten) und das zweite zum Speichern von Sicherungsdaten verwendet. Der MARS-Agent benötigt Zugriff auf ein Speicherkonto, das zum Speichern von Sicherungsdaten verwendet wird.

Für einen Tresor mit aktivierten privaten Endpunkten erstellt Azure Backup einen privaten Endpunkt für diese Speicherkonten, der den Datenverkehr für den Kommunikationskanal und Sicherungsdaten über das Azure-Backbonenetzwerk weiterleitet. Dadurch wird verhindert, dass Datenverkehr im Hinblick auf Azure Backup das virtuelle Netzwerk verlässt.

Als Voraussetzung erfordert der Recovery Services-Tresor Berechtigungen zum Erstellen zusätzlicher privater Endpunkte in derselben Ressourcengruppe. Außerdem wird empfohlen, dem Recovery Services-Tresor die Berechtigungen zum Erstellen von DNS-Einträgen in den privaten DNS-Zonen (privatelink.blob.core.windows.net, privatelink.queue.core.windows.net) bereitzustellen. Der Recovery Services-Tresor sucht in den Ressourcengruppen, in denen das VNET und der private Endpunkt erstellt werden, nach privaten DNS-Zonen. Wenn er über die Berechtigungen zum Hinzufügen von DNS-Einträgen in diesen Zonen verfügt, werden sie vom Tresor erstellt. Andernfalls müssen diese manuell vom Benutzer in seinem benutzerdefinierten DNS oder in einer privaten DNS-Zone erstellt werden, die mit dem VNET verknüpft ist.

>Die privaten IP-Zuordnungen sind auf dem Blatt „Privater Endpunkt“ für die Blobs und Warteschlangen im Portal verfügbar.

Das folgende Diagramm zeigt, wie die Namensauflösung für Speicherkonten funktioniert, die eine private DNS-Zone verwenden.

:::image type="content" source="./media/private-endpoints-overview/name-resolution-works-for-storage-accounts-using-private-dns-zone-inline.png" alt-text="Das Diagramm zeigt, wie die Namensauflösung für Speicherkonten funktioniert, die eine private DNS-Zone verwenden." lightbox="./media/private-endpoints-overview/name-resolution-works-for-storage-accounts-using-private-dns-zone-expanded.png":::

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen und verwenden Sie private Endpunkte](private-endpoints.md).
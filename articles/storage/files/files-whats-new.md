---
title: Neuerungen in Azure Files
description: Informieren Sie sich über neue Features und Verbesserungen in Azure Files.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 08/31/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 6bc44ba9bc2cad45ab447b86a580fb505c8abd6d
ms.sourcegitcommit: 7b6ceae1f3eab4cf5429e5d32df597640c55ba13
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/31/2021
ms.locfileid: "123273229"
---
# <a name="whats-new-in-azure-files"></a>Neuerungen in Azure Files
Azure Files wird regelmäßig zur Bereitstellung neuer Features und Verbesserungen aktualisiert. Dieser Artikel enthält ausführliche Informationen zu den Neuerungen in Azure Files.

## <a name="2021-quarter-3-july-august-september"></a>3\. Quatal 2021 (Juli, August, September)
### <a name="smb-multichannel-is-generally-available"></a>SMB Multichannel ist allgemein verfügbar
SMB Multichannel ermöglicht SMB-Clients das Herstellen mehrerer paralleler Verbindungen mit einer Azure-Dateifreigabe. Dadurch können SMB-Clients die gesamte verfügbare Netzwerkbandbreite nutzen, und sie sind widerstandsfähiger gegenüber Netzwerkausfällen, wodurch die Gesamtbetriebskosten reduziert werden und eine 2-3-fache Leistung bei Lesevorgängen und eine 3-4-fache Leistung bei Schreibvorgängen über einen einzigen Client ermöglicht wird. SMB Multichannel ist für Premium-Dateifreigaben (im Speicherkontotyp „FileStorage“ bereitgestellte Dateifreigaben) verfügbar und standardmäßig deaktiviert. 

Weitere Informationen finden Sie unter:

- [SMB Multichannel-Leistung – Azure Files](storage-files-smb-multichannel-performance.md)
- [Aktivieren von SMB Multichannel](files-smb-protocol.md#smb-multichannel)
- [Übersicht über SMB Multichannel in der Dokumentation zu Windows Server](/azure-stack/hci/manage/manage-smb-multichannel)

### <a name="smb-311-and-smb-security-settings"></a>SMB 3.1.1 und SMB-Sicherheitseinstellungen
SMB 3.1.1 ist die neueste Version des SMB-Protokolls, die mit Windows 10 veröffentlicht wurde und wichtige Sicherheits- und Leistungsupdates enthält. Azure Files SMB 3.1.1 unterstützt jetzt neben AES-128-CCM zwei zusätzliche Verschlüsselungsmodi: AES-128-GCM und AES-256-GCM. Um die Leistung zu maximieren, wird AES-128-GCM als Standardoption für die SMB-Kanalverschlüsselung ausgehandelt. AES-128-CCM wird nur auf älteren Clients ausgehandelt, die AES-128-GCM nicht unterstützen. 

Je nach den gesetzlichen Vorgaben und Complianceanforderungen Ihrer Organisation kann AES-256-GCM anstelle von AES-128-GCM ausgehandelt werden, indem zulässige SMB-Kanalverschlüsselungsoptionen entweder auf den SMB-Clients oder in Azure Files oder auf beiden Seiten eingeschränkt werden. Unterstützung für AES-256-GCM wurde in Windows Server 2022 und Windows 10 (Version 21H1) hinzugefügt.

Zusätzlich zu SMB 3.1.1 bietet Azure Files Sicherheitseinstellungen, mit denen Sie das Verhalten des SMB-Protokolls ändern können. Mit dieser Version können Sie zulässige SMB-Protokollversionen, SMB-Kanalverschlüsselungsoptionen, Authentifizierungsmethoden und Kerberos-Ticketverschlüsselungsoptionen konfigurieren. Standardmäßig aktiviert Azure Files die kompatibelsten Optionen. Diese Optionen können jedoch jederzeit über einen Umschalter deaktiviert (und wieder aktiviert) werden.

Weitere Informationen finden Sie unter:

- [SMB-Sicherheitseinstellungen](files-smb-protocol.md#smb-security-settings)
- [Einbinden einer Azure-SMB-Dateifreigabe unter Windows](storage-how-to-use-files-windows.md) und [Einbinden einer Azure-SMB-Dateifreigabe unter Linux](storage-how-to-use-files-linux.md) – SMB-Versionen
- [Übersicht über SMB-Features in der Dokumentation zu Windows Server](/windows-server/storage/file-server/file-server-smb-overview)

## <a name="2021-quarter-2-april-may-june"></a>2\. Quatal 2021 (April, Mai, Juni)
### <a name="premium-hot-and-cool-storage-capacity-reservations"></a>Speicherkapazitätsreservierungen für die Ebenen „Premium“, „Heiß“ und „Kalt“ 
Azure Files unterstützt Speicherkapazitätsreservierungen (auch als *Reserveinstanzen* bezeichnet). Mit Speicherkapazitätsreservierungen können Sie einen Preisnachlass für Speicherplatz erzielen, indem Sie sich im Voraus auf die Speichernutzung festlegen. Azure Files unterstützt Kapazitätsreservierungen für die Ebenen „Premium“, „Heiß“ und „Kalt“. Kapazitätsreservierungen werden in Einheiten von 10 TiB oder 100 TiB für eine Laufzeit von einem Jahr oder drei Jahren verkauft. 

Weitere Informationen finden Sie unter:

- [Grundlegendes zur Abrechnung für Azure Files](understanding-billing.md)
- [Optimieren der Kosten für Azure Files mit reservierter Kapazität](files-reserve-capacity.md)
- [Azure Files – Preise](https://azure.microsoft.com/pricing/details/storage/files/)

### <a name="improved-portal-experience-for-domain-joining-to-active-directory"></a>Verbesserte Portaloberfläche für die Domäneneinbindung in Active Directory
Die Benutzeroberfläche für das Einbinden eines Azure-Speicherkontos in eine Domäne wurde verbessert, um Administratoren für Azure-Dateifreigaben bei der erstmaligen Ausführung des Prozesses zu unterstützen. Wenn Sie im Azure-Portal im Abschnitt **Dateifreigaben** unter **Dateifreigabeeinstellungen** die Option „Active Directory“ auswählen, werden Sie durch die für die Domäneneinbindung erforderlichen Schritte geführt.

:::image type="content" source="media/files-whats-new/ad-domain-join-1.png" alt-text="Screenshot der neuen Portaloberfläche für die Domäneneinbindung eines Speicherkontos in Active Directory" lightbox="media/files-whats-new/ad-domain-join-1.png":::

Weitere Informationen finden Sie unter:

- [Übersicht über die Optionen der identitätsbasierten Authentifizierung mit Azure Files für den SMB-Zugriff](storage-files-active-directory-overview.md)
- [Übersicht – lokale Active Directory Domain Services-Authentifizierung über SMB für Azure-Dateifreigaben](storage-files-identity-auth-active-directory-enable.md)

## <a name="2021-quarter-1-january-february-march"></a>1\. Quatal 2021 (Januar, Februar, März)
### <a name="azure-files-management-now-available-through-the-control-plane"></a>Azure Files-Verwaltung jetzt über die Steuerungsebene verfügbar
Verwaltungs-APIs für Azure Files-Ressourcen, den Dateidienst und Dateifreigaben sind jetzt über die Steuerungsebene (`Microsoft.Storage`-Ressourcenanbieter) verfügbar. Dadurch können Azure-Dateifreigaben mit einer Azure Resource Manager- oder Bicep-Vorlage erstellt werden, sodass sie vollständig verwaltet werden können, wenn auf die Datenebene (d. h. die FileREST-API) nicht zugegriffen werden kann (z. B. wenn der öffentliche Endpunkt des Speicherkontos deaktiviert ist), und die vollständige Semantik der rollenbasierten Zugriffssteuerung (Role-Based Access Control, RBAC) unterstützt wird.

Wir empfehlen Ihnen, Azure Files in den meisten Fällen über die Steuerungsebene zu verwalten. Zur Unterstützung der Verwaltung des Dateidiensts und der Dateifreigaben über die Steuerungsebene wurden das Azure-Portal, das Azure Storage PowerShell-Modul und die Azure-Befehlszeilenschnittstelle (Command-Line Interface, CLI) so aktualisiert, dass die meisten Verwaltungsaktionen über die Steuerungsebene unterstützt werden. 

Zur Beibehaltung des vorhandenen Skriptverhaltens werden im Azure Storage PowerShell-Modul und in der Azure-Befehlszeilenschnittstelle die vorhandenen Befehle beibehalten, bei denen die Datenebene zum Verwalten des Dateidiensts und der Dateifreigaben verwendet wird, und es werden neue Befehle zur Verwendung der Steuerungsebene hinzugefügt. Portalanforderungen werden nur über den Ressourcenanbieter der Steuerungsebene gesendet. PowerShell- und CLI-Befehle werden wie folgt benannt:

- Az.Storage PowerShell:
    - Cmdlets für Dateifreigaben auf Steuerungsebene wird das Präfix `Rm` vorangestellt. Beispiele hierfür sind `New-AzRmStorageShare`, `Get-AzRmStorageShare`, `Update-AzRmStorageShare` und `Remove-AzRmStorageShare`. 
    - Herkömmliche Cmdlets für Dateifreigaben auf Datenebene weisen kein Präfix auf. Beispiele hierfür sind `New-AzStorageShare`, `Get-AzStorageShare`, `Set-AzStorageShareQuota` und `Remove-AzStorageShare`.
    - Cmdlets zum Verwalten des Dateidiensts sind nur über die Steuerungsebene verfügbar und weisen kein spezielles Präfix auf. Beispiele hierfür sind `Get-AzStorageFileServiceProperty` und `Update-AzStorageFileServiceProperty`.
- Azure Storage CLI:
    - Befehle für Dateifreigaben auf Steuerungsebene sind unter der Befehlsgruppe `az storage share-rm` verfügbar, z. B. `az storage share-rm create`, `az storage share-rm update` usw.
    - Herkömmliche Dateifreigabebefehle sind unter der Befehlsgruppe `az storage share` verfügbar, z. B. `az storage share create`, `az storage share update` usw.
    - Befehle zum Verwalten des Dateidiensts sind nur über die Steuerungsebene verfügbar und werden über die Befehlsgruppe `az storage account file-service-properties` bereitgestellt. Beispiele hierfür sind `az storage account file-service-properties show` und `az storage account file-service-properties update`.

Weitere Informationen zur Azure Files-Verwaltungs-API finden Sie unter:

- [Übersicht über die Azure Files-REST-API](/rest/api/storageservices/file-service-rest-api)
- Steuerungsebenen-API (`Microsoft.Storage`-Ressourcenanbieter) für Azure Files Ressourcen: 
    - [`FileService`](/rest/api/storagerp/file-services) 
    - [`FileShare`](/rest/api/storagerp/file-shares) 
- [Azure PowerShell](/powershell/module/az.storage) und [Azure-Befehlszeilenschnittstelle](/en-us/cli/azure/storage)

## <a name="2020-quarter-4-october-november-december"></a>4\. Quatal 2020 (Oktober, November, Dezember)
### <a name="azure-file-share-soft-delete"></a>Vorläufiges Löschen von Azure-Dateifreigaben
Azure-Dateifreigaben unterstützen das vorläufige Löschen. Wenn „Vorläufiges Löschen“ aktiviert ist, können versehentlich gelöschte Freigaben problemlos wiederhergestellt werden, falls der Löschvorgang innerhalb des benutzerdefinierten Aufbewahrungszeitraums rückgängig gemacht wird. Während des Aufbewahrungszeitraums fallen für vorläufig gelöschte Freigaben Gebühren für die genutzte Datenkapazität an. Vorläufig gelöschte Dateifreigaben werden zum Tarif für Momentaufnahmen abgerechnet.

Weitere Informationen zum vorläufigen Löschen finden Sie unter den folgenden Links:

- [Vorläufiges Löschen: Übersicht](storage-files-prevent-file-share-deletion.md)
- [Vorläufiges Löschen aktivieren](storage-files-enable-soft-delete.md)
- [Azure Files – Preise](https://azure.microsoft.com/pricing/details/storage/files/)

## <a name="see-also"></a>Weitere Informationen
- [Was ist Azure Files?](storage-files-introduction.md)
- [Planung für eine Azure Files-Bereitstellung](storage-files-planning.md)
- [Erstellen einer Azure-Dateifreigabe](storage-how-to-create-file-share.md)

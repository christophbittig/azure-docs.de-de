---
title: Erstellen und Verwenden privater Endpunkte für Azure Backup
description: Erfahren Sie mehr über den Prozess zum Erstellen privater Endpunkte für Azure Backup, wo private Endpunkte dazu beitragen, die Sicherheit Ihrer Ressourcen zu gewährleisten.
ms.topic: conceptual
ms.date: 11/09/2021
ms.custom: devx-track-azurepowershell
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: e474ae10e1461e834312e4ff52b36ece57bcc30e
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132137919"
---
# <a name="create-and-use-private-endpoints-for-azure-backup"></a>Erstellen und Verwenden privater Endpunkte für Azure Backup

Dieser Artikel enthält Informationen darüber, wie [private Endpunkte für Azure Backup](private-endpoints-overview.md) erstellt werden und in welchen Szenarien sie dazu beitragen, die Sicherheit Ihrer Ressourcen zu gewährleisten.

## <a name="before-you-start"></a>Vorbereitung

Stellen Sie sicher, dass Sie die [Voraussetzungen](private-endpoints-overview.md#before-you-start) und die [unterstützten Szenarien](private-endpoints-overview.md#recommended-and-supported-scenarios) überprüft haben, bevor Sie mit der Erstellung privater Endpunkte fortfahren.

Diese Details helfen Ihnen, die Einschränkungen und Bedingungen zu verstehen, die erfüllt werden müssen, bevor Sie private Endpunkte für Ihre Tresore erstellen.

## <a name="get-started-with-creating-private-endpoints-for-backup"></a>Erste Schritte mit dem Erstellen privater Endpunkte für die Azure Backup

Die folgenden Abschnitte befassen sich mit den Schritten zum Erstellen und Verwenden privater Endpunkte für Azure Backup innerhalb Ihrer virtuellen Netzwerke.

>[!IMPORTANT]
> Sie sollten die in diesem Dokument beschriebenen Schritte unbedingt in der genannten Reihenfolge durchführen. Andernfalls kann dies dazu führen, dass der Tresor nicht mehr mit dem Einsatz privater Endpunkte kompatibel ist und Sie den Vorgang mit einem neuen Tresor neu starten müssen.

## <a name="create-a-recovery-services-vault"></a>Erstellen eines Recovery Services-Tresors

Private Endpunkte für Azure Backup können nur für Recovery Services-Tresore erstellt werden, die keine geschützten Elemente enthalten (oder für die in der Vergangenheit nicht versucht wurden, Elemente zu schützen oder zu registrieren). Sie sollten daher mit einem neu erstellten Tresor beginnen. Weitere Informationen zum Erstellen eines neuen Tresors finden Sie unter [Erstellen und Konfigurieren eines Recovery Services-Tresors](backup-create-rs-vault.md).

In [diesem Abschnitt](#create-a-recovery-services-vault-using-the-azure-resource-manager-client) erfahren Sie, wie Sie einen Tresor mit dem Azure Resource Manager-Client erstellen. Dadurch wird ein Tresor erstellt, dessen verwaltete Identität bereits aktiviert ist.

## <a name="enable-managed-identity-for-your-vault"></a>Aktivieren der verwalteten Identität für Ihren Tresor

Verwaltete Identitäten ermöglichen dem Tresor, private Endpunkte zu erstellen und zu nutzen. In diesem Abschnitt wird das Aktivieren der verwalteten Identität für Ihren Tresor erläutert.

1. Navigieren Sie zu Ihrem Recovery Services-Tresor und dann zu **Identität**.

    ![Ändern von „Identitätsstatus“ in „Ein“](./media/private-endpoints/identity-status-on.png)

1. Ändern Sie den **Status** in **Ein**, und klicken Sie auf **Speichern**.

1. Es wird eine **Objekt-ID** generiert, die die verwaltete Identität des Tresors darstellt.

    >[!NOTE]
    >Nach der Aktivierung darf die verwaltete Identität **nicht** deaktiviert werden (auch nicht vorübergehend). Die Deaktivierung der verwalteten Identität kann zu inkonsistentem Verhalten führen.

## <a name="grant-permissions-to-the-vault-to-create-required-private-endpoints"></a>Erteilen von Berechtigungen für den Tresor zum Erstellen erforderlicher privater Endpunkte

Um die erforderlichen privaten Endpunkte für Azure Backup zu erstellen, muss der Tresor (bzw. die verwaltete Identität des Tresors) über Berechtigungen für die folgenden Ressourcengruppen verfügen:

- Die Ressourcengruppe mit dem Ziel-VNET
- Die Ressourcengruppe, in der die privaten Endpunkte erstellt werden sollen
- Die Ressourcengruppe, die die privaten DNS-Zonen enthält, wie detailliert unter [Erstellen privater Endpunkte für Azure Backup](#create-private-endpoints-for-azure-backup) erläutert

Wir empfehlen, dem Tresor (der verwalteten Identität) die Rolle **Mitwirkender** für diese drei Ressourcengruppen zu gewähren. Die folgenden Schritte beschreiben, wie dies für eine bestimmte Ressourcengruppe erfolgt (was für jede der drei Ressourcengruppen erfolgen muss):

1. Wechseln Sie zur Ressourcengruppe und dann auf der linken Leiste zu **Zugriffssteuerung (IAM)** .
1. Wechseln Sie in **Zugriffssteuerung (IAM)** zu **Rollenzuweisung hinzufügen**.

    ![Hinzufügen einer Rollenzuweisung](./media/private-endpoints/add-role-assignment.png)

1. Wählen Sie im Bereich **Rollenzuweisung hinzufügen** die Option **Mitwirkender** als **Rolle** aus, und verwenden Sie den **Namen** des Tresors als **Prinzipal**. Wählen Sie Ihren Tresor und dann **Speichern** aus, wenn Sie fertig sind.

    ![Wählen von „Rolle“ und „Prinzipal“](./media/private-endpoints/choose-role-and-principal.png)

Informationen zum Verwalten von Berechtigungen auf detaillierterer Ebene finden Sie unter [ Manuelles Erstellen von Rollen und Berechtigungen](#create-roles-and-permissions-manually).

## <a name="create-private-endpoints-for-azure-backup"></a>Erstellen privater Endpunkte für Azure Backup

In diesem Abschnitt wird erklärt, wie Sie einen privaten Endpunkt für Ihren Tresor erstellen.

1. Navigieren Sie zu Ihrem oben erstellten Tresor, und wechseln Sie dann in der linken Navigationsleiste zu **Private Endpunktverbindungen**. Wählen Sie oben **+Privater Endpunkt** aus, um die Erstellung eines neuen privaten Endpunkts für diesen Tresor zu starten.

    ![Erstellen eines neuen privaten Endpunkts](./media/private-endpoints/new-private-endpoint.png)

1. Sobald Sie sich im Prozess **Privaten Endpunkt erstellen** befinden, müssen Sie Details für die Herstellung der Verbindung mit Ihrem privaten Endpunkt angeben.
  
    1. **Grundlagen**: Geben Sie die grundlegenden Details für Ihre privaten Endpunkte ein. Die Region sollte mit der des Tresors und der Ressource, die gesichert wird, identisch sein.

        ![Grundlegende Informationen eingeben](./media/private-endpoints/basics-tab.png)

    1. **Ressource:** Auf dieser Registerkarte müssen Sie die PaaS-Ressource auswählen, für die Sie Ihre Verbindung herstellen möchten. Wählen Sie **Microsoft.RecoveryServices/vaults** als Ressourcentyp für Ihr gewünschtes Abonnement. Wählen Sie anschließend den Namen Ihres Recovery Services-Tresors für **Ressource** und **Azure Backup** für **Zielunterressource** aus.

        ![Auswählen der Ressource für Ihre Verbindung](./media/private-endpoints/resource-tab.png)

    1. **Konfiguration:** Geben Sie in der Konfiguration das virtuelle Netzwerk und Subnetz an, in dem der private Endpunkt erstellt werden soll. Dies ist das VNET mit der VM.

        Um eine private Verbindung herzustellen, benötigen Sie die erforderlichen DNS-Einträge. Basierend auf Ihrem Netzwerksetup können Sie zwischen den folgenden Möglichkeiten auswählen:

          - Integrieren Ihres privaten Endpunkts in eine private DNS-Zone: Wählen Sie **Ja** aus, wenn Sie die Integration fortsetzen möchten.
          - Verwenden des eigenen DNS-Servers: Wählen Sie **Nein** aus, wenn Sie einen eigenen DNS-Server verwenden möchten.

        Die Verwaltung von DNS-Einträgen für beide wird [später beschrieben](#manage-dns-records).

          ![Festlegen des virtuellen Netzwerks und des Subnetzes](./media/private-endpoints/configuration-tab.png)

    1. Optional können Sie für Ihren privaten Endpunkt **Tags** hinzufügen.
    1. Fahren Sie mit **Überprüfen + erstellen** fort, nachdem Sie die Details eingegeben haben. Klicken Sie nach Abschluss der Validierung auf **Erstellen**, um den privaten Endpunkt zu erstellen.

## <a name="approve-private-endpoints"></a>Genehmigen privater Endpunkte

Wenn der Benutzer, der den privaten Endpunkt erstellt, auch der Besitzer des Recovery Services-Tresors ist, wird der zuvor erstellte private Endpunkt automatisch genehmigt. Andernfalls muss der Besitzer des Tresors den privaten Endpunkt genehmigen, bevor er genutzt werden kann. In diesem Abschnitt wird die manuelle Genehmigung privater Endpunkte im Azure-Portal erläutert.

Weitere Informationen zum Genehmigen privater Endpunkte mithilfe des Azure Resource Manager-Clients finden Sie unter [Manuelle Genehmigung privater Endpunkte mithilfe des Azure Resource Manager-Clients](#manual-approval-of-private-endpoints-using-the-azure-resource-manager-client).

1. Navigieren Sie in Ihrem Recovery Services-Tresor auf der linken Leiste zu **Verbindungen mit privatem Endpunkt**.
1. Wählen Sie die Verbindung mit dem privaten Endpunkt aus, die Sie genehmigen möchten.
1. Wählen Sie auf der oberen Leiste **Genehmigen** aus. Sie können auch **Ablehnen** oder **Entfernen** auswählen, wenn Sie die Verbindung mit dem Endpunkt ablehnen oder löschen möchten.

    ![Genehmigen privater Endpunkte](./media/private-endpoints/approve-private-endpoints.png)

## <a name="manage-dns-records"></a>Verwalten von DNS-Einträgen

Wie bereits beschrieben, müssen die erforderlichen DNS-Einträge in Ihren privaten DNS-Zonen oder -Servern vorhanden sein, um eine private Verbindung herstellen zu können. Sie können Ihren privaten Endpunkt entweder direkt in die privaten Azure-DNS-Zonen integrieren oder Ihre benutzerdefinierten DNS-Server verwenden, um dies zu erreichen, je nach Ihren Netzwerkeinstellungen. Dies muss für alle drei Dienste durchgeführt werden: Sicherungen, Blobs und Warteschlangen.

Wenn Ihre DNS-Zone oder Ihr DNS-Server in einem Abonnement vorhanden ist, das sich von dem Abonnement mit dem privaten Endpunkt unterscheidet, ziehen Sie außerdem [Erstellen von DNS-Einträgen, wenn der DNS-Server bzw. die DNS-Zone sich in einem anderen Abonnement befindet](#create-dns-entries-when-the-dns-serverdns-zone-is-present-in-another-subscription) zurate. 

### <a name="when-integrating-private-endpoints-with-azure-private-dns-zones"></a>Bei Integration privater Endpunkte in private Azure-DNS-Zonen

Wenn Sie Ihren privaten Endpunkt in private DNS-Zonen integrieren möchten, fügt Azure Backup die erforderlichen DNS-Einträge hinzu. Sie können die verwendeten privaten DNS-Zonen unter **DNS-Konfiguration** des privaten Endpunkts anzeigen. Wenn diese DNS-Zonen nicht vorhanden sind, werden sie beim Erstellen des privaten Endpunkts automatisch erstellt. Sie müssen jedoch sicherstellen, dass Ihr virtuelles Netzwerk (das die zu sichernden Ressourcen enthält) ordnungsgemäß mit allen drei privaten DNS-Zonen verknüpft ist, wie unten beschrieben.

![DNS-Konfiguration in der privaten Azure DNS-Zone](./media/private-endpoints/dns-configuration.png)

>[!Note]
>Wenn Sie Proxyserver verwenden, können Sie den Proxyserver umgehen oder Ihre Sicherungen über den Proxyserver ausführen. Fahren Sie mit den folgenden Abschnitten fort, um einen Proxyserver zu umgehen. Informationen zur Verwendung des Proxyservers zum Ausführen Ihrer Sicherungen finden Sie unter [Details zum Proxyserversetup für den Recovery Services-Tresor](#set-up-proxy-server-for-recovery-services-vault-with-private-endpoint).
#### <a name="validate-virtual-network-links-in-private-dns-zones"></a>Validieren virtueller Netzwerkverbindungen in privaten DNS-Zonen

Führen Sie für jede oben aufgeführte **private DNS**-Zone (für Sicherungen, Blobs und Warteschlangen) folgende Schritte aus:

1. Navigieren Sie in der linken Navigationsleiste zur Option **Virtuelle Netzwerklinks**.
1. Es sollte ein Eintrag für das virtuelle Netzwerk, für das Sie den privaten Endpunkt erstellt haben, wie unten abgebildet, angezeigt werden:

    ![Virtuelles Netzwerk für privaten Endpunkt](./media/private-endpoints/virtual-network-links.png)

1. Wenn kein Eintrag angezeigt wird, fügen Sie einen virtuellen Netzwerklink zu allen DNS-Zonen hinzu, die über keinen Eintrag verfügen.

    ![Hinzufügen einer virtuellen Netzwerkverknüpfung](./media/private-endpoints/add-virtual-network-link.png)

### <a name="when-using-custom-dns-server-or-host-files"></a>Bei Verwendung benutzerdefinierter DNS-Server oder -Hostdateien

Wenn Sie Ihre eigenen DNS-Server verwenden, müssen Sie die erforderlichen DNS-Zonen erstellen und die von den privaten Endpunkten benötigten DNS-Einträge zu Ihren DNS-Servern hinzufügen. Für Blobs und Warteschlangen können Sie auch bedingte Weiterleitungen verwenden.

#### <a name="for-the-backup-service"></a>Für den Azure Backup-Dienst

1. Erstellen Sie in Ihrem DNS-Server eine DNS-Zone für Azure Backup entsprechend der folgenden Namenskonvention:

    |Zone |Dienst |
    |---------|---------|
    |`privatelink.<geo>.backup.windowsazure.com`   |  Backup        |

    >[!NOTE]
    > Im oben stehenden Text bezieht sich `<geo>` auf den Regionscode (z. B. *eus* und *ne* für „USA, Osten“ bzw. „Europa, Norden“). In den folgenden Listen finden Sie die Regionscodes:
    >
    > - [Alle öffentlichen Clouds](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/AzureRegionCodesList.docx)
    > - [China](/azure/china/resources-developer-guide#check-endpoints-in-azure)
    > - [Deutschland](../germany/germany-developer-guide.md#endpoint-mapping)
    > - [US Gov](../azure-government/documentation-government-developer-guide.md)
    > - [Geocodeliste – Beispiel-XML](scripts/geo-code-list.md)

1. Als Nächstes fügen Sie die erforderlichen DNS-Einträge hinzu. Zum Anzeigen der Datensätze, die der Azure Backup-DNZ-Zone hinzugefügt werden müssen, navigieren Sie zu dem oben erstellten privaten Endpunkt und dann in der linken Navigationsleiste zur Option **DNS-Konfiguration**.

    ![DNS-Konfiguration für einen benutzerdefinierten DNS-Server](./media/private-endpoints/custom-dns-configuration.png)

1. Fügen Sie einen Eintrag für jeden FQDN und jede IP hinzu, die als Einträge vom Typ A in Ihrer DNS-Zone für Azure Backup angezeigt werden. Wenn Sie eine Hostdatei für die Namensauflösung verwenden, nehmen Sie für jede IP und jeden FQDN entsprechende Einträge in folgendem Format in der Hostdatei vor:

    `<private ip><space><backup service privatelink FQDN>`

>[!NOTE]
>Wie im obigen Screenshot zu sehen, stellen die FQDNs `xxxxxxxx.<geo>.backup.windowsazure.com` und nicht `xxxxxxxx.privatelink.<geo>.backup. windowsazure.com` dar. Achten Sie in solchen Fällen darauf, dass Sie `.privatelink.`entsprechend dem angegebenen Format angeben (und ggf. hinzufügen).

#### <a name="for-blob-and-queue-services"></a>Für Blob- und Warteschlangendienste

Für Blobs und Warteschlangen können Sie entweder bedingte Weiterleitungen verwenden oder DNS-Zonen in Ihrem DNS-Server erstellen.

##### <a name="if-using-conditional-forwarders"></a>Bei Verwenden bedingter Weiterleitungen

Wenn Sie bedingte Weiterleitungen verwenden, fügen Sie Weiterleitungen für Blob- und Warteschlangen-FQDNs wie folgt hinzu:

|FQDN  |IP  |
|---------|---------|
|`privatelink.blob.core.windows.net`     |  168.63.129.16       |
|`privatelink.queue.core.windows.net`     | 168.63.129.16        |

##### <a name="if-using-private-dns-zones"></a>Bei Verwendung privater DNS-Zonen

Wenn Sie DNS-Zonen für Blobs und Warteschlangen verwenden, müssen Sie zunächst diese DNS-Zonen erstellen und später die erforderlichen A-Einträge hinzufügen.

|Zone |Dienst  |
|---------|---------|
|`privatelink.blob.core.windows.net`     |  Blob     |
|`privatelink.queue.core.windows.net`     | Warteschlange        |

Im Moment werden die Zonen für Blobs und Warteschlangen nur bei Verwendung benutzerdefinierter DNS-Server erstellt. Das Hinzufügen von DNS-Einträgen erfolgt später in zwei Schritten:

1. Beim Registrieren der ersten Sicherungsinstanz, d. h. wenn Sie die Sicherung zum ersten Mal konfigurieren
1. Beim Ausführen der ersten Sicherung

Die Schritte werden in den folgenden Abschnitten ausgeführt.

## <a name="use-private-endpoints-for-backup"></a>Verwenden privater Endpunkte für Azure Backup

Sobald die privaten Endpunkte, die für den Tresor in Ihrem VNET erstellt wurden, genehmigt sind, können Sie sie für Sicherungen und Wiederherstellungen nutzen.

>[!IMPORTANT]
>Vergewissern Sie sich vor dem Fortfahren, dass Sie alle zuvor im Dokument erwähnten Schritte erfolgreich abgeschlossen haben. Zur Erinnerung: Sie müssen die Schritte in der folgenden Checkliste ausgeführt haben:
>
>1. (Neuen) Recovery Services-Tresor erstellt
>2. Den Tresor für die Nutzung der systemseitig zugewiesenen verwalteten Identität aktiviert
>3. Der verwalteten Identität des Tresors die entsprechenden Berechtigungen zugewiesen
>4. Einen privaten Endpunkt für Ihren Tresor erstellt
>5. Den privaten Endpunkt genehmigt (falls nicht automatisch genehmigt)
>6. Sichergestellt, dass alle DNS-Einträge ordnungsgemäß hinzugefügt wurden (mit Ausnahme von Blob- und Warteschlangeneinträgen für benutzerdefinierte Server, die in den folgenden Abschnitten behandelt werden)

### <a name="check-vm-connectivity"></a>Konnektivität des virtuellen Computers überprüft

Stellen Sie auf dem virtuellen Computer im gesperrten Netzwerk Folgendes sicher:

1. Der virtuelle Computer hat Zugriff auf AAD.
2. Führen Sie von Ihrem virtuellen Computer aus **nslookup** auf der Sicherungs-URL (`xxxxxxxx.privatelink.<geo>.backup. windowsazure.com`) aus, um die Konnektivität sicherzustellen. Dies sollte die in Ihrem virtuellen Netzwerk zugewiesene private IP zurückgeben.

### <a name="configure-backup"></a>Konfigurieren der Sicherung

Sobald Sie sichergestellt haben, dass die obige Prüfliste und der Zugriff erfolgreich abgeschlossen wurden, können Sie mit der Konfiguration der Sicherung von Workloads in den Tresor fortfahren. Wenn Sie einen benutzerdefinierten DNS-Server verwenden, müssen Sie DNS-Einträge für Blobs und Warteschlangen hinzufügen, die nach dem Konfigurieren der ersten Sicherung verfügbar sind.

#### <a name="dns-records-for-blobs-and-queues-only-for-custom-dns-servershost-files-after-the-first-registration"></a>DNS-Einträge für Blobs und Warteschlangen (nur für benutzerdefinierte DNS-Server/Hostdateien) nach der ersten Registrierung

Nachdem Sie die Sicherung für mindestens eine Ressource in einem für private Endpunkte aktivierten Tresor konfiguriert haben, fügen Sie die erforderlichen DNS-Einträge für Blobs und Warteschlangen wie unten beschrieben hinzu.

1. Navigieren Sie zu Ihrer Ressourcengruppe, und suchen Sie nach dem von Ihnen erstellten privaten Endpunkt.
1. Neben dem von Ihnen angegebenen Namen des privaten Endpunkts werden zwei weitere private Endpunkte erstellt. Diese beginnen mit `<the name of the private endpoint>_ecs` und weisen den Suffix `_blob` bzw. `_queue` auf.

    ![Private Endpunktressourcen](./media/private-endpoints/private-endpoint-resources.png)

1. Navigieren Sie zu jedem dieser privaten Endpunkte. In der DNS-Konfigurationsoption sehen Sie für jeden der beiden privaten Endpunkte einen Eintrag mit einem FQDN und einer IP-Adresse. Fügen Sie diese beiden zu Ihrem benutzerdefinierten DNS-Server hinzu, zusätzlich zu den bereits beschriebenen.
Wenn Sie eine Hostdatei verwenden, nehmen Sie für jede IP und jeden FQDN entsprechende Einträge in folgendem Format in der Hostdatei vor:

    `<private ip><space><blob service privatelink FQDN>`<br>
    `<private ip><space><queue service privatelink FQDN>`

    ![Blob-DNS-Konfiguration](./media/private-endpoints/blob-dns-configuration.png)

Zusätzlich zu den oben genannten Einträgen ist nach der ersten Sicherung ein weiterer Eintrag erforderlich, der [später](#dns-records-for-blobs-only-for-custom-dns-servershost-files-after-the-first-backup) besprochen wird.

### <a name="backup-and-restore-of-workloads-in-azure-vm-sql-and-sap-hana"></a>Sichern und Wiederherstellen von Workloads in Azure VM (SQL und SAP HANA)

Sobald der private Endpunkt erstellt und genehmigt wurde, sind clientseitig keine weiteren Änderungen erforderlich, um den privaten Endpunkt zu verwenden (es sei denn, Sie verwenden SQL-Verfügbarkeitsgruppen, die später in diesem Abschnitt behandelt werden). Die gesamte Kommunikation und Datenübertragung aus Ihrem abgesicherten Netzwerk zum Tresor erfolgt über den privaten Endpunkt. Wenn Sie jedoch private Endpunkte für den Tresor entfernen, nachdem ein Server (SQL oder SAP HANA) bei ihm registriert wurde, müssen Sie den Container erneut beim Tresor registrieren. Sie müssen den Schutz für sie nicht aufheben.

#### <a name="dns-records-for-blobs-only-for-custom-dns-servershost-files-after-the-first-backup"></a>DNS-Einträge für Blobs (nur für benutzerdefinierte DNS-Server/Hostdateien) nach der ersten Sicherung

Nachdem Sie die erste Sicherung ausgeführt haben und einen benutzerdefinierten DNS-Server (ohne bedingte Weiterleitung) verwenden, ist es wahrscheinlich, dass Ihre Sicherung fehlschlägt. Wenn das passiert:

1. Navigieren Sie zu Ihrer Ressourcengruppe, und suchen Sie nach dem von Ihnen erstellten privaten Endpunkt.
1. Neben den drei bereits besprochenen privaten Endpunkten sehen Sie nun einen vierten privaten Endpunkt, dessen Name mit `<the name of the private endpoint>_prot` beginnt und den Suffix `_blob` aufweist.

    ![Privater Endpunkt mit dem Suffix „prot“](./media/private-endpoints/private-endpoint-prot.png)

1. Navigieren Sie zu diesem neuen privaten Endpunkt. In der DNS-Konfigurationsoption wird ein Eintrag mit einem FQDN und einer IP-Adresse angezeigt. Fügen Sie diese zu Ihrem privaten DNS-Server hinzu, zusätzlich zu den bereits beschriebenen.

    Wenn Sie eine Hostdatei verwenden, nehmen Sie für jede IP und jeden FQDN die entsprechenden Einträge in folgendem Format in der Hostdatei vor:

    `<private ip><space><blob service privatelink FQDN>`

>[!NOTE]
>An diesem Punkt sollten Sie in der Lage sein, **nslookup** vom virtuellen Computer aus auszuführen und die privaten IP-Adressen aufzulösen, wenn dies für die Azure Backup- und Azure Storage-URLs des Tresors erforderlich ist.

### <a name="when-using-sql-availability-groups"></a>Bei Verwendung von SQL-Verfügbarkeitsgruppen

Wenn Sie SQL-Verfügbarkeitsgruppen (AG) verwenden, müssen Sie die bedingte Weiterleitung im benutzerdefinierten Verfügbarkeitsgruppen-DNS bereitstellen, wie unten beschrieben:

1. Melden Sie sich bei Ihrem Domänencontroller an.
1. Fügen Sie unter der DNS-Anwendung je nach Bedarf bedingte Weiterleitungen für alle drei DNS-Zonen (Sicherungen, Blobs und Warteschlangen) zur Host-IP 168.63.129.16 oder der benutzerdefinierten DNS-Server-IP-Adresse hinzu. Die folgenden Screenshots zeigen, wenn Sie auf die Azure-Host-IP weiterleiten. Wenn Sie einen eigenen DNS-Server verwenden, ersetzen Sie diese IP durch die IP Ihres DNS-Servers.

    ![Bedingte Weiterleitungen im DNS-Manager](./media/private-endpoints/dns-manager.png)

    ![Neue bedingte Weiterleitung](./media/private-endpoints/new-conditional-forwarder.png)

### <a name="backup-and-restore-through-mars-agent"></a>Sichern und Wiederherstellen über den Mars-Agent

Wenn Sie den MARS-Agent zur Sicherung Ihrer lokalen Ressourcen verwenden, stellen Sie sicher, dass Ihr lokales Netzwerk (das die zu sichernden Ressourcen enthält) ein Peering mit dem virtuellen Azure-Netzwerk aufweist, das einen privaten Endpunkt für den Tresor enthält, damit Sie ihn nutzen können. Sie können dann mit der Installation des MARS-Agents fortfahren und die Sicherung wie hier beschrieben konfigurieren. Sie müssen jedoch sicherstellen, dass die gesamte Kommunikation für die Sicherung ausschließlich über das Peeringnetzwerk erfolgt.

Wenn Sie jedoch private Endpunkte für den Tresor entfernen, nachdem ein MARS-Agent bei ihm registriert wurde, müssen Sie den Container erneut beim Tresor registrieren. Sie müssen den Schutz für sie nicht aufheben.

## <a name="deleting-private-endpoints"></a>Löschen privater Endpunkte

Informationen zum Löschen privater Endpunkte finden Sie in [diesem Abschnitt](/rest/api/virtualnetwork/privateendpoints/delete).

## <a name="additional-topics"></a>Weitere Themen

### <a name="create-a-recovery-services-vault-using-the-azure-resource-manager-client"></a>Erstellen eines Recovery Services-Tresors mit dem Azure Resource Manager-Client

Sie können den Recovery Services-Tresor erstellen und seine verwaltete Identität mit dem Azure Resource Manager-Client aktivieren (die Aktivierung der verwalteten Identität ist erforderlich, wie wir später sehen werden). Ein Beispiel hierfür finden Sie nachstehend:

```rest
armclient PUT /subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>?api-version=2017-07-01-preview @C:\<filepath>\MSIVault.json
```

Die obige JSON-Datei muss folgenden Inhalt aufweisen:

JSON-Code der Anforderung:

```json
{
  "location": "eastus2",
  "name": "<vaultname>",
  "etag": "W/\"datetime'2019-05-24T12%3A54%3A42.1757237Z'\"",
  "tags": {
    "PutKey": "PutValue"
  },
  "properties": {},
  "id": "/subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>",
  "type": "Microsoft.RecoveryServices/Vaults",
  "sku": {
    "name": "RS0",
    "tier": "Standard"
  },
  "identity": {
    "type": "systemassigned"
  }
}
```

JSON-Code der Antwort:

```json
{
   "location": "eastus2",
   "name": "<vaultname>",
   "etag": "W/\"datetime'2020-02-25T05%3A26%3A58.5181122Z'\"",
   "tags": {
     "PutKey": "PutValue"
   },
   "identity": {
     "tenantId": "<tenantid>",
     "principalId": "<principalid>",
     "type": "SystemAssigned"
   },
   "properties": {
     "provisioningState": "Succeeded",
     "privateEndpointStateForBackup": "None",
     "privateEndpointStateForSiteRecovery": "None"
   },
   "id": "/subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>",
   "type": "Microsoft.RecoveryServices/Vaults",
   "sku": {
     "name": "RS0",
     "tier": "Standard"
   }
 }
```

>[!NOTE]
>Der Tresor, der in diesem Beispiel mit dem Azure Resource Manager-Client erstellt wird, wurde bereits mit einer systemseitig zugewiesenen verwalteten Identität angelegt.

### <a name="managing-permissions-on-resource-groups"></a>Verwalten von Berechtigungen für Ressourcengruppen

Die verwaltete Identität für den Tresor muss in der Ressourcengruppe und im virtuellen Netzwerk, in dem die privaten Endpunkte erstellt werden, über die folgenden Berechtigungen verfügen:

- `Microsoft.Network/privateEndpoints/*` Erforderlich, um CRUD-Vorgänge (Create, Read, Update und Delete [Erstellen, Lesen, Aktualisieren und Löschen]) für private Endpunkte in der Ressourcengruppe auszuführen. Muss der Ressourcengruppe zugewiesen werden.
- `Microsoft.Network/virtualNetworks/subnets/join/action` Erforderlich für das virtuelle Netzwerk, in dem private IP-Adressen mit dem privaten Endpunkt verbunden werden.
- `Microsoft.Network/networkInterfaces/read` Erforderlich für die Ressourcengruppe, um die für den privaten Endpunkt erstellte Netzwerkschnittstelle abzurufen.
- Rolle „Mitwirkender“ für die private DNS-Zone: Ist bereits vorhanden und kann verwendet werden, um die Berechtigungen `Microsoft.Network/privateDnsZones/A/*` und `Microsoft.Network/privateDnsZones/virtualNetworkLinks/read` bereitzustellen.

Sie können mithilfe einer der folgenden Methoden Rollen mit den erforderlichen Berechtigungen anlegen:

#### <a name="create-roles-and-permissions-manually"></a>Manuelles Erstellen von Rollen und Berechtigungen

Erstellen Sie die folgenden JSON-Dateien, und legen Sie anschließend mit dem PowerShell-Befehl am Ende des Abschnitts Rollen an:

//PrivateEndpointContributorRoleDef.json

```json
{
  "Name": "PrivateEndpointContributor",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows management of Private Endpoint",
  "Actions": [
    "Microsoft.Network/privateEndpoints/*",
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000"
  ]
}
```

//NetworkInterfaceReaderRoleDef.json

```json
{
  "Name": "NetworkInterfaceReader",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows read on networkInterfaces",
  "Actions": [
    "Microsoft.Network/networkInterfaces/read"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000"
  ]
}
```

//PrivateEndpointSubnetContributorRoleDef.json

```json
{
  "Name": "PrivateEndpointSubnetContributor",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows adding of Private Endpoint connection to Virtual Networks",
  "Actions": [
    "Microsoft.Network/virtualNetworks/subnets/join/action"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000"
  ]
}
```

```azurepowershell
 New-AzRoleDefinition -InputFile "PrivateEndpointContributorRoleDef.json"
 New-AzRoleDefinition -InputFile "NetworkInterfaceReaderRoleDef.json"
 New-AzRoleDefinition -InputFile "PrivateEndpointSubnetContributorRoleDef.json"
```

#### <a name="use-a-script"></a>Verwenden eines Skripts

1. Starten Sie im Azure-Portal die **Cloud Shell**, und wählen Sie im PowerShell-Fenster **Datei hochladen** aus.

    ![„Datei hochladen“ im PowerShell-Fenster auswählen](./media/private-endpoints/upload-file-in-powershell.png)

1. Laden Sie das folgende Skript hoch: [VaultMsiPrereqScript](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/VaultMsiPrereqScript.ps1)

1. Wechseln Sie zu Ihrem Stammordner (Beispiel: `cd /home/user`).

1. Führen Sie folgendes Skript aus:

    ```azurepowershell
    ./VaultMsiPrereqScript.ps1 -subscription <subscription-Id> -vaultPEResourceGroup <vaultPERG> -vaultPESubnetResourceGroup <subnetRG> -vaultMsiName <msiName>
    ```

    Die Parameter lauten wie folgt:

    - **subscription**: **SubscriptionId mit der Ressourcengruppe, in der der private Endpunkt für den Tresor erstellt werden soll, und dem Subnetz, an das der private Endpunkt des Tresors angefügt werden soll

    - **vaultPEResourceGroup**: Ressourcengruppe, in der die privaten Endpunkte für den Tresor erstellt werden sollen

    - **vaultPESubnetResourceGroup**: Ressourcengruppe des Subnetzes, mit dem der private Endpunkt verknüpft wird

    - **vaultMsiName**: MSI-Name des Tresors, der mit **VaultName** übereinstimmt

1. Vervollständigen Sie die Authentifizierung. Das Skript berücksichtigt den Kontext des oben angegebenen Abonnements. Es erstellt die entsprechenden Rollen, falls sie im Mandanten fehlen, und weist dem MSI des Tresors Rollen zu.

### <a name="creating-private-endpoints-using-azure-powershell"></a>Erstellen privater Endpunkte mit Azure PowerShell

#### <a name="auto-approved-private-endpoints"></a>Automatisch genehmigte private Endpunkte

```azurepowershell
$vault = Get-AzRecoveryServicesVault `
        -ResourceGroupName $vaultResourceGroupName `
        -Name $vaultName
  
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
        -Name $privateEndpointConnectionName `
        -PrivateLinkServiceId $vault.ID `
        -GroupId "AzureBackup"  

$vnet = Get-AzVirtualNetwork -Name $vnetName -ResourceGroupName $VMResourceGroupName
$subnet = $vnet | Select -ExpandProperty subnets | Where-Object {$_.Name -eq '<subnetName>'}


$privateEndpoint = New-AzPrivateEndpoint `
        -ResourceGroupName $vmResourceGroupName `
        -Name $privateEndpointName `
        -Location $location `
        -Subnet $subnet `
        -PrivateLinkServiceConnection $privateEndpointConnection `
        -Force
```

### <a name="manual-approval-of-private-endpoints-using-the-azure-resource-manager-client"></a>Manuelle Genehmigung privater Endpunkte mithilfe des Azure Resource Manager-Clients

1. Rufen Sie mit **GetVault** die Verbindungs-ID für Ihren privaten Endpunkt ab.

    ```rest
    armclient GET /subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/vaults/<vaultname>?api-version=2017-07-01-preview
    ```

    Dadurch wird die Verbindungs-ID des privaten Endpunkts zurückgegeben. Der Name der Verbindung kann durch Nutzung des ersten Teils der Verbindungs-ID wie folgt abgerufen werden:

    `privateendpointconnectionid = {peName}.{vaultId}.backup.{guid}`

1. Rufen Sie die **Verbindungs-ID des privaten Endpunkts** (und den **Namen des privaten Endpunkts**, sofern erforderlich) aus der Antwort ab. Ersetzen Sie sie im folgenden JSON-Code und Azure Resource Manager-URI, und versuchen Sie, den Status in „Approved/Rejected/Disconnected“ zu ändern, wie im folgenden Beispiel veranschaulicht:

    ```rest
    armclient PUT /subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>/privateEndpointConnections/<privateendpointconnectionid>?api-version=2020-02-02-preview @C:\<filepath>\BackupAdminApproval.json
    ```

    JSON:

    ```json
    {
    "id": "/subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>/privateEndpointConnections/<privateendpointconnectionid>",
    "properties": {
        "privateEndpoint": {
        "id": "/subscriptions/<subscriptionid>/resourceGroups/<pergname>/providers/Microsoft.Network/privateEndpoints/pename"
        },
        "privateLinkServiceConnectionState": {
        "status": "Disconnected",  //choose state from Approved/Rejected/Disconnected
        "description": "Disconnected by <userid>"
        }
    }
    }
    ```

### <a name="set-up-proxy-server-for-recovery-services-vault-with-private-endpoint"></a>Einrichten eines Proxyservers für den Recovery Services-Tresor mit privatem Endpunkt

Führen Sie die folgenden Schritte aus, um einen Proxyserver für einen virtuellen Azure-Computer oder einen lokalen Computer zu konfigurieren:

1. Fügen Sie die folgenden Domänen hinzu, auf die vom Proxyserver aus zugegriffen werden muss.
   
   | Dienst | Domänennamen | Port |
   | ------- | ------ | ---- |
   | Azure Backup | *.backup.windowsazure.com | 443 |
   | Azure Storage | *.blob.core.windows.net <br><br> *.queue.core.windows.net <br><br> *.blob.storage.azure.net | 443 |
   | Azure Active Directory <br><br> Die in den Abschnitten 56 und 59 in [Microsoft 365 Common und Office Online](/microsoft-365/enterprise/urls-and-ip-address-ranges?view=o365-worldwide&preserve-view=true#microsoft-365-common-and-office-online) erwähnten Domänen-URLs wurden aktualisiert. | *.msftidentity.com, *.msidentity.com, account.activedirectory.windowsazure.com, accounts.accesscontrol.windows.net, adminwebservice.microsoftonline.com, api.passwordreset.microsoftonline.com, autologon.microsoftazuread-sso.com, becws.microsoftonline.com, clientconfig.microsoftonline-p.net, companymanager.microsoftonline.com, device.login.microsoftonline.com, graph.microsoft.com, graph.windows.net, login.microsoft.com, login.microsoftonline.com, login.microsoftonline-p.com, login.windows.net, logincert.microsoftonline.com, loginex.microsoftonline.com, login-us.microsoftonline.com, nexus.microsoftonline-p.com, passwordreset.microsoftonline.com, provisioningapi.microsoftonline.com <br><br> 20.190.128.0/18, 40.126.0.0/18, 2603:1006:2000::/48, 2603:1007:200::/48, 2603:1016:1400::/48, 2603:1017::/48, 2603:1026:3000::/48, 2603:1027:1::/48, 2603:1036:3000::/48, 2603:1037:1::/48, 2603:1046:2000::/48, 2603:1047:1::/48, 2603:1056:2000::/48, 2603:1057:2::/48 <br><br> *.hip.live.com, *.microsoftonline.com, *.microsoftonline-p.com, *.msauth.net, *.msauthimages.net, *.msecnd.net, *.msftauth.net, *.msftauthimages.net, *.phonefactor.net, enterpriseregistration.windows.net, management.azure.com, policykeyservice.dc.ad.msft.net | Wie anwendbar. |

1. Erlauben Sie den Zugriff auf diese Domänen auf dem Proxyserver, und verknüpfen Sie die private DNS-Zone (`*.privatelink.<geo>.backup.windowsazure.com`, `*.privatelink.blob.core.windows.net`, `*.privatelink.queue.core.windows.net`) mit dem virtuellen Netzwerk, in dem der Proxyserver erstellt wird, oder verwenden Sie einen benutzerdefinierten DNS-Server mit den entsprechenden DNS-Einträgen. <br><br> Das virtuelle Netzwerk, in dem der Proxyserver ausgeführt wird, und das virtuelle Netzwerk, in dem die private Endpunkt-NIC erstellt wird, sollten per Peering verbunden werden, sodass der Proxyserver die Anforderungen an die private IP-Adresse umleiten kann. 

   >[!NOTE]
   >Im oben stehenden Text bezieht sich `<geo>` auf den Regionscode (z. B. *eus* und *ne* für „USA, Osten“ bzw. „Europa, Norden“). In den folgenden Listen finden Sie die Regionscodes:
   >
   >- [Alle öffentlichen Clouds](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/AzureRegionCodesList.docx)
   >- [China](/azure/china/resources-developer-guide#check-endpoints-in-azure)
   >- [Deutschland](../germany/germany-developer-guide.md#endpoint-mapping)
   >- [US Gov](../azure-government/documentation-government-developer-guide.md)
   >- [Geocodeliste – Beispiel-XML](scripts/geo-code-list.md)

Das folgende Diagramm zeigt ein Setup (bei Verwendung der privaten Azure-DNS-Zonen) mit einem Proxyserver, dessen virtuelles Netzwerk mit einer privaten DNS-Zone mit erforderlichen DNS-Einträgen verknüpft ist. Der Proxyserver kann auch einen eigenen DNS-Server haben, und die oben genannten Domänen können bedingt an 168.63.129.16 weitergeleitet werden. Wenn Sie eine benutzerdefinierte DNS-Server-/-Hostdatei für die DNS-Auflösung verwenden, lesen Sie die Abschnitte zum [Verwalten von DNS-Einträgen](#manage-dns-records) und [Konfigurieren des Schutzes](#configure-backup).

:::image type="content" source="./media/private-endpoints/setup-with-proxy-server-inline.png" alt-text="Diagramm eines Setups mit einem Proxyserver." lightbox="./media/private-endpoints/setup-with-proxy-server-expanded.png":::

### <a name="create-dns-entries-when-the-dns-serverdns-zone-is-present-in-another-subscription"></a>Erstellen von DNS-Einträgen, wenn der DNS-Server bzw. die DNS-Zone sich in einem anderen Abonnement befindet

In diesem Abschnitt werden die Fälle erläutert, in denen Sie eine DNS-Zone verwenden, die in einem Abonnement vorhanden ist, oder eine Ressourcengruppe, die sich von der Ressourcengruppe unterscheidet, die den privaten Endpunkt für den Recovery Services-Tresor enthält, z. B. eine Hub-and-Spoke-Topologie. Da die zum Erstellen von private Endpunkten (und der DNS-Einträge) verwendete verwaltete Identität nur über Berechtigungen für die Ressourcengruppe verfügt, in der die privaten Endpunkte erstellt werden, werden zusätzlich die erforderlichen DNS-Einträge benötigt. Verwenden Sie die folgenden PowerShell-Skripts, um DNS-Einträge zu erstellen.
  
>[!Note]
>Sehen Sie sich den gesamten unten beschriebenen Prozess an, um die erforderlichen Ergebnisse zu erzielen. Der Prozess muss zweimal wiederholt werden – einmal während der ersten Ermittlung (um die für Kommunikationsspeicherkonten erforderlichen DNS-Einträge zu erstellen) und dann noch einmal während der ersten Sicherung (um die für Back-End-Speicherkonten erforderlichen DNS-Einträge zu erstellen).

#### <a name="step-1-get-required-dns-entries"></a>Schritt 1: Abrufen der erforderlichen DNS-Einträge

Verwenden Sie das Skript [PrivateIP.ps1](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/PrivateIP.ps1), um alle DNS-Einträge aufzulisten, die erstellt werden müssen.

>[!Note]
>In der folgenden Syntax bezieht sich `subscription` auf das Abonnement, in dem der private Endpunkt des Tresors erstellt werden soll.

**Syntax für die Verwendung des Skripts**

```azurepowershell
./PrivateIP.ps1 -Subscription "<VaultPrivateEndpointSubscriptionId>" -VaultPrivateEndpointName "<vaultPrivateEndpointName>" -VaultPrivateEndpointRGName <vaultPrivateEndpointRGName> -DNSRecordListFile dnsentries.txt
```

**Beispielausgabe**

```
ResourceName                                                                 DNS                                                                       PrivateIP
<vaultId>-ab-pod01-fc1         privatelink.eus.backup.windowsazure.com         10.12.0.15
<vaultId>-ab-pod01-fab1        privatelink.eus.backup.windowsazure.com         10.12.0.16
<vaultId>-ab-pod01-prot1       privatelink.eus.backup.windowsazure.com         10.12.0.17
<vaultId>-ab-pod01-rec2        privatelink.eus.backup.windowsazure.com         10.12.0.18
<vaultId>-ab-pod01-ecs1        privatelink.eus.backup.windowsazure.com         10.12.0.19
<vaultId>-ab-pod01-id1         privatelink.eus.backup.windowsazure.com         10.12.0.20
<vaultId>-ab-pod01-tel1        privatelink.eus.backup.windowsazure.com         10.12.0.21
<vaultId>-ab-pod01-wbcm1       privatelink.eus.backup.windowsazure.com         10.12.0.22
abcdeypod01ecs114        privatelink.blob.core.windows.net       10.12.0.23
abcdeypod01ecs114        privatelink.queue.core.windows.net      10.12.0.24
abcdeypod01prot120       privatelink.blob.core.windows.net       10.12.0.28
abcdeypod01prot121       privatelink.blob.core.windows.net       10.12.0.32
abcdepod01prot110       privatelink.blob.core.windows.net       10.12.0.36
abcdeypod01prot121       privatelink.blob.core.windows.net       10.12.0.30
abcdeypod01prot122       privatelink.blob.core.windows.net       10.12.0.34
abcdepod01prot120       privatelink.blob.core.windows.net       10.12.0.26

```

#### <a name="step-2-create--dns-entries"></a>Schritt 2: Erstellen von DNS-Einträgen

Erstellen Sie DNS-Einträge entsprechend den oben genannten Einträgen. Basierend auf dem verwendeten DNS-Typ gibt es zwei Alternativen zum Erstellen von DNS-Einträgen.

**Fall 1**: Wenn Sie einen benutzerdefinierten DNS-Server verwenden, müssen Sie manuell Einträge für jeden Datensatz aus dem obigen Skript erstellen und überprüfen, ob der FQDN (ResourceName.DNS) in eine private IP-Adresse innerhalb des virtuellen Netzwerks aufgelöst wird.

**Fall 2**: Wenn Sie Azure DNS Private Zones verwenden, können Sie das Skript [CreateDNSEntries.ps1](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/CreateDNSEntries.ps1) verwenden, um automatisch DNS-Einträge in der Zone „Privates DNS“ zu erstellen. In der folgenden Syntax ist `subscription` das in der Zone „Privates DNS“ vorhandene Abonnement.

**Syntax für die Verwendung des Skripts**

```azurepowershell
/CreateDNSEntries.ps1 -Subscription <PrivateDNSZoneSubId> -DNSResourceGroup <PrivateDNSZoneRG> -DNSRecordListFile dnsentries.txt
```

#### <a name="summary-of-the-entire-process"></a>Zusammenfassung des gesamten Prozesses

Um den privaten Endpunkt für RSV über diese Problemumgehung ordnungsgemäß einzurichten, müssen Sie folgende Schritte ausführen:

1. Erstellen Sie einen privaten Endpunkt für den Tresor (wie weiter oben in diesem Artikel beschrieben).
1. Lösen Sie die Ermittlung aus. Die Ermittlung für SQL/HANA schlägt mit _UserErrorVMInternetConnectivityIssue_ fehl, da DNS-Einträge für das Kommunikationsspeicherkonto fehlen.
1. Führen Sie die Skripts aus, um DNS-Einträge abzurufen und entsprechende DNS-Einträge für das zuvor in diesem Abschnitt erwähnte Kommunikationsspeicherkonto zu erstellen.
1. Lösen Sie die Ermittlung erneut aus. Dieses Mal sollte die Ermittlung gelingen.
1. Lösen Sie die Sicherung aus. Die Sicherung für SQL/HANA und MARS kann fehlschlagen, da DNS-Einträge für Back-End-Speicherkonten fehlen, wie weiter oben in diesem Abschnitt erwähnt.
1. Führen Sie die Skripts aus, um DNS-Einträge für das Back-End-Speicherkonto zu erstellen.
1. Lösen Sie die Sicherung erneut aus. Dieses Mal sollten die Sicherungen gelingen.

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

### <a name="can-i-create-a-private-endpoint-for-an-existing-backup-vaultbr"></a>Kann ich einen privaten Endpunkt für einen vorhandenen Azure Backup-Tresor erstellen?<br>

Nein, private Endpunkte können nur für neue Azure Backup-Tresore erstellt werden. Daher durfte der Tresor zu keiner Zeit Elemente enthalten, die durch ihn geschützt wurden. Vor der Einrichtung privater Endpunkte können keine Versuche erfolgen, Elemente im Tresor zu schützen.

### <a name="i-tried-to-protect-an-item-to-my-vault-but-it-failed-and-the-vault-still-doesnt-contain-any-items-protected-to-it-can-i-create-private-endpoints-for-this-vaultbr"></a>Ich habe versucht, ein Element in meinem Tresor zu schützen, was aber nicht gelang, und der Tresor enthält immer noch keine geschützten Elemente. Kann ich private Endpunkte für diesen Tresor erstellen?<br>

Nein, mit dem Tresor darf in der Vergangenheit nicht versucht worden sein, Elemente darin zu schützen.

### <a name="i-have-a-vault-thats-using-private-endpoints-for-backup-and-restore-can-i-later-add-or-remove-private-endpoints-for-this-vault-even-if-i-have-backup-items-protected-to-itbr"></a>Ich habe einen Tresor, der private Endpunkte für Sicherung und Wiederherstellung nutzt. Kann ich später private Endpunkte für diesen Tresor hinzufügen oder entfernen, auch wenn ich Sicherungselemente darin geschützt habe?<br>

Ja. Wenn Sie bereits private Endpunkte für einen Tresor und geschützte Sicherungselemente darin erstellt haben, können Sie später je nach Bedarf private Endpunkte hinzufügen oder entfernen.

### <a name="can-the-private-endpoint-for-azure-backup-also-be-used-for-azure-site-recoverybr"></a>Kann der private Endpunkt für Azure Backup auch für Azure Site Recovery genutzt werden?<br>

Nein, der private Endpunkt für Backup kann nur für Azure Backup genutzt werden. Sie müssen einen neuen privaten Endpunkt für Azure Site Recovery erstellen, sofern vom Dienst unterstützt.

### <a name="i-missed-one-of-the-steps-in-this-article-and-went-on-to-protect-my-data-source-can-i-still-use-private-endpointsbr"></a>Ich habe einen der Schritte in diesem Artikel versäumt und das Schützen meiner Datenquelle fortgesetzt. Kann ich weiterhin private Endpunkte verwenden?<br>

Wenn Sie die Schritte im Artikel nicht befolgen und Elemente weiterhin schützen, kann dies dazu führen, dass der Tresor private Endpunkte nicht nutzen kann. Es wird daher empfohlen, sich auf diese Checkliste zu beziehen, bevor Sie mit dem Schützen von Elementen fortfahren.

### <a name="can-i-use-my-own-dns-server-instead-of-using-the-azure-private-dns-zone-or-an-integrated-private-dns-zonebr"></a>Kann ich meinen eigenen DNS-Server verwenden, anstatt die private DNS-Zone in Azure oder eine integrierte private DNS-Zone zu nutzen?<br>

Ja, Sie können Ihre eigenen DNS-Server verwenden. Stellen Sie jedoch sicher, dass alle erforderlichen DNS-Einträge wie in diesem Abschnitt beschrieben hinzugefügt werden.

### <a name="do-i-need-to-perform-any-additional-steps-on-my-server-after-ive-followed-the-process-in-this-articlebr"></a>Muss ich zusätzliche Schritte auf meinem Server durchführen, nachdem ich den in diesem Artikel beschriebenen Prozess befolgt habe?<br>

Nachdem Sie den in diesem Artikel beschriebenen Prozess befolgt haben, müssen Sie keine weiteren Schritte unternehmen, um private Endpunkte für Sicherung und Wiederherstellung zu nutzen.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über die [Sicherheitsfeatures von Azure Backup](security-overview.md).
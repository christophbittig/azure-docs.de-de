---
title: Aktivieren der Replikation für lokale Computer mit privaten Endpunkten
description: In diesem Artikel wird beschrieben, wie Sie die Replikation für lokale Computer mit privaten Endpunkten mithilfe von Site Recovery konfigurieren.
author: Harsha-CS
ms.author: harshacs
ms.service: site-recovery
ms.topic: article
ms.date: 07/14/2020
ms.openlocfilehash: 939eb89de2e347aeec2d8946f456f5ba2aa6be29
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/07/2021
ms.locfileid: "129658574"
---
# <a name="replicate-on-premises-machines-by-using-private-endpoints"></a>Replizieren von lokalen Computern mit privaten Endpunkten

Mit Azure Site Recovery können Sie private [Azure Private Link-Endpunkte](../private-link/private-endpoint-overview.md) für die Replikation Ihrer lokalen Computer in virtuelle Azure-Netzwerke verwenden. Der Zugriff auf einen Wiederherstellungstresor über private Endpunkte wird in allen kommerziellen Azure-Regionen und Azure Government-Regionen unterstützt.

In diesem Artikel wird beschrieben, wie Sie die folgenden Schritte ausführen:

- Erstellen eines Azure Backup Recovery Services-Tresors, um Ihre Computer zu schützen
- Aktivieren Sie eine verwaltete Identität für den Tresor. Gewähren Sie die für den Zugriff auf Speicherkonten erforderlichen Berechtigungen, um die Replikation des Datenverkehrs von lokalen zu Azure-Zielen zu aktivieren. Für Private Link-Zugriff auf den Tresor ist Speicherzugriff über eine verwaltete Identität erforderlich.

- Nehmen Sie die für private Endpunkte erforderlichen DNS-Änderungen vor.
- Erstellen und genehmigen Sie private Endpunkte für einen Tresor innerhalb eines virtuellen Netzwerks.
- Erstellen privater Endpunkte für Speicherkonten: Sie können nach Bedarf weiterhin öffentlichen Zugriff oder Firewallzugriff auf den Speicher zulassen. Das Erstellen eines privaten Endpunkts für den Speicherzugriff ist für Azure Site Recovery nicht erforderlich.
  
Auf der folgenden Abbildung sehen Sie den Replikationsworkflow für eine hybride Notfallwiederherstellung mit privaten Endpunkten. Private Endpunkte können nicht in Ihrem lokalen Netzwerk erstellt werden. Damit Sie private Links verwenden können, müssen Sie ein virtuelles Azure-Netzwerk (in diesem Artikel als *Umgehungsnetzwerk* bezeichnet) erstellen, private Konnektivität zwischen dem lokalen Netzwerk und dem Umgehungsnetzwerk einrichten und dann private Endpunkte im Umgehungsnetzwerk erstellen. Sie können eine beliebige Form privater Konnektivität wählen.

:::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/architecture.png" alt-text="Abbildung der Architektur für Azure Site Recovery und private Endpunkte":::

## <a name="prerequisites-and-caveats"></a>Voraussetzungen und Einschränkungen

- Private Links werden ab Site Recovery 9.35 unterstützt.
- Private Endpunkte können nur für neue Recovery Services-Tresore erstellt werden, bei denen keine Elemente im Tresor registriert sind. Daher müssen private Endpunkte erstellt werden, bevor dem Tresor Elemente hinzugefügt werden. Preisinformationen finden Sie unter [Azure Private Link – Preise](https://azure.microsoft.com/pricing/details/private-link/).
- Sobald ein privater Endpunkt für einen Tresor erstellt ist, wird der Tresor gesperrt. Der Zugriff ist nur über Netzwerke möglich, die über private Endpunkte verfügen.
- Azure Active Directory unterstützt derzeit keine privaten Endpunkte. Daher müssen Sie den ausgehenden Zugriff vom gesicherten virtuellen Azure-Netzwerk auf IP-Adressen und vollqualifizierte Domänennamen zulassen, die erforderlich sind, damit Azure Active Directory in einer Region verwendet werden kann.
  Bei Bedarf können Sie das Netzwerksicherheitsgruppentag „Azure Active Directory“ und die Azure Firewall-Tags verwenden, um den Zugriff auf Azure Active Directory zuzulassen.
- In dem Umgehungsnetzwerk, in dem Ihr privater Endpunkt erstellt wird, sind fünf IP-Adressen erforderlich. Wenn Sie einen privaten Endpunkt für den Tresor erstellen, erstellt Site Recovery fünf private Links für den Zugriff auf die zugehörigen Microservices.
- Für die Konnektivität privater Endpunkte mit einem Cachespeicherkonto ist eine zusätzliche IP-Adresse im Umgehungsnetzwerk erforderlich. Sie können zwischen einem lokalen und Ihrem Speicherkontoendpunkt eine beliebige Konnektivitätsmethode verwenden. Sie können beispielsweise das Internet oder Microsoft Azure [ExpressRoute](../expressroute/index.yml) verwenden. Das Einrichten eines privaten Links ist optional. Sie können private Endpunkte als Speicher nur für Speicherkonten des Typs „Universell v2“ erstellen. Unter [Azure-Seitenblobs: Preise](https://azure.microsoft.com/pricing/details/storage/page-blobs/) finden Sie Informationen zu den Preisen für die Datenübertragung für Speicherkonten des Typs „Universell v2“.

> [!NOTE]
> Beim Einrichten privater Endpunkte zum Schutz von VMware und physischen Computern müssen Sie MySQL manuell auf dem Konfigurationsserver installieren. Führen Sie die [hier](vmware-azure-deploy-configuration-server.md#configure-settings) beschriebenen Schritte aus, um die manuelle Installation durchzuführen. 

 ## <a name="create-and-use-private-endpoints-for-site-recovery"></a>Erstellen und Verwenden privater Endpunkte für Site Recovery

 In den folgenden Abschnitten werden die Schritte beschrieben, die Sie durchführen müssen, um private Endpunkte für Site Recovery in Ihren virtuellen Netzwerken zu erstellen und zu verwenden.

> [!NOTE]
> Sie sollten diese Schritte in der hier angezeigten Reihenfolge ausführen. Tun Sie dies nicht, können Sie private Endpunkte im Tresor möglicherweise nicht verwenden, und möglicherweise müssen Sie den Prozess mit einem neuen Tresor neu starten.

### <a name="create-a-recovery-services-vault"></a>Erstellen eines Recovery Services-Tresors

Ein Recovery Services-Tresor enthält die Replikationsinformationen des Computers. Er wird verwendet, um Site Recovery-Vorgänge auszulösen. Weitere Informationen zum Erstellen eines Recovery Services-Tresors in der Azure-Region, in der Sie im Falle eines Notfalls ein Failover ausführen möchten, finden Sie unter [Erstellen eines Recovery Services-Tresors](./azure-to-azure-tutorial-enable-replication.md#create-a-recovery-services-vault).

### <a name="enable-the-managed-identity-for-the-vault"></a>Aktivieren der verwalteten Identität für den Tresor

Eine [verwaltete Identität](../active-directory/managed-identities-azure-resources/overview.md) ermöglicht es dem Tresor, auf Ihre Speicherkonten zuzugreifen. Je nach Szenario benötigt Site Recovery möglicherweise Zugriff auf das Zielspeicherkonto und die Cache- und Protokollspeicherkonten. Der Zugriff auf verwaltete Identitäten wird benötigt, wenn Sie den Private Link-Dienst für den Tresor verwenden.

1. Navigieren Sie zu Ihrem Recovery Services-Tresor. Klicken Sie unter **Einstellungen** auf **Identität**:

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/enable-managed-identity-in-vault.png" alt-text="Screenshot: Seite „Identitätseinstellungen“":::

1. Ändern Sie den **Status** in **Ein**, und klicken Sie auf **Speichern**.

   Eine Objekt-ID wird erzeugt. Der Tresor ist jetzt bei Azure Active Directory registriert.

### <a name="create-private-endpoints-for-the-recovery-services-vault"></a>Erstellen privater Endpunkte für den Recovery Services-Tresor

Sie benötigen einen privaten Endpunkt für den Tresor im Umgehungsnetzwerk, der zum Schutz der Computer im lokalen Quellnetzwerk dient. Erstellen Sie den privaten Endpunkt über das Private Link-Center im Azure-Portal oder über [Azure PowerShell](../private-link/create-private-endpoint-powershell.md).

1. Suchen Sie über die Suchleiste des Azure-Portals nach „private link“. Klicken Sie auf **Private Link**, um zum Private Link-Center zu navigieren:

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/search-private-links.png" alt-text="Screenshot der Suche im Azure-Portal nach dem Private Link-Center":::

1. Klicken Sie im linken Bereich auf **Private Endpunkte**. Sobald Sie sich auf der Seite **Private Endpunkte** befinden, klicken Sie auf **Hinzufügen**, um mit der Erstellung eines privaten Endpunkts für Ihren Tresor zu beginnen:

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints.png" alt-text="Screenshot der Erstellung eines privaten Endpunkts im Private Link-Center":::

1. Geben Sie auf der Seite **Privaten Endpunkt erstellen** die Details zum Erstellen Ihrer privaten Endpunktverbindung an.

   1. **Grundeinstellungen:** Hier geben Sie die grundlegenden Details für Ihre privaten Endpunkte an. Verwenden Sie die Region, die Sie für Ihr Umgehungsnetzwerk verwendet haben:

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints-basic-tab.png" alt-text="Screenshot der Registerkarte „Grundeinstellungen“ für das Erstellen eines privaten Endpunkts":::

   1. **Ressource:** Auf dieser Registerkarte müssen Sie die PaaS-Ressource (Platform-as-a-Service) angeben, für die Sie eine Verbindung herstellen möchten. Wählen Sie unter **Ressourcentyp** für Ihr ausgewähltes Abonnement **Microsoft.RecoveryServices/vaults** aus. Wählen Sie unter **Ressource** den Namen Ihres Recovery Services-Tresor aus. Klicken Sie für **Untergeordnete Zielressource** auf **Azure Site Recovery**.

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints-resource-tab.png" alt-text="Screenshot der Registerkarte „Ressource“ für das Herstellen von Verknüpfungen zu einem privaten Endpunkt":::

   1. **Konfiguration**. Geben Sie auf dieser Registerkarte das Umgehungsnetzwerk und das Subnetz an, in dem der private Endpunkt erstellt werden soll. 

      Aktivieren Sie die Integration mit einer privaten DNS-Zone, indem Sie auf **Ja** klicken.
      Wählen Sie eine vorhandene DNS-Zone aus, oder erstellen Sie eine neue. Wenn Sie auf **Ja** klicken, wird die Zone automatisch mit dem Umgehungsnetzwerk verknüpft. Diese Aktion fügt außerdem die DNS-Einträge für die DNS-Auflösung der neuen IP-Adressen und vollqualifizierte Domänennamen hinzu, die für den privaten Endpunkt erstellt wurden.

      Stellen Sie sicher, dass Sie für jeden neuen privaten Endpunkt, der eine Verbindung mit dem gleichen Tresor herstellt, eine neue DNS-Zone erstellen. Wenn Sie eine vorhandene private DNS-Zone auswählen, werden die vorherigen CNAME-Einträge überschrieben. Lesen Sie die Informationen unter [Eigenschaften eines privaten Endpunkts](../private-link/private-endpoint-overview.md#private-endpoint-properties), bevor Sie fortfahren.

      Wenn Ihre Umgebung auf einem Hub-Spoke-Modell basiert, benötigen Sie nur einen privaten Endpunkt und eine private DNS-Zone für das gesamte Setup. Dies liegt daran, dass das Peering für alle virtuellen Netzwerke bereits aktiviert ist. Weitere Informationen finden Sie unter [DNS-Konfiguration für private Azure-Endpunkte](../private-link/private-endpoint-dns.md#virtual-network-workloads-without-custom-dns-server).

      Befolgen Sie die Schritte unter [Erstellen privater DNS-Zonen und manuelles Hinzufügen von DNS-Einträgen](#create-private-dns-zones-and-add-dns-records-manually), um die private DNS-Zone manuell zu erstellen.

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints-configuration-tab.png" alt-text="Screenshot der Registerkarte „Konfiguration“ für die Konfiguration eines privaten Endpunkts":::

   1. **Tags**. Optional können Sie Tags für Ihren privaten Endpunkt hinzufügen.

   1. **Überprüfen \+ erstellen:** Klicken Sie nach Abschluss der Validierung auf **Erstellen**, um den privaten Endpunkt zu erstellen.

Nachdem der private Endpunkt erstellt wurde, werden dem privaten Endpunkt fünf vollqualifizierte Domänennamen (FQDNs) hinzugefügt. Diese Links ermöglichen den Computern im lokalen Netzwerk über das Umgehungsnetzwerk den Zugriff auf alle erforderlichen Site Recovery-Microservices im Kontext des Tresors. Der gleiche private Endpunkt kann für den Schutz aller Azure-Computer im Umgehungsnetzwerk und in allen Netzwerken mit Peering verwendet werden.

Die fünf Domänennamen werden nach folgendem Muster formatiert:

`{Vault-ID}-asr-pod01-{type}-.{target-geo-code}.siterecovery.windowsazure.com`

### <a name="approve-private-endpoints-for-site-recovery"></a>Genehmigen privater Endpunkte für Site Recovery

Wenn Sie den privaten Endpunkt erstellt und auch Besitzer des Recovery Services-Tresors sind, wird der zuvor erstellte private Endpunkt innerhalb weniger Minuten automatisch genehmigt. Andernfalls muss der Besitzer des Tresors den privaten Endpunkt genehmigen, bevor Sie diesen nutzen können. Navigieren Sie auf der Seite des Wiederherstellungstresors unter **Einstellungen** zu **Verbindungen mit privatem Endpunkt**, um eine angeforderte private Endpunktverbindung zu genehmigen oder zu verweigern.

Sie können zur privaten Endpunktressource wechseln, um den Status der Verbindung zu überprüfen, bevor Sie fortfahren:

:::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/vault-private-endpoint-connections.png" alt-text="Screenshot der Seite „Verbindungen mit privatem Endpunkt“ des Tresors und Verbindungsliste":::

### <a name="optional-create-private-endpoints-for-the-cache-storage-account"></a><a name="create-private-endpoints-for-the-cache-storage-account"></a>(Optional) Erstellen privater Endpunkte für das Cachespeicherkonto

Es kann ein privater Endpunkt für Azure Storage verwendet werden. Das Erstellen privater Endpunkte für den Speicherzugriff ist für die Azure Site Recovery-Replikation optional. Wenn Sie einen privaten Speicherendpunkt erstellen, benötigen Sie einen privaten Endpunkt für das Cache- oder Protokollspeicherkonto in Ihrem virtuellen Umgehungsnetzwerk.

> [!NOTE]
> Auch wenn keine privaten Endpunkte im Speicherkonto aktiviert sind, wäre der Schutz erfolgreich. Der Replikationsdatenverkehr würde jedoch über das Internet zu den öffentlichen Endpunkten von Azure Site Recovery geleitet. Um sicherzustellen, dass der Replikationsdatenverkehr über private Links fließt, müssen private Endpunkte für das Speicherkonto aktiviert sein.

> [!NOTE]
> Private Speicherendpunkte können nur in Speicherkonten des Typs „Universell v2“ erstellt werden. Informationen zu den Preisen finden Sie unter [Azure-Seitenblobs: Preise](https://azure.microsoft.com/pricing/details/storage/page-blobs/).

Befolgen Sie die Schritte unter [Erstellen Ihres privaten Endpunkts](../private-link/tutorial-private-endpoint-storage-portal.md#create-storage-account-with-a-private-endpoint), um ein Speicherkonto mit einem privaten Endpunkt zu erstellen. Klicken Sie unter **In private DNS-Zone integrieren** auf **Ja**. Wählen Sie eine vorhandene DNS-Zone aus, oder erstellen Sie eine neue.

### <a name="grant-required-permissions-to-the-vault"></a>Erteilen der erforderlichen Berechtigungen für den Tresor

Je nach Setup benötigen Sie ein oder mehrere Speicherkonten in der Azure-Zielregion. Erteilen Sie der verwalteten Identität als Nächstes die Berechtigungen für alle Cache- und Protokollspeicherkonten, die für Site Recovery erforderlich sind. In diesem Fall müssen Sie die erforderlichen Speicherkonten im Voraus erstellen.

Bevor Sie die Replikation virtueller Computer aktivieren, benötigt die verwaltete Identität des Tresors je nach Art des Speicherkontos folgende Rollenberechtigungen.

- Resource Manager-basierte Speicherkonten (Standard):
  - [Mitwirkender](../role-based-access-control/built-in-roles.md#contributor)
  - [Mitwirkender an Speicherblobdaten](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)
- Resource Manager-basierte Speicherkonten (Premium):
  - [Mitwirkender](../role-based-access-control/built-in-roles.md#contributor)
  - [Besitzer von Speicherblobdaten](../role-based-access-control/built-in-roles.md#storage-blob-data-owner)
- Klassische Speicherkonten:
  - [Mitwirkender von klassischem Speicherkonto](../role-based-access-control/built-in-roles.md#classic-storage-account-contributor)
  - [Klassische Dienstrolle „Speicherkonto-Schlüsseloperator“](../role-based-access-control/built-in-roles.md#classic-storage-account-key-operator-service-role)

Diese Schritte beschreiben, wie Sie Ihrem Speicherkonto eine Rollenzuweisung hinzufügen:

1. Navigieren Sie zum Speicherkonto. Klicken Sie im linken Bereich auf **Zugriffssteuerung (IAM)** .

1. Klicken Sie im Bereich **Rollenzuweisung hinzufügen** auf **Hinzufügen**:

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/storage-role-assignment.png" alt-text="Screenshot der Seite „Zugriffssteuerung (IAM)“ für ein Speicherkonto":::

1. Wählen Sie auf der Seite **Rollenzuweisung hinzufügen** in der Liste **Rolle** die Rolle aus der Liste zu Beginn dieses Abschnitts aus. Geben Sie den Namen des Tresors ein, und klicken Sie dann auf **Speichern**.

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/storage-role-assignment-select-role.png" alt-text="Screenshot der Seite „Rollenzuweisung hinzufügen“":::

Nach Hinzufügen dieser Berechtigungen müssen Sie den Zugriff auf vertrauenswürdige Microsoft-Dienste zulassen. Navigieren Sie zu **Firewalls und virtuelle Netzwerke**, und klicken Sie unter **Ausnahmen** auf **Vertrauenswürdigen Microsoft-Diensten den Zugriff auf dieses Speicherkonto erlauben**.

### <a name="protect-your-virtual-machines"></a>Schützen Ihrer virtuellen Computer

Nach Abschluss der vorherigen Aufgaben fahren Sie mit der Einrichtung Ihrer lokalen Infrastruktur fort. Fahren Sie fort, indem Sie eine der folgenden Aufgaben erledigen: 

- [Bereitstellen eines Konfigurationsservers](./vmware-azure-deploy-configuration-server.md)
- [Einrichten der Quellumgebung](./hyper-v-azure-tutorial.md#set-up-the-source-environment)

Nach Abschluss der Einrichtung können Sie die Replikation für Ihre Quellcomputer aktivieren. Richten Sie die Infrastruktur erst ein, wenn die privaten Endpunkte für den Tresor im Umgehungsnetzwerk erstellt wurden.

### <a name="create-private-dns-zones-and-add-dns-records-manually"></a>Erstellen privater DNS-Zonen und manuelles Hinzufügen von DNS-Einträgen

Wenn Sie die Option für die Integration mit privaten DNS-Zonen nicht bei der Erstellung des privaten Endpunkts für den Tresor ausgewählt haben, befolgen Sie die Schritte in diesem Abschnitt.

Erstellen Sie eine private DNS-Zone, damit der Site Recovery-Anbieter (für Hyper-V-Computer) oder der Prozessserver (für VMware- und physische Computer) vollqualifizierte Domänennamen für private Links in private IP-Adressen auflösen kann.

1. Erstellen Sie eine private DNS-Zone.

   1. Suchen Sie in der Suchleiste **Alle Dienste** nach „Private DNS-Zone“, und wählen Sie aus den Ergebnissen **Private DNS-Zone** aus:

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/search-private-dns-zone.png" alt-text="Screenshot: Suche nach „Private DNS-Zone“ auf der Seite „Neue Ressourcen“ im Azure-Portal":::

   1. Klicken Sie auf der Seite **Private DNS-Zonen** auf die Schaltfläche **Hinzufügen**, um mit dem Erstellen einer neuen Zone zu beginnen.

   1. Geben Sie auf der Seite **Private DNS-Zone erstellen** die erforderlichen Details ein. Geben Sie als Name für die private DNS-Zone **privatelink.siterecovery.windowsazure.com** ein. Sie können eine beliebige Ressourcengruppe und ein beliebiges Abonnement auswählen.

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-dns-zone.png" alt-text="Screenshot der Registerkarte „Grundeinstellungen“ der Seite „Private DNS-Zone erstellen“":::

   1. Überprüfen und erstellen Sie die DNS-Zone anschließend über die Registerkarte **Überprüfen \+ erstellen**.

1. Verknüpfen Sie die private DNS-Zone mit Ihrem virtuellen Netzwerk.

   Sie müssen die private DNS-Zone, die Sie erstellt haben, mit dem Umgehungsnetzwerk verknüpfen.

   1. Wechseln Sie zu der privaten DNS-Zone, die Sie im vorherigen Schritt erstellt haben, und navigieren Sie dann auf der linken Seite zu **VNET-Verknüpfungen**. Wählen Sie **Hinzufügen**.

   1. Geben Sie die erforderlichen Details ein. Wählen Sie in den Listen **Abonnement** und **Virtuelles Netzwerk** die Details aus, die dem Umgehungsnetzwerk entsprechen. Behalten Sie für die übrigen Felder den Standardwert bei.

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/add-virtual-network-link.png" alt-text="Screenshot der Seite „VNET-Verknüpfung hinzufügen“":::

1. Fügen Sie DNS-Einträge hinzu.

   Sobald Sie die erforderliche private DNS-Zone und den privaten Endpunkt erstellt haben, müssen Sie Ihrer DNS-Zone DNS-Einträge hinzufügen.

   > [!NOTE]
   > Wenn Sie eine benutzerdefinierte private DNS-Zone verwenden, sollten Sie dafür sorgen, ähnliche Einträge zu erstellen. Sehen Sie sich dazu den folgenden Schritt an.

   In diesem Schritt müssen Sie Einträge für die einzelnen FQDNs in Ihrem privaten Endpunkt in Ihrer privaten DNS-Zone vornehmen.

   1. Wechseln Sie zu Ihrer privaten DNS-Zone, und navigieren Sie im linken Bereich zu **Übersicht**. Klicken Sie dort auf **Datensatzgruppe**, um mit dem Hinzufügen von Einträgen zu beginnen.

   1. Fügen Sie auf der Seite **Datensatzgruppe hinzufügen** für jeden vollqualifizierten Domänennamen und jede private IP-Adresse einen **A**-Eintrag hinzu. Die Liste der vollqualifizierten Domänennamen und IP-Adressen finden Sie auf der Seite **Privater Endpunkt** unter **Übersicht**. Wie im nachstehenden Screenshot veranschaulicht wird, wird der erste vollqualifizierte Domänenname aus dem privaten Endpunkt der in der privaten DNS-Zone festgelegten Datensatzgruppe hinzugefügt.

      Diese vollqualifizierten Domänennamen entsprechen folgendem Muster: `{Vault-ID}-asr-pod01-{type}-.{target-geo-code}.siterecovery.windowsazure.com`

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/add-record-set.png" alt-text="Screenshot der Seite „Datensatzgruppe hinzufügen“":::

## <a name="next-steps"></a>Nächste Schritte

Sie haben Ihre privaten Endpunkte jetzt erfolgreich für die Replikation virtueller Computer aktiviert. Weitere Informationen finden Sie in den folgenden Artikeln:

- [Bereitstellen eines Konfigurationsservers](./vmware-azure-deploy-configuration-server.md)
- [Einrichten der Notfallwiederherstellung von lokalen Hyper-V-VMs in Azure](./hyper-v-azure-tutorial.md)

---
title: Verwenden privater Endpunkte für den sicheren Zugriff auf Azure Purview
description: In diesem Artikel wird beschrieben, wie Sie einen privaten Endpunkt für Ihr Azure Purview-Konto einrichten.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 06/11/2021
ms.openlocfilehash: 8a5fb386cfbf8c31abc92d75d49854ca237d4978
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122338844"
---
# <a name="use-private-endpoints-for-your-azure-purview-account"></a>Verwenden privater Endpunkte für Ihr Azure Purview-Konto

Sie können private Endpunkte für Ihre Azure Purview-Konten verwenden, um Clients und Benutzern in einem virtuellen Netzwerk (VNet) den sicheren Zugriff auf den Katalog über Azure Private Link zu ermöglichen. Der private Endpunkt verwendet eine IP-Adresse aus dem Adressraum des virtuellen Netzwerks für Ihr Azure Purview-Konto. Der Netzwerkdatenverkehr zwischen den Clients im virtuellen Netzwerk und dem Azure Purview-Konto wird über das virtuelle Netzwerk und eine private Verbindung im Microsoft-Backbonenetzwerk geleitet. Dadurch wird er vom öffentlichen Internet isoliert.

   :::image type="content" source="media/catalog-private-link/purview-private-link-architecture.png" alt-text="Abbildung der Azure Purview- und Private Link-Architektur.":::

Sehen Sie sich die [häufig gestellten Fragen (FAQ) zu Private Link für Azure Purview](./catalog-private-link-faqs.md) an.

## <a name="create-an-azure-purview-account-with-a-private-endpoint"></a>Erstellen eines Azure Purview-Kontos mit einem privaten Endpunkt

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com) und dort zu Ihrem Azure Purview-Konto.

1. Geben Sie die grundlegenden Informationen ein, und legen Sie auf der Registerkarte **Netzwerk** als Konnektivitätsmethode **Privater Endpunkt** fest. Richten Sie Ihre privaten Endpunkte für die Erfassung ein, indem Sie Details für die Elemente **Abonnement**, **Virtuelles Netzwerk** und **Subnetz** eingeben, die mit Ihrem privaten Endpunkt verbunden werden sollen.

   > [!NOTE]
   > Sie sollten nur dann einen privaten Endpunkt für die Erfassung erstellen, wenn Sie die Netzwerkisolation für End-to-End-Überprüfungsszenarios sowohl für Azure- als auch für lokale Quellen aktivieren möchten. Private Erfassungsendpunkte für AWS-Quellen werden derzeit nicht unterstützt.

   :::image type="content" source="media/catalog-private-link/create-pe-azure-portal.png" alt-text="Screenshot: Erstellen eines privaten Endpunkts im Azure-Portal.":::

1. Optional können Sie für jeden privaten Erfassungsendpunkt eine **private DNS-Zone** einrichten.

1. Wählen Sie **+ Hinzufügen** aus, um Ihrem Azure Purview-Konto einen privaten Endpunkt hinzuzufügen.

1. Wählen Sie auf der Seite **Privaten Endpunkt erstellen** für **Purview-Unterressource** die Option **Konto** aus. Wählen Sie Ihr virtuelles Netzwerk und Subnetz aus, und wählen Sie die private DNS-Zone aus, in der das DNS registriert wird. Sie können auch Ihre eigenen DNS-Server verwenden oder DNS-Einträge mithilfe von Hostdateien auf Ihren virtuellen Computern erstellen.

    :::image type="content" source="media/catalog-private-link/create-pe-account.png" alt-text="Screenshot: Auswahl auf der Seite „Privaten Endpunkt erstellen“.":::

1. Klicken Sie auf **OK**.

1. Klicken Sie auf **Überprüfen + erstellen**. Auf der Seite **Überprüfen + erstellen** überprüft Azure Ihre Konfiguration.

1. Wenn die Meldung „Überprüfung erfolgreich“ angezeigt wird, wählen Sie **Erstellen** aus.

    :::image type="content" source="media/catalog-private-link/validation-passed.png" alt-text="Screenshot, der zeigt, dass die Überprüfung für die Kontoerstellung erfolgreich war.":::

## <a name="create-a-private-endpoint-for-the-azure-purview-web-portal"></a>Erstellen eines privaten Endpunkts für das Azure Purview-Webportal

1. Navigieren Sie zum erstellten Azure Purview-Konto, und wählen Sie unter **Einstellungen** die Optionen **Netzwerk** > **Private Endpunktverbindungen** aus.

1. Wählen Sie **+ Privater Endpunkt** aus, um einen neuen privaten Endpunkt zu erstellen.

    :::image type="content" source="media/catalog-private-link/pe-portal.png" alt-text="Screenshot: Erstellen eines privaten Endpunkts im Portal.":::

1. Geben Sie die grundlegenden Informationen an.

1. Wählen Sie auf der Registerkarte **Ressource** für **Ressourcentyp** die Option **Microsoft.Purview/accounts** aus.

1. Wählen Sie für **Ressource** das neu erstellte Azure Purview-Konto aus. Wählen Sie als **Zielunterressource** die Option **Portal** aus.
    :::image type="content" source="media/catalog-private-link/pe-portal-details.png" alt-text="Screenshot: Details zum privaten Endpunkt des Portals.":::

1. Wählen Sie auf der Registerkarte **Konfiguration** das virtuelle Netzwerk und eine private DNS-Zone aus. Wechseln Sie zur Zusammenfassungsseite, und wählen Sie **Erstellen** aus, um den privaten Endpunkt des Portals zu erstellen.

## <a name="private-dns-zone-requirements-for-private-endpoints"></a>Anforderungen an private DNS-Zonen für private Endpunkte

Wenn Sie einen privaten Endpunkt erstellen, wird der DNS-CNAME-Ressourceneintrag für Azure Purview auf einen Alias in einer Unterdomäne mit dem Präfix `privatelink` aktualisiert. Standardmäßig erstellen wir außerdem eine [private DNS-Zone](../dns/private-dns-overview.md), die der Unterdomäne `privatelink` entspricht, mit den DNS-A-Ressourceneinträgen für die privaten Endpunkte.

Wenn Sie die Azure Purview-Endpunkt-URL von einem Punkt außerhalb des virtuellen Netzwerks mit dem privaten Endpunkt auflösen, wird diese in den öffentlichen Endpunkt von Azure Purview aufgelöst. Bei Auflösung aus dem virtuellen Netzwerk, das den privaten Endpunkt hostet, wird die Azure Purview-Endpunkt-URL in die IP-Adresse des privaten Endpunkts aufgelöst.

Beispiel: Wenn der Name eines Azure Purview-Kontos „PurviewA“ lautet, wird er von außerhalb des virtuellen Netzwerks aufgelöst, das den privaten Endpunkt hostet:

| Name | type | Wert |
| ---------- | -------- | --------------- |
| `PurviewA.purview.azure.com` | CNAME | `PurviewA.privatelink.purview.azure.com` |
| `PurviewA.privatelink.purview.azure.com` | CNAME | \<Purview public endpoint\> |
| \<Purview public endpoint\> | Ein | \<Purview public IP address\> |
| `Web.purview.azure.com` | CNAME | \<Purview public endpoint\> |

Die DNS-Ressourceneinträge für „PurviewA“ lauten nach dem Auflösen im virtuellen Netzwerk, das den privaten Endpunkt hostet, wie folgt:

| Name | type | Wert |
| ---------- | -------- | --------------- |
| `PurviewA.purview.azure.com` | CNAME | `PurviewA.privatelink.purview.azure.com` |
| `PurviewA.privatelink.purview.azure.com` | Ein | \<private endpoint IP address\> |
| `Web.purview.azure.com` | CNAME | \<private endpoint IP address\> |

<br>

 > [!Important]
 > Wenn Sie keine DNS-Weiterleitungen verwenden und A-Einträge stattdessen direkt auf Ihren lokalen DNS-Servern verwalten, um die Endpunkte über ihre privaten IP-Adressen aufzulösen, müssen Sie möglicherweise weitere A-Einträge auf Ihren DNS-Servern erstellen.

| Name | type | Wert |
| ---------- | -------- | --------------- |
| `PurviewA.Purview.azure.com` | Ein | \<account private endpoint IP address of Azure Purview> |
| `PurviewA.scan.Purview.azure.com` | Ein | \<account private endpoint IP address of Azure Purview> |
| `PurviewA.catalog.Purview.azure.com` | Ein | \<account private endpoint IP address of Azure Purview\> |
| `PurviewA.proxy.purview.azure.com` | Ein | \<account private endpoint IP address of Azure Purview\> |
| `PurviewA.guardian.purview.azure.com` | Ein | \<account private endpoint IP address of Azure Purview\> |
| `PurviewA.web.purview.azure.com` | Ein | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.manifest.prod.ext.web.purview.azure.com` | Ein | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.cdn.prod.ext.web.purview.azure.com` | Ein | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.hub.prod.ext.web.purview.azure.com` | Ein | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.catalog.prod.ext.web.purview.azure.com` | Ein | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.cseo.prod.ext.web.purview.azure.com` | Ein | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.datascan.prod.ext.web.purview.azure.com` | Ein | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.datashare.prod.ext.web.purview.azure.com` | Ein | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.datasource.prod.ext.web.purview.azure.com` | Ein | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.policy.prod.ext.web.purview.azure.com` | Ein | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.sensitivity.prod.ext.web.purview.azure.com` | Ein | \<portal private endpoint IP address of Azure Purview\> |

<br> 

Das folgende Beispiel zeigt die DNS-Namensauflösung von Azure Purview von außerhalb des virtuellen Netzwerks oder wenn kein privater Azure-Endpunkt konfiguriert ist.

   :::image type="content" source="media/catalog-private-link/purview-name-resolution-external.png" alt-text="Screenshot: Auflösung des Azure Purview-Namens von außerhalb des Unternehmensnetzwerks.":::

Das folgende Beispiel zeigt die DNS-Namensauflösung von Azure Purview innerhalb des virtuellen Netzwerks.

   :::image type="content" source="media/catalog-private-link/purview-name-resolution-private-link.png" alt-text="Screenshot: Auflösung des Purview-Namens innerhalb des Unternehmensnetzwerks.":::

Es ist wichtig, die DNS-Einstellungen ordnungsgemäß zu konfigurieren, um die IP-Adresse des privaten Endpunkts in den vollqualifizierten Domänennamen (FQDN) der Verbindungszeichenfolge aufzulösen.

Wenn Sie einen benutzerdefinierten DNS-Server in Ihrem Netzwerk verwenden, müssen Clients in der Lage sein, den FQDN für den Azure Purview-Endpunkt in die IP-Adresse des privaten Endpunkts aufzulösen. Konfigurieren Sie den DNS-Server so, dass Ihre Private Link-Unterdomäne an die private DNS-Zone für das virtuelle Netzwerk delegiert wird. Alternativ können Sie die A-Einträge für `PurviewA.privatelink.purview.azure.com` mit der IP-Adresse des privaten Endpunkts konfigurieren.

   :::image type="content" source="media/catalog-private-link/purview-name-resolution-diagram.png" alt-text="Darstellung der Azure Purview-Namensauflösung.":::

Weitere Informationen finden Sie unter [DNS-Konfiguration für private Azure-Endpunkte](../private-link/private-endpoint-dns.md).

## <a name="enable-access-to-azure-active-directory"></a>Aktivieren des Zugriffs auf Azure Active Directory

> [!NOTE]
> Wenn Ihr virtueller Computer, Ihr VPN-Gateway oder Ihr VNet-Peeringgateway über öffentlichen Internetzugriff verfügt, kann er bzw. es auf das Azure Purview-Portal und das Azure Purview-Konto zugreifen, das mit privaten Endpunkten aktiviert ist. Daher müssen Sie die übrigen Anweisungen nicht befolgen. Wenn für Ihr privates Netzwerk NSG-Regeln festgelegt sind, die den gesamten öffentlichen Internetdatenverkehr ablehnen, müssen Sie Regeln hinzufügen, um den Azure Active Directory-Zugriff (Azure AD) zu aktivieren. Befolgen Sie die entsprechenden Anweisungen.

Diese Anweisungen gelten für den sicheren Zugriff auf Azure Purview von einer Azure-VM. Wenn Sie ein VPN-Gateway oder ein anderes VNet-Peeringgateway verwenden, müssen Sie ähnliche Schritte ausführen.

1. Navigieren Sie im Azure-Portal zu Ihrer VM, und wählen Sie im Abschnitt **Einstellungen** die Option **Netzwerk** aus. Wählen Sie dann **Regeln für ausgehende Ports** > **Regel für ausgehenden Port hinzufügen** aus.

   :::image type="content" source="media/catalog-private-link/outbound-rule-add.png" alt-text="Screenshot: Hinzufügen einer Ausgangsregel.":::

1. Gehen Sie im Bereich **Ausgangssicherheitsregel hinzufügen** wie folgt vor:

   1. Wählen Sie **Diensttag** als **Ziel** aus.
   1. Wählen Sie unter **Zieldiensttag** die Option **AzureActiveDirectory** aus.
   1. Wählen Sie unter **Zielportbereiche** das Sternchen (*) aus.
   1. Wählen Sie für **Aktion** die Option **Zulassen** aus.
   1. Der Wert unter **Priorität** sollte höher sein als die Regel, mit der der gesamte Internetdatenverkehr verweigert wurde.
   
   Erstellen Sie die Regel.

   :::image type="content" source="media/catalog-private-link/outbound-rule-details.png" alt-text="Screenshot: Details beim Hinzufügen einer Ausgangsregel.":::

1. Führen Sie die gleichen Schritte aus, um eine weitere Regel zu erstellen, die das Diensttag **AzureResourceManager** zulässt. Wenn Sie auf das Azure-Portal zugreifen müssen, können Sie auch eine Regel für das Diensttag **AzurePortal** hinzufügen.

1. Stellen Sie eine Verbindung mit der VM her, und öffnen Sie den Browser. Navigieren Sie zur Browserkonsole, indem Sie STRG+UMSCHALT+J drücken, und wechseln Sie dort zur Registerkarte „Netzwerk“, um Netzwerkanforderungen zu überwachen. Geben Sie im Feld „URL“ die Adresse „web.purview.azure.com“ ein, und versuchen Sie, sich mit Ihren Azure AD-Anmeldeinformationen anzumelden. Wahrscheinlich ist die Anmeldung nicht erfolgreich, und Sie können auf der Registerkarte **Netzwerk** der Konsole erkennen, dass Azure AD versucht, auf „aadcdn.msauth.net“ zuzugreifen, der Zugriff aber blockiert wird.

   :::image type="content" source="media/catalog-private-link/login-fail.png" alt-text="Screenshot: Details der nicht erfolgreichen Anmeldung.":::

1. Öffnen Sie in diesem Fall auf dem virtuellen Computer eine Eingabeaufforderung, und pingen Sie „aadcdn.msauth.net“. Nachdem Sie die zugehörige IP-Adresse abgerufen haben, fügen Sie in den Netzwerksicherheitsregeln der VM eine Regel für ausgehenden Port für die IP-Adresse hinzu. Legen Sie für **Ziel** die Option **IP-Adressen** und für **Ziel-IP-Adressen** die IP-Adresse des AAD-CDN fest. Aufgrund von Azure Load Balancer und Azure Traffic Manager ist die IP-Adresse des Content Delivery Network von Azure AD möglicherweise dynamisch. Nachdem Sie die IP-Adresse abgerufen haben, sollten Sie sie der VM-Hostdatei hinzufügen, um zu erzwingen, dass der Browser diese IP-Adresse für den Zugriff auf Azure AD Content Delivery Network aufruft.

   :::image type="content" source="media/catalog-private-link/ping.png" alt-text="Screenshot: Testping.":::

   :::image type="content" source="media/catalog-private-link/aadcdn-rule.png" alt-text="Screenshot: Azure AD Content Delivery Network-Regel.":::

1. Nachdem die neue Regel erstellt wurde, navigieren Sie zurück zur VM, und melden Sie sich erneut mit Ihren Azure AD-Anmeldeinformationen an. Wenn die Anmeldung erfolgreich ist, kann das Azure Purview-Portal verwendet werden. In einigen Fällen führt Azure AD jedoch basierend auf dem Kontotyp eines Kunden eine Umleitung an andere Domänen durch. Für „live.com“-Konten leitet Azure AD Benutzer zur Anmeldung beispielsweise an „live.com“ um, und diese Anforderungen werden erneut blockiert. Bei Microsoft-Mitarbeiterkonten greift Azure AD auf „msft.sts.microsoft.com“ zu, um Anmeldeinformationen zu erhalten.

   Überprüfen Sie auf der Registerkarte **Netzwerk** der Browserkonsole die Netzwerkanforderungen, um die Domäne zu ermitteln, deren Anforderungen blockiert werden. Wiederholen Sie dann den vorherigen Schritt, um ihre IP-Adresse abzurufen, und fügen Sie in der Netzwerksicherheitsgruppe Regeln für ausgehenden Port hinzu, um Anforderungen für diese IP-Adresse zuzulassen. Fügen Sie die URL und die IP-Adresse nach Möglichkeit der Hostdatei der VM hinzu, um die DNS-Auflösung zu korrigieren. Wenn Sie die genauen IP-Adressbereiche der Anmeldedomäne kennen, können Sie sie auch direkt den Netzwerkregeln hinzufügen.

1. Ihre Azure AD-Anmeldung sollte nun erfolgreich sein. Das Azure Purview-Portal wird erfolgreich geladen, es können jedoch nicht alle Azure Purview-Konten aufgeführt werden, da nur auf ein bestimmtes Azure Purview-Konto zugegriffen werden kann. Geben Sie `web.purview.azure.com/resource/{PurviewAccountName}` ein, um direkt zu dem Azure Purview-Konto zu gelangen, für das Sie erfolgreich einen privaten Endpunkt eingerichtet haben.

## <a name="ingestion-private-endpoints-and-scanning-sources"></a>Private Erfassungsendpunkte und Überprüfen von Quellen

Quellen in privaten Netzwerken, virtuellen Netzwerken und hinter privaten Endpunkten müssen überprüft werden. Um die Netzwerkisolation für Ihre Metadaten zwischen der Quelle, die überprüft wird, und Azure Purview DataMap sicherzustellen, führen Sie folgende Schritte aus:

1. Führen Sie die Schritte in [diesem Abschnitt](#create-an-ingestion-private-endpoint) aus, um einen privaten Erfassungsendpunkt zu aktivieren.

1. Überprüfen Sie die Quelle mithilfe einer selbstgehosteten Integration Runtime (IR).

    1. Alle lokalen Quelltypen wie z. B. Azure SQL Server, Oracle oder SAP werden derzeit nur über Überprüfungen mit einer selbstgehosteten Integration Runtime (IR) unterstützt. Die selbstgehostete IR muss innerhalb Ihres privaten Netzwerks ausgeführt und mit Ihrem virtuellen Netzwerk in Azure per Peering verbunden werden. Führen Sie [diese Schritte](#create-an-ingestion-private-endpoint) aus, um Ihr virtuelles Azure-Netzwerk für Ihren privaten Erfassungsendpunkt zu aktivieren.

    2. Für alle Azure-Quelltypen wie z. B. Azure Blob Storage oder Azure SQL-Datenbank müssen Sie die Überprüfung mithilfe einer selbstgehosteten IR explizit ausführen, um die Netzwerkisolation sicherzustellen. Führen Sie die Schritte unter [Erstellen und Verwalten einer selbstgehosteten Integration Runtime](manage-integration-runtimes.md), um eine selbstgehostete IR einzurichten. Richten Sie anschließend die Überprüfung für die Azure-Quelle ein, indem Sie in der Dropdownliste **Verbindung über Integration Runtime herstellen** Ihre selbstgehostete IR auswählen, um die Netzwerkisolation sicherzustellen.
    
       :::image type="content" source="media/catalog-private-link/shir-for-azure.png" alt-text="Screenshot: Ausführen einer Azure-Überprüfung mithilfe einer selbstgehosteten IR.":::

> [!NOTE]
> Wenn Sie einen privaten Endpunkt zur Erfassung verwenden, können Sie Azure Integration Runtime lediglich zur Überprüfung der folgenden Datenquellen verwenden:
>
> - Azure Blob Storage
> - Azure Data Lake Gen2
>
> Für andere Datenquellen ist eine selbstgehostete IR erforderlich. Anmeldungen über MSI werden derzeit nicht unterstützt, wenn Sie Ihre Azure-Quellen mithilfe einer selbstgehosteten IR überprüfen. Verwenden Sie für diese Azure-Quelle eine der anderen unterstützten Anmeldemethoden.

## <a name="enable-a-private-endpoint-on-existing-azure-purview-accounts"></a>Aktivieren eines privaten Endpunkts für vorhandene Azure Purview-Konten

Es gibt zwei Möglichkeiten, nach dem Erstellen Ihres Azure Purview-Kontos private Azure Purview-Endpunkte hinzuzufügen:

- Über das Azure-Portal (Azure Purview-Konto).
- Über Private Link Center.

### <a name="use-the-azure-portal-azure-purview-account"></a>Verwenden des Azure-Portals (Azure Purview-Konto)

1. Navigieren Sie im Azure-Portal zum Azure Purview-Konto, und wählen Sie unter **Einstellungen** > **Netzwerk** die Option **Private Endpunktverbindungen** aus.

    :::image type="content" source="media/catalog-private-link/pe-portal.png" alt-text="Screenshot: Erstellen eines privaten Endpunkts für ein Konto.":::

1. Wählen Sie **+ Privater Endpunkt** aus, um einen neuen privaten Endpunkt zu erstellen.

1. Geben Sie die grundlegenden Informationen an.

1. Wählen Sie auf der Registerkarte **Ressource** für **Ressourcentyp** die Option **Microsoft.Purview/accounts** aus.

1. Wählen Sie für **Ressource** das Azure Purview-Konto und für **Zielunterressource** die Option **Konto** aus.

1. Wählen Sie auf der Registerkarte **Konfiguration** das virtuelle Netzwerk und eine private DNS-Zone aus. Wechseln Sie zur Zusammenfassungsseite, und wählen Sie **Erstellen** aus, um den privaten Endpunkt für das Konto zu erstellen.

> [!NOTE]
> Führen Sie dieselben Schritte aus, wenn Sie für **Zielunterressource** die Option **Portal** auswählen.

#### <a name="create-an-ingestion-private-endpoint"></a>Erstellen eines privaten Endpunkts für die Erfassung

1. Navigieren Sie im Azure-Portal zum Azure Purview-Konto, und wählen Sie unter **Einstellungen** > **Netzwerk** die Option **Private Endpunktverbindungen** aus.

1. Navigieren Sie zur Registerkarte **Private Endpunktverbindungen für die Erfassung**, und wählen Sie **+ Neu** aus, um einen neuen privaten Endpunkt für die Erfassung zu erstellen.

1. Geben Sie die grundlegenden Informationen und Details des virtuellen Netzwerks ein.

   :::image type="content" source="media/catalog-private-link/ingestion-pe-fill-details.png" alt-text="Screenshot: Ausfüllen der Details des privaten Endpunkts.":::

1. Wählen Sie **Erstellen** aus, um die Einrichtung abzuschließen.

> [!NOTE]
> Private Endpunkte für die Erfassung können nur über die oben beschriebene Benutzeroberfläche im Azure Purview-Portal erstellt werden. Sie können nicht über Private Link Center erstellt werden.

### <a name="use-the-private-link-center"></a>Verwenden von Private Link Center

1. Wechseln Sie zum [Azure-Portal](https://portal.azure.com).

1. Geben Sie in der Suchleiste im oberen Bereich der Seite **private link** ein, und navigieren Sie zum Bereich **Private Link**, indem Sie die erste Option auswählen.

1. Wählen Sie **+ Hinzufügen** aus, und geben Sie die grundlegenden Informationen an.

   :::image type="content" source="media/catalog-private-link/private-link-center.png" alt-text="Screenshot: Erstellen privater Endpunkte über Private Link Center.":::

1. Wählen Sie für **Ressource** das bereits erstellte Azure Purview-Konto aus. Wählen Sie für **Zielunterressource** die Option **Konto** aus.

1. Wählen Sie auf der Registerkarte **Konfiguration** das virtuelle Netzwerk und eine private DNS-Zone aus. Wechseln Sie zur Zusammenfassungsseite, und wählen Sie **Erstellen** aus, um den privaten Endpunkt für das Konto zu erstellen.

> [!NOTE]
> Führen Sie dieselben Schritte aus, wenn Sie für **Zielunterressource** die Option **Portal** auswählen.

## <a name="firewalls-to-restrict-public-access"></a>Firewalls zur Einschränkung des öffentlichen Zugriffs

Führen Sie diese Schritte aus, um den Zugriff auf das Azure Purview-Konto über das öffentliche Internet vollständig zu unterbrechen. Diese Einstellung gilt sowohl für private Endpunktverbindungen als auch für private Endpunktverbindungen für die Erfassung.

1. Navigieren Sie im Azure-Portal zum Azure Purview-Konto, und wählen Sie unter **Einstellungen** > **Netzwerk** die Option **Private Endpunktverbindungen** aus.

1. Navigieren Sie zur Registerkarte **Firewall**, und vergewissern Sie sich, dass die Einstellung **Verweigern** festgelegt ist.

   :::image type="content" source="media/catalog-private-link/private-endpoint-firewall.png" alt-text="Screenshot: Firewalleinstellungen für private Endpunkte.":::

## <a name="next-steps"></a>Nächste Schritte

- [Browsen Sie im Azure Purview Datenkatalog](how-to-browse-catalog.md)
- [Durchsuchen Sie den Azure Purview Datenkatalog](how-to-search-catalog.md)

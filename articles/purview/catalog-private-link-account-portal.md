---
title: Privates und sicheres Herstellen einer Verbindung mit Ihrem Purview-Konto
description: In diesem Artikel wird beschrieben, wie Sie einen privaten Endpunkt einrichten können, um über ein eingeschränktes Netzwerk eine Verbindung mit Ihrem Purview-Konto herzustellen.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 08/18/2021
ms.openlocfilehash: 26b98ad9c4c042c1e6bf60889625a4d23090f6f5
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/20/2021
ms.locfileid: "122515923"
---
# <a name="connect-privately-and-securely-to-your-purview-account"></a>Privates und sicheres Herstellen einer Verbindung mit Ihrem Purview-Konto
In diesem Leitfaden erfahren Sie, wie Sie private Endpunkte für Ihr Purview-Konto bereitstellen, sodass Sie nur über VNets und private Netzwerke eine Verbindung mit Ihrem Azure Purview-Konto herstellen können. Hierfür müssen Sie einen privaten _Kontoendpunkt_, einen privaten _Portalendpunkt_ und einen privaten _Erfassungsendpunkt_ für Ihr Azure Purview-Konto bereitstellen.

Mit dem privaten Azure Purview-Endpunkt _Konto_ wird eine zusätzliche Sicherheitsschicht hinzugefügt, indem Szenarios ermöglicht werden, bei denen nur Clientaufrufe aus dem virtuellen Netzwerk für den Zugriff auf das Azure Purview-Konto zulässig sind. Dieser private Endpunkt ist auch eine Voraussetzung für den privaten Endpunkt „Portal“.

Der privaten Azure Purview-Endpunkt _Portal_ ist erforderlich, um die Verbindung zu Azure Purview Studio über ein privates Netzwerk zu ermöglichen.

   :::image type="content" source="media/catalog-private-link/purview-private-link-account-portal.png" alt-text="Abbildung der Azure Purview- und Private Link-Architektur.":::

Weitere Informationen zum Dienst Azure Private Link finden Sie unter [Was ist ein privater Endpunkt in Azure?](../private-link/private-endpoint-overview.md).

## <a name="deployment-checklist"></a>Bereitstellungsprüfliste
Mithilfe einer der in diesem Leitfaden erläuterten Bereitstellungsoptionen können Sie ein neues Azure Purview-Konto mit den privaten Endpunkten für das _Konto_ und das _Portal_ bereitstellen. Alternativ können Sie diese privaten Endpunkte für ein bereits vorhandenes Azure Purview-Konto bereitstellen:

1. Wählen Sie ein geeignetes virtuelles Azure-Netzwerk und ein Subnetz aus, um private Endpunkte für Azure Purview bereitzustellen. Wählen Sie eine der folgenden Optionen aus:
   - Erstellen Sie in Ihrem Azure-Abonnement ein [neues virtuelles Netzwerk](../virtual-network/quick-create-portal.md).
   - Suchen Sie ein vorhandenes virtuelles Azure-Netzwerk und ein Subnetz in Ihrem Azure-Abonnement.
  
2. Definieren Sie eine geeignete [DNS-Namensauflösungsmethode](./catalog-private-link-name-resolution.md#deployment-options), damit über private IP-Adressen auf das Azure Purview-Konto und das Webportal zugegriffen werden kann. Sie können auch eine der folgenden Optionen verwenden:
   - Stellen Sie neue Azure DNS-Zonen mithilfe der weiter unten in diesem Leitfaden beschriebenen Schritte bereit.
   - Fügen Sie den vorhandenen Azure DNS-Zonen die erforderlichen DNS-Einträge hinzu, indem Sie die weiter unten in diesem Leitfaden beschriebenen Schritte ausführen.
   - Fügen Sie nach Abschluss der Schritte in diesem Leitfaden den vorhandenen DNS-Servern die erforderlichen DNS-A-Einträge manuell hinzu.
3. Stellen Sie ein [neues Purview-Konto](#option-1---deploy-a-new-azure-purview-account-with-account-and-portal-private-endpoints) mit privaten Endpunkten für das Konto und das Portal bereit, oder stellen Sie diese privaten Endpunkte für ein [bereits vorhandenes Purview-Konto](#option-2---enable-account-and-portal-private-endpoint-on-existing-azure-purview-accounts) bereit.
4. [Aktivieren Sie den Zugriff auf Azure Active Directory](#enable-access-to-azure-active-directory), wenn in Ihrem privaten Netzwerk die Regeln für Netzwerksicherheitsgruppen so eingestellt sind, dass der gesamte öffentliche Internetverkehr abgelehnt wird.
5. Passen Sie nach Abschluss dieses Leitfadens bei Bedarf die DNS-Konfigurationen an.
6. Überprüfen Sie die Netzwerk- und Namensauflösung zwischen dem Verwaltungscomputer und Azure Purview. 

## <a name="option-1---deploy-a-new-azure-purview-account-with-_account_-and-_portal_-private-endpoints"></a>Option 1: Bereitstellen eines neuen Azure Purview-Kontos mit privaten Endpunkten für das _Konto_ und das _Portal_

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com) und dann zu der Seite **Purview-Konto**. Klicken Sie auf **+ Erstellen**, um ein neues Azure Purview-Konto zu erstellen.

2. Geben Sie die grundlegenden Informationen ein, und legen Sie auf der Registerkarte **Netzwerk** als Konnektivitätsmethode **Privater Endpunkt** fest. Legen Sie die Option „Privaten Endpunkt aktivieren für“ auf **Nur Konto und Portal** fest.

3. Navigieren Sie zu **Konto und Portal** und wählen Sie **+ Hinzufügen** aus, um Ihrem Azure Purview-Konto einen privaten Endpunkt hinzuzufügen.

   :::image type="content" source="media/catalog-private-link/purview-pe-scenario-1-1.png" alt-text="Screenshot: Optionen auf der Seite zum Erstellen eines privaten Endpunkts für Konto und Portal":::

4. Wählen Sie auf der Seite für **Purview-Unterressource** die Option **Einen Privaten Endpunkt erstellen** aus. Wählen Sie dann Ihren Standort aus, geben Sie einen Namen für den privaten Endpunkt des _Kontos_ an und klicken Sie dann auf **Konto**. Wählen Sie unter **Netzwerk** Ihr virtuelles Netzwerk und das Subnetz aus. Wählen Sie dann optional **Integration in eine private DNS-Zone** aus, um eine neue Azure Private DNS-Zone zu erstellen. 
   
   :::image type="content" source="media/catalog-private-link/purview-pe-scenario-1-2.png" alt-text="Screenshot: Seite zum Erstellen eines privaten Endpunkts für ein Konto":::


   > [!NOTE]
   > Sie können auch Ihre vorhandenen Azure Private DNS-Zonen verwenden oder später manuell die DNS-Einträge in Ihren DNS-Servern erstellen. Weitere Informationen finden Sie unter [Konfigurieren privater Endpunkte für die DNS-Namensauflösung](./catalog-private-link-name-resolution.md)

5. Klicken Sie auf **OK**.
   
6. Klicken Sie im Assistenten zum **Erstellen eines Purview-Kontos** noch mal auf **+ Hinzufügen**, um einen privaten Endpunkt für das _Portal_ hinzuzufügen.
     
7. Wählen Sie auf der Seite für **Purview-Unterressource** die Option **Einen Privaten Endpunkt erstellen** aus. Wählen Sie dann Ihren Standort aus, geben Sie einen Namen für den privaten Endpunkt des _Portals_ an und klicken Sie dann auf **Portal**. Wählen Sie unter **Netzwerk** Ihr virtuelles Netzwerk und das Subnetz aus. Wählen Sie dann optional **Integration in eine private DNS-Zone** aus, um eine neue Azure Private DNS-Zone zu erstellen. 
   
   > [!NOTE]
   > Sie können auch Ihre vorhandenen Azure Private DNS-Zonen verwenden oder später manuell die DNS-Einträge in Ihren DNS-Servern erstellen. Weitere Informationen finden Sie unter [Konfigurieren privater Endpunkte für die DNS-Namensauflösung](./catalog-private-link-name-resolution.md)

8.  Klicken Sie auf **OK**.
   
   :::image type="content" source="media/catalog-private-link/purview-pe-scenario-1-3.png" alt-text="Screenshot: Überprüfungsseite bei der Erstellung eines privaten Endpunkts":::

9.  Klicken Sie auf **Überprüfen + erstellen**. Auf der Seite **Überprüfen + erstellen** überprüft Azure Ihre Konfiguration.

10. Wenn die Meldung „Überprüfung erfolgreich“ angezeigt wird, wählen Sie **Erstellen** aus.

## <a name="option-2---enable-_account_-and-_portal_-private-endpoint-on-existing-azure-purview-accounts"></a>Option 2: Aktivieren der privaten Endpunkte für das _Konto_ und das _Portal_ für bereits vorhandene Azure Purview-Konten

Es gibt zwei Möglichkeiten, einem bestehenden Azure Purview-Konto private Azure Purview-Endpunkte für das _Konto_ und das _Portal_ hinzuzufügen:

- Über das Azure-Portal (Azure Purview-Konto).
- Über Private Link Center

### <a name="use-the-azure-portal-azure-purview-account"></a>Verwenden des Azure-Portals (Azure Purview-Konto)

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com). Klicken Sie auf Ihr Azure Purview-Konto und wählen Sie unter **Einstellungen** **Netzwerk** die Option **Private Endpunktverbindungen** aus.

    :::image type="content" source="media/catalog-private-link/pe-portal.png" alt-text="Screenshot: Erstellen eines privaten Endpunkts für ein Konto.":::

2. Wählen Sie **+ Privater Endpunkt** aus, um einen neuen privaten Endpunkt zu erstellen.

3. Geben Sie die grundlegenden Informationen an.

4. Wählen Sie auf der Registerkarte **Ressource** für **Ressourcentyp** die Option **Microsoft.Purview/accounts** aus.

5. Wählen Sie für **Ressource** das Azure Purview-Konto und für **Zielunterressource** die Option **Konto** aus.

6. Wählen Sie auf der Registerkarte **Konfiguration** das virtuelle Netzwerk aus, und klicken Sie optional auf „Azure Private DNS zone“ (Azure Private DNS-Zone), um eine neue Azure DNS-Zone zu erstellen.
   
   > [!NOTE]
   > Für die DNS-Konfiguration können Sie auch Ihre vorhandenen Azure Private DNS-Zonen aus der Dropdownliste verwenden oder die erforderlichen DNS-Einträge später manuell Ihren DNS-Servern hinzufügen. Weitere Informationen finden Sie unter [Konfigurieren privater Endpunkte für die DNS-Namensauflösung](./catalog-private-link-name-resolution.md)

7. Wechseln Sie zur Zusammenfassungsseite, und wählen Sie **Erstellen** aus, um den privaten Endpunkt des Portals zu erstellen.
   
8. Führen Sie dieselben Schritte aus, wenn Sie für die **Zielunterressource** die Option **Portal** auswählen.

### <a name="use-the-private-link-center"></a>Verwenden von Private Link Center

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com).

1. Geben Sie in der Suchleiste im oberen Bereich der Seite **private link** ein, und navigieren Sie zum Bereich **Private Link**, indem Sie die erste Option auswählen.

1. Klicken Sie auf **+ Hinzufügen**, und geben Sie die grundlegenden Informationen an.

   :::image type="content" source="media/catalog-private-link/private-link-center.png" alt-text="Screenshot: Erstellen privater Endpunkte über Private Link Center":::

1. Wählen Sie für **Ressource** das bereits erstellte Azure Purview-Konto aus. Wählen Sie für **Untergeordnete Zielressource** die Option **Konto** aus.

1. Wählen Sie auf der Registerkarte **Konfiguration** das virtuelle Netzwerk und eine private DNS-Zone aus. Wechseln Sie zur Zusammenfassungsseite, und klicken Sie auf **Erstellen**, um den privaten Endpunkt für das Konto zu erstellen.

> [!NOTE]
> Führen Sie dieselben Schritte aus, wenn Sie für die **Zielunterressource** die Option **Portal** auswählen.

## <a name="enable-access-to-azure-active-directory"></a>Aktivieren des Zugriffs auf Azure Active Directory

> [!NOTE]
> Wenn Ihre VM, Ihr VPN-Gateway oder Ihr VNet-Peering-Gateway über öffentlichen Internetzugriff verfügt, kann sie bzw. es auf das Azure Purview-Portal und das Azure Purview-Konto zugreifen, für das private Endpunkte aktiviert sind. Daher müssen Sie die übrigen Anweisungen nicht befolgen. Wenn für Ihr privates Netzwerk Regeln für Netzwerksicherheitsgruppen festgelegt sind, die den gesamten öffentlichen Internetdatenverkehr ablehnen, müssen Sie Regeln hinzufügen, um den Azure Active Directory-Zugriff (Azure AD) zu aktivieren. Befolgen Sie dafür die entsprechenden Anweisungen.

Diese Anweisungen beziehen sich auf den sicheren Zugriff auf Azure Purview über einen virtuellen Azure-Computer. Wenn Sie ein VPN-Gateway oder ein anderes VNet-Peeringgateway verwenden, müssen Sie ähnliche Schritte ausführen.

1. Navigieren Sie im Azure-Portal zu Ihrem virtuellen Computer, und wählen Sie im Abschnitt **Einstellungen** die Option **Netzwerk** aus. Klicken Sie dann auf **Regeln für ausgehende Ports** und anschließend auf **Regel für ausgehenden Port hinzufügen**.

   :::image type="content" source="media/catalog-private-link/outbound-rule-add.png" alt-text="Ein Screenshot, der das Hinzufügen einer Ausgangsregel zeigt":::.

1. Gehen Sie im Bereich **Ausgangssicherheitsregel hinzufügen** wie folgt vor:

   1. Wählen Sie unter **Ziel** die Option **Diensttag** aus.
   1. Wählen Sie unter **Zieldiensttag** die Option **AzureActiveDirectory** aus.
   1. Wählen Sie unter **Zielportbereiche** „*“ aus.
   1. Wählen Sie für **Aktion** die Option **Zulassen** aus.
   1. Der Wert für **Priorität** sollte höher sein als bei der Regel, mit der der gesamte Internetdatenverkehr abgelehnt wurde.
   
   Erstellen Sie die Regel.

   :::image type="content" source="media/catalog-private-link/outbound-rule-details.png" alt-text="Screenshot: Details beim Hinzufügen einer Ausgangsregel":::

1. Führen Sie die gleichen Schritte aus, um eine weitere Regel zu erstellen, die das Diensttag **AzureResourceManager** zulässt. Wenn Sie auf das Azure-Portal zugreifen müssen, können Sie auch eine Regel für das Diensttag **AzurePortal** hinzufügen.

1. Stellen Sie eine Verbindung mit dem virtuellen Computer her, und öffnen Sie den Browser. Navigieren Sie zur Browserkonsole, indem Sie STRG+UMSCHALT+J drücken, und wechseln Sie dort zur Registerkarte „Netzwerk“, um die Netzwerkanforderungen zu überwachen. Geben Sie im Feld „URL“ die Adresse „web.purview.azure.com“ ein, und versuchen Sie, sich mit Ihren Azure AD-Anmeldeinformationen anzumelden. Wahrscheinlich ist die Anmeldung nicht erfolgreich, und Sie können auf der Registerkarte **Netzwerk** der Konsole sehen, dass Azure AD versucht, auf „aadcdn.msauth.net“ zuzugreifen, der Zugriff aber blockiert wird.

   :::image type="content" source="media/catalog-private-link/login-fail.png" alt-text="Screenshot: Details für eine nicht erfolgreiche Anmeldung":::

1. Öffnen Sie in diesem Fall auf dem virtuellen Computer eine Eingabeaufforderung, pingen Sie aadcdn.msauth.net, und rufen Sie die zugehörige IP-Adresse ab. Fügen Sie anschließend in den Netzwerksicherheitsregeln des virtuellen Computers eine Regel für einen ausgehenden Port für diese IP-Adresse hinzu. Legen Sie **Ziel** auf **IP-Adressen** fest, und geben Sie bei **Destination IP addresses** (Ziel-IP-Adressen) die aadcdn-IP-Adresse an. Aufgrund von Azure Load Balancer und Azure Traffic Manager ist die IP-Adresse von Azure AD Content Delivery Network möglicherweise dynamisch. Nachdem Sie die IP-Adresse abgerufen haben, sollten Sie sie der Hostdatei des virtuellen Computers hinzufügen, um zu erzwingen, dass der Browser diese IP-Adresse für den Zugriff auf Azure AD Content Delivery Network aufruft.

   :::image type="content" source="media/catalog-private-link/ping.png" alt-text="Screenshot: Testping":::

   :::image type="content" source="media/catalog-private-link/aadcdn-rule.png" alt-text="Screenshot: Regel für Azure AD Content Delivery Network":::

1. Navigieren Sie nach dem Erstellen der neuen Regel zurück zum virtuellen Computer, und versuchen Sie noch mal, sich mit Ihren Azure AD-Anmeldeinformationen anzumelden. Wenn die Anmeldung erfolgreich ist, kann das Azure Purview-Portal verwendet werden. In einigen Fällen leitet Azure AD die Kund*innen jedoch basierend auf ihrem Kontotyp für die Anmeldung zu einer anderen Domäne um. Bei live.com-Konten leitet Azure AD die Benutzer*innen beispielsweise für die Anmeldung zu live.com um und blockiert diese Anforderungen anschließend wieder. Bei Microsoft-Mitarbeiterkonten ruft Azure AD die Anmeldeinformationen von msft.sts.microsoft.com ab.

   Überprüfen Sie auf der Registerkarte **Netzwerk** im Browser die Netzwerkanforderungen, um die Domäne zu ermitteln, deren Anforderungen blockiert werden. Wiederholen Sie dann den vorherigen Schritt zum Abrufen der IP-Adresse dieser Domäne, und fügen Sie in der Netzwerksicherheitsgruppe Regeln für einen ausgehenden Port hinzu, um Anforderungen für diese IP-Adresse zuzulassen. Ergänzen Sie nach Möglichkeit die URL und die IP-Adresse in der Hostdatei des virtuellen Computers, um die DNS-Auflösung zu korrigieren. Wenn Sie die genauen IP-Adressbereiche der Anmeldedomäne kennen, können Sie diese auch direkt in den Netzwerkregeln hinzufügen.

1. Ihre Azure AD-Anmeldung sollte nun erfolgreich sein. Das Azure Purview-Portal wird erfolgreich geladen, es können jedoch nicht alle Azure Purview-Konten aufgeführt werden, da nur auf ein bestimmtes Azure Purview-Konto zugegriffen werden kann. Geben Sie `web.purview.azure.com/resource/{PurviewAccountName}` ein, um direkt zu dem Azure Purview-Konto zu gelangen, für das Sie erfolgreich einen privaten Endpunkt eingerichtet haben.

## <a name="next-steps"></a>Nächste Schritte

-  [Überprüfen der Auflösung für private Endpunkte](./catalog-private-link-name-resolution.md)
-  [Verwalten von Datenquellen in Azure Purview](./manage-data-sources.md)
-  [Problembehandlung bei der Konfiguration privater Endpunkte für Ihr Azure Purview-Konto](./catalog-private-link-troubleshoot.md)

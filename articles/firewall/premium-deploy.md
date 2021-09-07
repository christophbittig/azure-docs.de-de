---
title: Bereitstellen und Konfigurieren von Azure Firewall Premium
description: Erfahren Sie, wie Sie Azure Firewall Premium bereitstellen und konfigurieren.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: how-to
ms.date: 07/15/2021
ms.author: victorh
ms.openlocfilehash: 9358852dd8b16f495d5123b59ea3ae0f4c29b00f
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114441146"
---
# <a name="deploy-and-configure-azure-firewall-premium"></a>Bereitstellen und Konfigurieren von Azure Firewall Premium


 Azure Firewall Premium ist eine Firewall der nächsten Generation mit Funktionen, die für streng vertrauliche und regulierte Umgebungen erforderlich sind. Es umfasst die folgenden Features:

- **TLS-Inspektion** – Entschlüsselt den ausgehenden Datenverkehr, verarbeitet die Daten, verschlüsselt sie dann und sendet sie an das Ziel.
- **IDPS** – Ein System zur Erkennung und Verhinderung von Eindringversuchen in Netzwerke (IDPS) ermöglicht es Ihnen, Netzwerkaktivitäten auf schädliche Aktivitäten zu überwachen, Informationen über diese Aktivitäten zu protokollieren, sie zu melden und optional zu versuchen, sie zu blockieren.
- **URL-Filterung** – Erweitert die FQDN-Filterfunktion von Azure Firewall, um eine gesamte URL zu berücksichtigen. Beispiel: `www.contoso.com/a/c` anstelle von `www.contoso.com`.
- **Webkategorien** – Administratoren können den Benutzerzugriff auf Websitekategorien wie Gaming- oder Social Media-Websites zulassen oder verweigern.

Weitere Informationen finden Sie unter [Azure Firewall Premium-Features](premium-features.md).

Sie werden eine Vorlage verwenden, um eine Testumgebung bereitzustellen, die über ein zentrales VNet (10.0.0.0/16) mit drei Subnetzen verfügt:
- Ein Workersubnetz (10.0.10.0/24)
- Ein Azure Bastion-Subnetz (10.0.20.0/24)
- Ein Firewallsubnetz (10.0.100.0/24)

Der Einfachheit halber wird in dieser Testumgebung ein einzelnes zentrales VNet verwendet. Für Produktionszwecke ist eine [Hub-and-Spoke-Topologie](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) mit VNets mit Peering üblicher.

:::image type="content" source="media/premium-deploy/premium-topology.png" alt-text="Zentrale VNet-Topologie":::

Der virtuelle Workercomputer ist ein Client, der HTTP/S-Anforderungen durch die Firewall sendet.

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="deploy-the-infrastructure"></a>Bereitstellen der Infrastruktur

Die Vorlage stellt eine vollständige Testumgebung für Azure Firewall Premium bereit, die mit IDPS, TLS-Inspektion, URL-Filterung und Webkategorien aktiviert ist:

- Ein neues Azure Firewall Premium und eine Firewallrichtlinie mit vordefinierten Einstellungen, um eine einfache Überprüfung ihrer Kernfunktionen (IDPS, TLS-Inspektion, URL-Filterung und Webkategorien) zu ermöglichen.
- Stellt alle Abhängigkeiten einschließlich Key Vault und einer verwalteten Identität bereit. In einer Produktionsumgebung sind diese Ressourcen möglicherweise bereits erstellt und werden nicht in derselben Vorlage benötigt.
- Generiert eine selbstsignierte Stammzertifizierungsstelle und stellt Sie für den generierten Key Vault bereit.
-  Generiert eine abgeleitete Zwischenzertifizierungsstelle und stellt Sie auf einem virtuellen Windows-Testcomputer (WorkerVM) bereit.
- Ein Bastionhost (BastionHost) wird ebenfalls bereitgestellt und kann zum Herstellen der Verbindung mit dem Windows-Testcomputer (WorkerVM) verwendet werden.



[![In Azure bereitstellen](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.network%2Fazurefirewall-premium%2Fazuredeploy.json)

## <a name="test-the-firewall"></a>Testen der Firewall

Jetzt können Sie IDPS, TLS-Inspektion, Webfilterung und Webkategorien testen.

### <a name="add-firewall-diagnostics-settings"></a>Hinzufügen von Einstellungen für die Firewalldiagnose

Zum Sammeln von Firewallprotokollen müssen Sie entsprechende Diagnoseeinstellungen hinzufügen.

1. Wählen Sie **DemoFirewall** und unter **Überwachung** die Option **Diagnoseeinstellungen** aus.
2. Klicken Sie auf **Diagnoseeinstellung hinzufügen**.
3. Für **Name der Diagnoseeinstellung** geben Sie *fw-diag* ein.
4. Wählen Sie unter **Protokoll** die Optionen **AzureFirewallApplicationRule** und **AzureFirewallNetworkRule** aus.
5. Wählen Sie unter **Zieldetails** die Option **An Log Analytics-Arbeitsbereich senden** aus.
6. Wählen Sie **Speichern** aus.

### <a name="idps-tests"></a>IDPs-Tests

Zum Testen von IDPs müssen Sie Ihren eigenen internen Webserver mit einem entsprechenden Serverzertifikat bereitstellen. Weitere Informationen zu den Zertifikatsanforderungen für Azure Firewall Premium finden Sie unter [Zertifikate von Azure Firewall Premium](premium-certificates.md).

Sie können `curl` verwenden, um verschiedene HTTP-Header zu kontrollieren und schädlichen Datenverkehr zu simulieren.

#### <a name="to-test-idps-for-http-traffic"></a>So testen Sie IDPs für HTTP-Datenverkehr

1. Öffnen Sie auf dem virtuellen Computer „WorkerVM“ ein Eingabeaufforderungsfenster mit Administratorrechten.
2. Geben Sie den folgenden Befehl an der Eingabeaufforderung ein:

   `curl -A "BlackSun" <your web server address>`
3. Die Antwort des Webservers wird angezeigt.
4. Wechseln Sie im Azure-Portal zu den Protokollen der Firewallnetzwerkregel, um eine Warnung ähnlich der folgenden zu finden:

   :::image type="content" source="media/premium-deploy/alert-message.png" alt-text="Warnmeldung":::

   > [!NOTE]
   > Es kann einige Zeit dauern, bis die Daten in den Protokollen angezeigt werden. Gewähren Sie mindestens 20 Minuten für die Anzeige der Daten durch die Protokolle.
5. Fügen Sie eine Signaturregel für die Signatur 2008983 hinzu:

   1. Wählen Sie **DemoFirewallPolicy** und unter **Einstellungen** die Option **IDPS** aus.
   1. Wählen Sie die Registerkarte **Signaturregeln** aus.
   1. Geben Sie unter **Signatur-ID** in das offene Textfeld *2008983* ein.
   1. Wählen Sie unter **Modus** die Option **Ablehnen** aus.
   1. Wählen Sie **Speichern** aus.
   1. Warten Sie, bis die Bereitstellung abgeschlossen ist, bevor Sie den Vorgang fortsetzen.



6. Führen Sie auf der WorkerVM den Befehl `curl` erneut aus:

   `curl -A "BlackSun" <your web server address>`

   Da die HTTP-Anforderung jetzt von der Firewall blockiert wird, sehen Sie nach Ablauf des Verbindungstimeouts die folgende Ausgabe:

   `read tcp 10.0.100.5:55734->10.0.20.10:80: read: connection reset by peer`

7. Wechseln Sie zu den Überwachungsprotokollen im Azure-Portal, und suchen Sie die Meldung für die blockierte Anforderung.
<!---8. Now you can bypass the IDPS function using the **Bypass list**.

   1. On the **IDPS (preview)** page, select the **Bypass list** tab.
   2. Edit **MyRule** and set **Destination** to *10.0.20.10, which is the ServerVM private IP address.
   3. Select **Save**.
1. Run the test again: `curl -A "BlackSun" http://server.2020-private-preview.com` and now you should get the `Hello World` response and no log alert. --->

#### <a name="to-test-idps-for-https-traffic"></a>So testen Sie IDPS für HTTPS-Datenverkehr

Wiederholen Sie diese curl-Tests mit HTTPS anstelle von HTTP. Beispiel:

`curl --ssl-no-revoke -A "BlackSun" <your web server address>`

Es sollten dieselben Ergebnisse wie bei den HTTP-Tests angezeigt werden.

### <a name="tls-inspection-with-url-filtering"></a>TLS-Inspektion mit URL-Filterung

Verwenden Sie die folgenden Schritte, um die TLS-Inspektion mit URL-Filterung zu testen.

1. Bearbeiten Sie die Anwendungsregeln der Firewallrichtlinie, und fügen Sie eine neue Regel namens `AllowURL` zur Regelsammlung `AllowWeb` hinzu. Konfigurieren Sie die Ziel-URL als `www.nytimes.com/section/world`, die IP-Quelladresse als **\* *_ und den Zieltyp als _* URL**, und wählen Sie die Optionen **TLS-Überprüfung** und die Protokolle **HTTP, HTTPS** aus.

3. Wenn die Bereitstellung abgeschlossen ist, öffnen Sie einen Browser auf der WorkerVM, wechseln Sie zu `https://www.nytimes.com/section/world` und überprüfen Sie, ob die HTML-Antwort wie erwartet im Browser angezeigt wird.
4. Im Azure-Portal können Sie die gesamte URL in den Protokollen der Anwendungsregelüberwachung anzeigen:

      :::image type="content" source="media/premium-deploy/alert-message-url.png" alt-text="Warnmeldung mit Anzeige der URL":::

Einige HTML-Seiten können unvollständig aussehen, da sie auf andere URLs verweisen, die abgelehnt werden. Um dieses Problem zu lösen, kann der folgende Ansatz verfolgt werden:

- Wenn die HTML-Seite Links zu anderen Domänen enthält, können Sie diese Domänen zu einer neuen Anwendungsregel hinzufügen, die den Zugriff auf diese FQDNs gestattet.
- Wenn die HTML-Seite Links zu untergeordneten URLs enthält, können Sie die Regel ändern und ein Sternchen zur URL hinzufügen. Beispiel: `targetURLs=www.nytimes.com/section/world*`

   Alternativ können Sie der Regel eine neue URL hinzufügen. Beispiel: 

   `www.nytimes.com/section/world, www.nytimes.com/section/world/*`

### <a name="web-categories-testing"></a>Testen von Webkategorien

Lassen Sie uns eine Anwendungsregel erstellen, um den Zugriff auf Sport-Websites zu gestatten.
1. Öffnen Sie im Portal Ihre Ressourcengruppe, und wählen Sie **DemoFirewallPolicy** aus.
2. Wählen Sie **Anwendungsregeln** und dann **Regelsammlung hinzufügen** aus.
3. Für **Name** geben Sie *GeneralWeb* und für **Priorität** den Wert *103* ein. Wählen Sie für **Regelsammlungsgruppe** die Option **DefaultApplicationRuleCollectionGroup** aus.
4. Geben Sie unter **Regeln** als **Name** *AllowSports*, unter **Quelle** *\** und unter **Protokoll** *http, https* ein. Wählen Sie anschließend die Option **TLS-Überprüfung**, unter **Zieltyp** die Option *Webkategorien* und unter **Ziel** die Option *Sport* aus.
5. Wählen Sie **Hinzufügen**.

      :::image type="content" source="media/premium-deploy/web-categories.png" alt-text="Webkategorie „Sport“":::
6. Wenn die Bereitstellung abgeschlossen ist, wechseln Sie zu **WorkerVM**, und öffnen Sie einen Webbrowser, um zu `https://www.nfl.com` zu navigieren.

   Es sollte die NFL-Webseite angezeigt werden, und das Anwendungsregelprotokoll zeigt, dass für eine Regel für **Webkategorie: Sport** ein Treffer gefunden und die Anforderung zugelassen wurde.

## <a name="next-steps"></a>Nächste Schritte

- [Azure Firewall Premium im Azure-Portal](premium-portal.md)
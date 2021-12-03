---
title: 'Tutorial: Zuordnen eines vorhandenen benutzerdefinierten DNS-Namens'
description: Hier erfahren Sie, wie Sie einen vorhandenen benutzerdefinierten DNS-Domänennamen einer Web-App, einem Back-End einer mobilen App oder einer API-App in Azure App Service hinzufügen.
keywords: App Service, Azure App Service, Domänenzuordnung, Domänenname, vorhandene Domäne, Hostname, Vanity-Domäne
ms.assetid: dc446e0e-0958-48ea-8d99-441d2b947a7c
ms.topic: tutorial
ms.date: 05/27/2021
ms.custom: mvc, seodec18, devx-track-azurepowershell
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./app-service-web-tutorial-custom-domain-uiex
ms.openlocfilehash: 607a1e8e12fa3840235e3158e98fb7dfb34a73fe
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131447814"
---
# <a name="tutorial-map-an-existing-custom-dns-name-to-azure-app-service"></a>Tutorial: Zuordnen eines vorhandenen benutzerdefinierten DNS-Namens zu Azure App Service

Von [Azure App Service](overview.md) wird ein hochgradig skalierbarer Webhostingdienst mit Self-Patching bereitgestellt. In diesem Tutorial wird gezeigt, wie App Service ein vorhandener benutzerdefinierter DNS-Name (Domain Name System) zugeordnet wird.

In diesem Tutorial lernen Sie, wie die folgenden Aufgaben ausgeführt werden:

> [!div class="checklist"]
> * Zuordnen einer Unterdomäne per [CNAME-Eintrag](https://en.wikipedia.org/wiki/CNAME_record)
> * Zuordnen einer Stammdomäne per [A-Eintrag](https://en.wikipedia.org/wiki/List_of_DNS_record_types#A)
> * Zuordnen einer [Platzhalterdomäne](https://en.wikipedia.org/wiki/Wildcard_DNS_record) per CNAME-Eintrag
> * Umleiten der Standard-URL an ein benutzerdefiniertes Verzeichnis


## <a name="1-prepare-your-environment"></a>1. Vorbereiten der Umgebung

* [Erstellen Sie eine App Service-App](./index.yml), oder verwenden Sie eine App, die Sie für ein anderes Tutorial erstellt haben.
* Vergewissern Sie sich, dass Sie DNS-Einträge für Ihre benutzerdefinierte Domäne bearbeiten können. Sollten Sie noch nicht über eine benutzerdefinierte Domäne verfügen, können Sie [eine App Service-Domäne erwerben](manage-custom-dns-buy-domain.md).

    > [!NOTE]
    > Zum Bearbeiten von DNS-Einträgen benötigen Sie Zugriff auf die DNS-Registrierung für Ihren Domänenanbieter, wie etwa GoDaddy. Um beispielsweise DNS-Einträge für `contoso.com` und `www.contoso.com` hinzuzufügen, müssen Sie die DNS-Einstellungen für die Stammdomäne `contoso.com` konfigurieren können.


## <a name="2-prepare-the-app"></a>2. Vorbereiten der App

Damit Sie einer Web-App einen benutzerdefinierten DNS-Namen zuordnen können, muss der [App Service-Plan](overview-hosting-plans.md) der Web-App einem kostenpflichtigen Tarif angehören, (nicht **Free (F1)** ).

#### <a name="sign-in-to-azure"></a>Anmelden bei Azure

Öffnen Sie das [Azure-Portal](https://portal.azure.com), und melden Sie sich mit Ihrem Azure-Konto an.

#### <a name="select-the-app-in-the-azure-portal"></a>Auswählen der App im Azure-Portal

1. Suchen Sie nach **App Services**, und wählen Sie diese Option aus.

   ![Screenshot: Auswählen von App Services](./media/app-service-web-tutorial-custom-domain/app-services.png)

1. Wählen Sie auf der Seite **App Services** den Namen Ihrer Azure-App aus.

   ![Screenshot: Navigation im Portal zu einer Azure-App](./media/app-service-web-tutorial-custom-domain/select-app.png)

    Die Verwaltungsseite der App Service-App wird angezeigt.

<a name="checkpricing" aria-hidden="true"></a>

#### <a name="check-the-pricing-tier"></a>Überprüfen des Tarifs

1. Scrollen Sie im linken Bereich der App-Seite zum Abschnitt **Einstellungen**, und wählen Sie **Hochskalieren (App Service-Plan)** aus.

   ![Screenshot: Menü „Hochskalieren (App Service-Plan)“](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

1. Der aktuelle Tarif der App wird durch einen blauen Rahmen hervorgehoben. Vergewissern Sie sich, dass sich die App nicht im Tarif **F1** befindet. Benutzerdefiniertes DNS wird im Tarif **F1** nicht unterstützt.

   ![Screenshot: Empfohlene Tarife](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

1. Wenn sich der App Service-Plan nicht im Tarif **F1** befindet, schließen Sie die Seite **Hochskalieren**, und fahren Sie mit [3. Abrufen der Verifizierungs-ID für eine Domäne](#3-get-a-domain-verification-id) fort.

<a name="scaleup" aria-hidden="true"></a>

#### <a name="scale-up-the-app-service-plan"></a>Hochskalieren des App Service-Plans

1. Wählen Sie einen der kostenpflichtigen Tarife aus (**D1**, **B1**, **B2**, **B3** oder einen beliebigen Tarif aus der Kategorie **Produktion**). Wählen Sie **Alle Optionen anzeigen** aus, um weitere Optionen anzuzeigen.

1. Wählen Sie **Übernehmen**.

   ![Screenshot: Überprüfen des Tarifs](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

   Wenn die unten angegebene Benachrichtigung angezeigt wird, ist der Skalierungsvorgang abgeschlossen.

   ![Screenshot: Bestätigung des Skalierungsvorgangs](./media/app-service-web-tutorial-custom-domain/scale-notification.png)


## <a name="3-get-a-domain-verification-id"></a>3. Abrufen der Verifizierungs-ID für eine Domäne

Wenn Sie Ihrer App eine benutzerdefinierte Domäne hinzufügen möchten, müssen Sie den Besitz der Domäne bestätigen, indem Sie bei Ihrem Domänenanbieter eine Verifizierungs-ID als TXT-Datensatz hinzufügen. 

1. Wählen Sie im linken Bereich Ihrer App-Seite die Option **Benutzerdefinierte Domänen** aus. 
1. Kopieren Sie auf der Seite **Benutzerdefinierte Domänen** die ID im Feld **Verifizierungs-ID für benutzerdefinierte Domänen** für den nächsten Schritt.

    ![Screenshot: ID im Feld „Verifizierungs-ID für benutzerdefinierte Domänen“](./media/app-service-web-tutorial-custom-domain/get-custom-domain-verification-id.png)

    > [!WARNING]
    > Durch das Hinzufügen von Domänenverifizierungs-IDs zu Ihrer benutzerdefinierten Domäne können verwaiste DNS-Einträge und Unterdomänenübernahmen verhindert werden. Benutzerdefinierte Domänen, die Sie zuvor ohne diese Überprüfungs-ID konfiguriert haben, sollten Sie vor dem gleichen Risiko schützen, indem Sie Ihrem DNS-Datensatz die Überprüfungs-ID hinzufügen. Weitere Informationen zu dieser allgemeinen Bedrohung mit hohem Schweregrad finden Sie unter [Verhindern verwaister DNS-Einträge und Vermeiden von Unterdomänenübernahmen](../security/fundamentals/subdomain-takeover.md).
    
<a name="info"></a>

3. **(Nur A-Eintrag)** Für die Zuordnung eines [A-Eintrags](https://en.wikipedia.org/wiki/List_of_DNS_record_types#A) benötigen Sie die externe IP-Adresse der App. Kopieren Sie auf der Seite **Benutzerdefinierte Domänen** den Wert der **IP-Adresse**.

   ![Screenshot: Navigation im Portal zu einer Azure-App](./media/app-service-web-tutorial-custom-domain/mapping-information.png)


## <a name="4-create-the-dns-records"></a>4. Erstellen der DNS-Einträge

1. Melden Sie sich bei der Website Ihres Domänenanbieters an.

    > [!NOTE]
    > Sie können Azure DNS verwenden, um DNS-Einträge für Ihre Domäne zu verwalten und einen benutzerdefinierten DNS-Namen für Azure App Service zu konfigurieren. Weitere Informationen finden Sie im [Tutorial: Hosten Ihrer Domäne in Azure DNS](../dns/dns-delegate-domain-azure-dns.md).

1. Suchen Sie die Seite für die Verwaltung von DNS-Einträgen. 

    > [!NOTE]
    > Da jeder Domänenanbieter eine eigene Benutzeroberfläche für DNS-Einträge hat, finden Sie Informationen dazu jeweils in der Dokumentation des Anbieters. Suchen Sie nach Bereichen der Website, die mit **Domänenname**, **DNS** oder **Namenserververwaltung** gekennzeichnet sind.
    >
    > Die Seite für DNS-Einträge erreichen Sie häufig in Ihren Kontoinformationen über einen Link wie **Eigene Domänen**. Navigieren Sie zu dieser Seite, und suchen Sie nach einem Link, der beispielsweise den Namen **Zonendatei**, **DNS-Einträge** oder **Erweiterte Konfiguration** hat.

   Der folgende Screenshot zeigt ein Beispiel für eine Seite mit DNS-Einträgen:

   ![Screenshot: Beispiel für eine Seite mit DNS-Einträgen](../../includes/media/app-service-web-access-dns-records-no-h/example-record-ui.png)

1. Wählen Sie **Hinzufügen** oder das entsprechende Widget aus, um einen Eintrag zu erstellen. 

1. Wählen Sie die Art des zu erstellenden Eintrags aus, und befolgen Sie die Anweisungen. Verwenden Sie für die Zuordnung eines benutzerdefinierten DNS-Namens zu App Service entweder einen [CNAME-Eintrag](https://en.wikipedia.org/wiki/CNAME_record) oder einen [A-Eintrag](https://en.wikipedia.org/wiki/List_of_DNS_record_types#A).

    > [!NOTE]
    > **Welcher Eintrag ausgewählt werden soll**
    > 
    > * Zum Zuordnen der Stammdomäne (z. B. `contoso.com`) verwenden Sie einen [A-Eintrag](https://en.wikipedia.org/wiki/List_of_DNS_record_types#A). Verwenden Sie für den Stammeintrag nicht den CNAME-Eintrag. (Weitere Informationen finden Sie in [RFC 1912, Abschnitt 2.4](https://datatracker.ietf.org/doc/html/rfc1912#section-2.4).)
    > * Verwenden Sie zum Zuordnen einer Unterdomäne (z. B. `www.contoso.com`) einen [CNAME-Eintrag](https://en.wikipedia.org/wiki/CNAME_record).
    > * Mit einem A-Eintrag können Sie der IP-Adresse der App zwar direkt eine Unterdomäne zuordnen, es ist jedoch möglich, dass sich [die IP-Adresse ändert](overview-inbound-outbound-ips.md#when-inbound-ip-changes). Der CNAME-Eintrag wird dagegen dem (weniger änderungsanfälligen) Standardhostnamen der App zugeordnet.
    > * Wenn Sie eine [Platzhalterdomäne](https://en.wikipedia.org/wiki/Wildcard_DNS_record) (z. B. `*.contoso.com`) zuordnen möchten, verwenden Sie einen CNAME-Eintrag.
    
# <a name="cname"></a>[CNAME](#tab/cname)

Erstellen Sie für eine Unterdomäne wie `www` in `www.contoso.com` zwei Einträge gemäß der folgenden Tabelle:

| Eintragstyp | Host | Wert | Kommentare |
| - | - | - |
| CNAME | `<subdomain>` (z. B. `www`) | `<app-name>.azurewebsites.net` | Die Domänenzuordnung selbst |
| TXT | `asuid.<subdomain>` (z. B. `asuid.www`) | [Die zuvor abgerufene Verifizierungs-ID](#3-get-a-domain-verification-id) | App Service greift auf den TXT-Eintrag `asuid.<subdomain>` zu, um den Besitz der benutzerdefinierten Domäne zu überprüfen. |

![Screenshot: Navigation im Portal zu einer Azure-App](./media/app-service-web-tutorial-custom-domain/cname-record.png)
    
# <a name="a"></a>[A](#tab/a)

- Erstellen Sie für eine Stammdomäne wie `contoso.com` zwei Einträge gemäß der folgenden Tabelle:

    | Eintragstyp | Host | Wert | Kommentare |
    | - | - | - |
    | Ein | `@` | IP-Adresse aus dem Schritt [Kopieren der IP-Adresse der App](#3-get-a-domain-verification-id) | Die Domänenzuordnung selbst (`@` stellt in der Regel die Stammdomäne dar.) |
    | TXT | `asuid` | [Die zuvor abgerufene Verifizierungs-ID](#3-get-a-domain-verification-id) | App Service greift auf den TXT-Eintrag `asuid.<subdomain>` zu, um den Besitz der benutzerdefinierten Domäne zu überprüfen. Verwenden Sie für die Stammdomäne `asuid`. |
    
    ![Screenshot: Seite mit DNS-Einträgen](./media/app-service-web-tutorial-custom-domain/a-record.png)
    
- Wenn Sie zum Hinzufügen einer Unterdomäne (etwa `www.contoso.com`) einen A-Eintrag anstelle eines empfohlenen CNAME-Eintrags verwenden möchten, sollten Ihr A-Eintrag und TXT-Eintrag stattdessen wie in der folgenden Tabelle aussehen:

    |Eintragstyp|Host|Wert|
    |--- |--- |--- |
    |Ein|\<subdomain\> (z. B. „www“)|IP-Adresse aus dem Schritt Kopieren der IP-Adresse der App|
    |TXT|asuid.\<subdomain\> (z. B. „asuid.www“)|Die zuvor abgerufene Verifizierungs-ID|
    
# <a name="wildcard-cname"></a>[Platzhalter (CNAME)](#tab/wildcard)

Erstellen Sie für eine Platzhalterdomäne wie `*` in `*.contoso.com` zwei Einträge gemäß der folgenden Tabelle:

| Eintragstyp | Host | Wert | Kommentare |
| - | - | - |
| CNAME | `*` | `<app-name>.azurewebsites.net` | Die Domänenzuordnung selbst |
| TXT | `asuid` | [Die zuvor abgerufene Verifizierungs-ID](#3-get-a-domain-verification-id) | App Service greift auf den TXT-Eintrag `asuid` zu, um den Besitz der benutzerdefinierten Domäne zu überprüfen. |

![Screenshot: Navigation zu einer Azure-App](./media/app-service-web-tutorial-custom-domain/cname-record-wildcard.png)
    
-----

> [!NOTE]
> Bei einigen Anbietern wie GoDaddy werden Änderungen an DNS-Einträgen erst wirksam, wenn Sie einen gesonderten Link **Änderungen speichern** wählen.

<a name="a" aria-hidden="true"></a>

<a name="enable-a" aria-hidden="true"></a>

<a name="wildcard" aria-hidden="true"></a>

<a name="cname" aria-hidden="true"></a>

## <a name="5-enable-the-mapping-in-your-app"></a>5. Aktivieren der Zuordnung in Ihrer App

1. Wählen Sie im linken Bereich der App-Seite im Azure-Portal die Option **Benutzerdefinierte Domänen** aus.

    ![Screenshot: Menü „Benutzerdefinierte Domänen“](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. Wählen Sie **Benutzerdefinierte Domäne hinzufügen**.

    ![Screenshot: Element zum Hinzufügen des Hostnamens](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

# <a name="cname"></a>[CNAME](#tab/cname)

3. Geben Sie den vollqualifizierten Domänennamen ein, für den Sie einen CNAME-Eintrag hinzugefügt haben, z.B. `www.contoso.com`.

1. Wählen Sie **Überprüfen** aus. Die Seite **Benutzerdefinierte Domäne hinzufügen** wird angezeigt.

1. Stellen Sie sicher, dass der **Typ des Hostnamenseintrags** auf **CNAME (www\.beispiel.com oder eine beliebige Unterdomäne)** festgelegt ist. Wählen Sie **Benutzerdefinierte Domäne hinzufügen**.

    ![Screenshot: Schaltfläche „Benutzerdefinierte Domäne hinzufügen“](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

    Unter Umständen dauert es eine Weile, bis die neue benutzerdefinierte Domäne auf der Seite **Benutzerdefinierte Domänen** der App angezeigt wird. Aktualisieren Sie den Browser, um die Daten zu aktualisieren.

    ![Screenshot: Hinzufügen des CNAME-Eintrags](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

    > [!NOTE]
    > Eine Warnbezeichnung für Ihre benutzerdefinierte Domäne bedeutet, dass diese noch nicht an ein TLS-/SSL-Zertifikat gebunden ist. Für alle HTTPS-Anforderungen von einem Browser an Ihre benutzerdefinierte Domäne wird abhängig vom Browser ein Fehler oder eine Warnung angezeigt. Weitere Informationen zum Hinzufügen einer TLS-/SSL-Bindung finden Sie unter [Schützen eines benutzerdefinierten DNS-Namens mit einer TLS-/SSL-Bindung in Azure App Service](configure-ssl-bindings.md).

    Wenn Sie einen Schritt ausgelassen haben oder Ihnen zu einem früheren Zeitpunkt ein Tippfehler unterlaufen ist, wird am unteren Rand der Seite ein Überprüfungsfehler angezeigt.

    ![Screenshot: Überprüfungsfehler](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

# <a name="a"></a>[A](#tab/a)

3. Geben Sie den vollqualifizierten Domänennamen ein, für den Sie den A-Eintrag konfiguriert haben, z.B. `contoso.com`. 

1. Wählen Sie **Überprüfen** aus. Die Seite **Benutzerdefinierte Domäne hinzufügen** wird angezeigt.

1. Stellen Sie sicher, dass die Option **Typ des Hostnamenseintrags** auf **A-Datensatz (beispiel.com)** festgelegt ist. Wählen Sie **Benutzerdefinierte Domäne hinzufügen**.

    ![Screenshot: Hinzufügen eines DNS-Namens zur App](./media/app-service-web-tutorial-custom-domain/validate-domain-name.png)

    Unter Umständen dauert es eine Weile, bis die neue benutzerdefinierte Domäne auf der Seite **Benutzerdefinierte Domänen** der App angezeigt wird. Aktualisieren Sie den Browser, um die Daten zu aktualisieren.

    ![Screenshot: Hinzufügen eines A-Eintrags](./media/app-service-web-tutorial-custom-domain/a-record-added.png)

    > [!NOTE]
    > Eine Warnbezeichnung für Ihre benutzerdefinierte Domäne bedeutet, dass diese noch nicht an ein TLS-/SSL-Zertifikat gebunden ist. Für alle HTTPS-Anforderungen von einem Browser an Ihre benutzerdefinierte Domäne wird abhängig vom Browser ein Fehler oder eine Warnung angezeigt. Weitere Informationen zum Hinzufügen einer TLS-/SSL-Bindung finden Sie unter [Schützen eines benutzerdefinierten DNS-Namens mit einer TLS-/SSL-Bindung in Azure App Service](configure-ssl-bindings.md).
    
    Wenn Sie einen Schritt ausgelassen haben oder Ihnen zu einem früheren Zeitpunkt ein Tippfehler unterlaufen ist, wird am unteren Rand der Seite ein Überprüfungsfehler angezeigt.
    
    ![Screenshot: Überprüfungsfehler](./media/app-service-web-tutorial-custom-domain/verification-error.png)
    
# <a name="wildcard-cname"></a>[Platzhalter (CNAME)](#tab/wildcard)

3. Geben Sie einen vollqualifizierten Domänennamen ein, der zur Platzhalterdomäne passt. Für `*.contoso.com` können Sie beispielsweise `sub1.contoso.com`, `sub2.contoso.com`, `*.contoso.com` oder eine beliebige andere Zeichenfolge verwenden, die dem Platzhaltermuster entspricht. Wählen Sie anschließend **Überprüfen** aus.

    Die Schaltfläche **Benutzerdefinierte Domäne hinzufügen** ist aktiviert.

1. Stellen Sie sicher, dass der **Typ des Hostnamenseintrags** auf **CNAME-Eintrag (www\.beispiel.com oder eine beliebige Unterdomäne)** festgelegt ist. Wählen Sie **Benutzerdefinierte Domäne hinzufügen**.

    ![Screenshot: Hinzufügen eines DNS-Namens zur App](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname-wildcard.png)

    Unter Umständen dauert es eine Weile, bis die neue benutzerdefinierte Domäne auf der Seite **Benutzerdefinierte Domänen** der App angezeigt wird. Aktualisieren Sie den Browser, um die Daten zu aktualisieren.

    > [!NOTE]
    > Eine Warnbezeichnung für Ihre benutzerdefinierte Domäne bedeutet, dass diese noch nicht an ein TLS-/SSL-Zertifikat gebunden ist. Für alle HTTPS-Anforderungen von einem Browser an Ihre benutzerdefinierte Domäne wird abhängig vom Browser ein Fehler oder eine Warnung angezeigt. Weitere Informationen zum Hinzufügen einer TLS-/SSL-Bindung finden Sie unter [Schützen eines benutzerdefinierten DNS-Namens mit einer TLS-/SSL-Bindung in Azure App Service](configure-ssl-bindings.md).

-----


## <a name="6-test-in-a-browser"></a>6. Testen in einem Browser

Navigieren Sie zu den DNS-Namen, die Sie zuvor konfiguriert haben.

![Screenshot: Navigation zu einer Azure-App](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

<a name="resolve-404-not-found" aria-hidden="true"></a>

Wenn Sie beim Navigieren zur URL Ihrer benutzerdefinierten Domäne einen HTTP 404-Fehler (Nicht gefunden) erhalten, sind dies die beiden häufigsten Ursachen:

* Der konfigurierten benutzerdefinierten Domäne fehlt ein A-Eintrag oder ein CNAME-Eintrag. Möglicherweise haben Sie den DNS-Eintrag gelöscht, nachdem Sie die Zuordnung in Ihrer App aktiviert haben. Überprüfen Sie mithilfe eines <a href="https://www.nslookup.io/">DNS-Onlinesuchtools</a>, ob die DNS-Einträge ordnungsgemäß konfiguriert sind.
* Im Browserclient ist die alte IP-Adresse Ihrer Domäne zwischengespeichert. Leeren Sie den Cache, und testen Sie die DNS-Auflösung erneut. Auf einem Windows-Computer können Sie den Cache mithilfe von `ipconfig /flushdns` leeren.


## <a name="migrate-an-active-domain"></a>Migrieren einer aktiven Domäne

Informationen zum Migrieren einer Livewebsite und ihres DNS-Domänennamens zu App Service ohne Ausfallzeiten finden Sie unter [Migrieren einer aktiven benutzerdefinierten Domäne zu Azure App Service](manage-custom-dns-migrate-domain.md).

<a name="virtualdir" aria-hidden="true"></a>

## <a name="redirect-to-a-custom-directory"></a>Umleitung zu einem benutzerdefinierten Verzeichnis

> [!NOTE]
> App Service leitet Webanforderungen standardmäßig an das Stammverzeichnis des App-Codes weiter. Manche Webframeworks starten jedoch nicht im Stammverzeichnis. [Laravel](https://laravel.com/) startet beispielsweise im Unterverzeichnis `public`. Um das DNS-Beispiel für `contoso.com` fortzusetzen: Auf eine solche App kann zwar unter `http://contoso.com/public` zugegriffen werden, normalerweise empfiehlt es sich aber, `http://contoso.com` stattdessen an das Verzeichnis `public` weiterzuleiten.

Hierbei handelt es sich zwar um ein gängiges Szenario, aber eigentlich nicht um eine benutzerdefinierte DNS-Zuordnung, da hier das virtuelle Verzeichnis innerhalb Ihrer App angepasst wird.

1. Wählen Sie im linken Bereich Ihrer Web-App-Seite die Option **Anwendungseinstellungen** aus.

1. Das virtuelle Stammverzeichnis `/` am unteren Seitenrand verweist standardmäßig auf `site\wwwroot` (also auf das Stammverzeichnis Ihres App-Codes). Ändern Sie die Angabe, um stattdessen beispielsweise auf `site\wwwroot\public` zu verweisen, und speichern Sie die Änderung.

    ![Screenshot: Anpassen eines virtuellen Verzeichnisses](./media/app-service-web-tutorial-custom-domain/customize-virtual-directory.png)

1. Überprüfen Sie nach Abschluss des Vorgangs das Ergebnis, indem Sie im Browser zum Stammpfad Ihrer App navigieren (beispielsweise zu `http://contoso.com` oder `http://<app-name>.azurewebsites.net`).


## <a name="automate-with-scripts"></a>Automatisieren mit Skripts

Durch die [Azure CLI](/cli/azure/install-azure-cli) oder durch [Azure PowerShell](/powershell/azure/) können Sie mithilfe von Skripts die Verwaltung von benutzerdefinierten Domänen automatisieren.

#### <a name="azure-cli"></a>Azure-Befehlszeilenschnittstelle

Mit dem folgenden Befehl wird ein konfigurierter benutzerdefinierter DNS-Name zu einer App Service-App hinzugefügt.

```azurecli 
az webapp config hostname add \
    --webapp-name <app-name> \
    --resource-group <resource_group_name> \
    --hostname <fully_qualified_domain_name>
``` 

Weitere Informationen finden Sie unter [Zuordnen einer benutzerdefinierten Domäne zu einer Web-App](scripts/cli-configure-custom-domain.md).

#### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Mit dem folgenden Befehl wird ein konfigurierter benutzerdefinierter DNS-Name zu einer App Service-App hinzugefügt.

```powershell  
Set-AzWebApp `
    -Name <app-name> `
    -ResourceGroupName <resource_group_name> ` 
    -HostNames @("<fully_qualified_domain_name>","<app-name>.azurewebsites.net")
```

Weitere Informationen finden Sie unter [Zuordnen einer benutzerdefinierten Domäne zu einer Web-App](scripts/powershell-configure-custom-domain.md).


## <a name="next-steps"></a>Nächste Schritte

Fahren Sie mit dem nächsten Tutorial fort, um sich über das Binden eines benutzerdefinierten TLS-/SSL-Zertifikats an eine Web-App zu informieren:

> [!div class="nextstepaction"]
> [Schützen eines benutzerdefinierten DNS-Namens mit einer TLS-/SSL-Bindung in Azure App Service](configure-ssl-bindings.md)

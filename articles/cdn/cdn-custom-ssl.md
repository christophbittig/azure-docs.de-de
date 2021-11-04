---
title: 'Tutorial: Konfigurieren von HTTPS in einer benutzerdefinierten Azure CDN-Domäne'
description: In diesem Tutorial wird beschrieben, wie Sie HTTPS in Ihrer benutzerdefinierten Azure CDN-Endpunktdomäne aktivieren und deaktivieren.
services: cdn
author: duongau
ms.service: azure-cdn
ms.topic: tutorial
ms.date: 03/26/2021
ms.author: duau
ms.custom: mvc
ms.openlocfilehash: d6a56aecaf8110118081e8c79e86bae5c277d992
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131458682"
---
# <a name="tutorial-configure-https-on-an-azure-cdn-custom-domain"></a>Tutorial: Konfigurieren von HTTPS in einer benutzerdefinierten Azure CDN-Domäne

In diesem Tutorial erfahren Sie, wie Sie das HTTPS-Protokoll für eine benutzerdefinierte Domäne aktivieren, die einem Azure CDN-Endpunkt zugeordnet ist. 

Durch das HTTPS-Protokoll in Ihrer benutzerdefinierten Domäne (Beispiel: https:\//www.contoso.com) wird sichergestellt, dass Ihre vertraulichen Daten sicher per TLS/SSL übertragen werden. Bei Verwendung einer HTTPS-Verbindung überprüft der Webbrowser das Zertifikat der Website. Dadurch wird sichergestellt, dass es von einer legitimen Zertifizierungsstelle stammt. Dieser Prozess sorgt für Sicherheit und schützt Ihre Webanwendungen vor Angriffen.

Azure CDN unterstützt HTTPS standardmäßig für einen CDN-Endpunkt-Hostnamen. Wenn Sie also beispielsweise einen CDN-Endpunkt erstellen (z.B. „https:\//contoso.azureedge.net“), wird HTTPS automatisch aktiviert.  

Zu den wichtigsten Attributen des benutzerdefinierten HTTPS-Features zählen unter anderem folgende:

- Keine zusätzlichen Kosten: Zertifikatabruf und -verlängerung sind kostenlos, und für HTTPS-Datenverkehr fallen keine zusätzlichen Kosten an. Sie zahlen nur für den ausgehenden Datenverkehr des CDNs (nach GB).

- Unkomplizierte Aktivierung: Über das [Azure-Portal](https://portal.azure.com) ist die Bereitstellung mit einem einzelnen Klick möglich. Das Feature kann aber auch per REST-API oder mithilfe anderer Entwicklertools aktiviert werden.

- Umfassende Zertifikatverwaltung: 
    * Die gesamte Zertifikatbeschaffung und -verwaltung wird für Sie erledigt. 
    * Zertifikate werden automatisch bereitgestellt und verlängert, bevor sie ablaufen.

In diesem Tutorial lernen Sie Folgendes:
> [!div class="checklist"]
> - Aktivieren des HTTPS-Protokolls für Ihre benutzerdefinierte Domäne
> - Verwenden eines CDN-verwalteten Zertifikats 
> - Verwenden Ihres eigenen Zertifikats
> - Überprüfen der Domäne
> - Deaktivieren des HTTPS-Protokolls für Ihre benutzerdefinierte Domäne

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)] 

Erstellen Sie ein CDN-Profil und mindestens einen CDN-Endpunkt, bevor Sie die Schritte in diesem Tutorial ausführen. Weitere Informationen finden Sie unter [Quickstart: Erstellen eines Azure CDN-Profils und -Endpunkts](cdn-create-new-endpoint.md).

Ordnen Sie Ihrem CDN-Endpunkt eine benutzerdefinierte Azure CDN-Domäne zu. Weitere Informationen finden Sie im [Tutorial: Hinzufügen einer benutzerdefinierten Domäne zum Azure CDN-Endpunkt](cdn-map-content-to-custom-domain.md).

> [!IMPORTANT]
> CDN-verwaltete Zertifikate sind für Stamm- oder Apex-Domänen nicht verfügbar. Wenn Ihre benutzerdefinierte Azure CDN-Domäne eine Stamm- oder Apex-Domäne ist, müssen Sie die Funktion „Bereitstellen eines eigenen Zertifikats“ verwenden. 
>

---

## <a name="tlsssl-certificates"></a>TLS-/SSL-Zertifikate

Wenn Sie HTTPS für eine benutzerdefinierte Azure CDN-Domäne aktivieren möchten, müssen Sie ein TLS/SSL-Zertifikat verwenden. Sie haben die Wahl zwischen einem von Azure CDN verwalteten Zertifikat oder einem eigenen Zertifikat.

# <a name="option-1-default-enable-https-with-a-cdn-managed-certificate"></a>[Option 1 (Standard): Aktivieren von HTTPS mit einem CDN-verwalteten Zertifikat](#tab/option-1-default-enable-https-with-a-cdn-managed-certificate)

Azure CDN übernimmt Zertifikatverwaltungsaufgaben wie Beschaffung und Verlängerung. Der Prozess wird umgehend nach der Aktivierung des Features gestartet. 

Wenn die benutzerdefinierte Domäne bereits dem CDN-Endpunkt zugeordnet ist, ist keine weitere Aktion erforderlich. Das Azure CDN durchläuft die Schritte und schließt Ihre Anforderung automatisch ab.

Sollte Ihre benutzerdefinierte Domäne an anderer Stelle zugeordnet sein, bestätigen Sie per E-Mail, dass Sie der Besitzer der Domäne sind.

Um HTTPS für eine benutzerdefinierte Domäne zu aktivieren, führen Sie die folgenden Schritte aus:

1. Rufen Sie das [Azure-Portal](https://portal.azure.com) auf, um ein von Ihrer Azure CDN-Instanz verwaltetes Zertifikat zu ermitteln. Suchen Sie nach **CDN-Profile**, und wählen Sie diese Option aus. 

2. Wählen Sie Ihr Profil aus:
    * **Azure CDN Standard von Microsoft**
    * **Azure CDN Standard von Akamai**
    * **Azure CDN Standard von Verizon**
    * **Azure CDN Premium von Verizon**

3. Wählen Sie in der Liste mit den CDN-Endpunkten den Endpunkt aus, der Ihre benutzerdefinierte Domäne enthält.

    ![Liste mit Endpunkten](./media/cdn-custom-ssl/cdn-select-custom-domain-endpoint.png)

    Die Seite **Endpunkt** wird angezeigt.

4. Wählen Sie in der Liste mit den benutzerdefinierten Domänen die benutzerdefinierte Domäne aus, für die Sie HTTPS aktivieren möchten.

    ![Screenshot, der die Seite „Benutzerdefinierte Domäne“ mit der Option „Eigenes Zertifikat verwenden“ zeigt.](./media/cdn-custom-ssl/cdn-custom-domain.png)

    Die Seite **Benutzerdefinierte Domäne** wird angezeigt.

5. Wählen Sie unter „Zertifikatverwaltungstyp“ die Option **CDN verwaltet** aus.

6. Klicken Sie auf **Ein**, um HTTPS zu aktivieren.

    ![HTTPS-Status benutzerdefinierter Domänen](./media/cdn-custom-ssl/cdn-select-cdn-managed-certificate.png)

7. Fahren Sie mit [Überprüfen der Domäne](#validate-the-domain) fort.


# <a name="option-2-enable-https-with-your-own-certificate"></a>[Option 2: Aktivieren von HTTPS mit Ihrem eigenen Zertifikat](#tab/option-2-enable-https-with-your-own-certificate)

> [!IMPORTANT]
> Diese Option steht nur in den Profilen **Azure CDN von Microsoft** und **Azure CDN von Verizon** zur Verfügung. 
>
 
Sie können das HTTPS-Feature mit Ihrem eigenen Zertifikat aktivieren. Dabei erfolgt eine Integration in Azure Key Vault, was eine sichere Speicherung Ihrer Zertifikate ermöglicht. Azure Front Door nutzt diesen sicheren Mechanismus zum Abrufen Ihres Zertifikats, und es sind einige zusätzliche Schritte erforderlich. Wenn Sie Ihr TLS/SSL-Zertifikat erstellen, müssen Sie eine vollständige Zertifikatkette mit einer zulässigen Zertifizierungsstelle erstellen, die in der [Microsoft-Liste der vertrauenswürdigen Zertifizierungsstellen](https://ccadb-public.secure.force.com/microsoft/IncludedCACertificateReportForMSFT) enthalten ist. Bei Verwendung einer unzulässigen Zertifizierungsstelle wird Ihre Anforderung abgelehnt.  Wenn ein Zertifikat ohne vollständige Kette präsentiert wird, funktionieren die in diesem Zertifikat enthaltenen Anforderungen nicht wie erwartet. Für Azure CDN von Verizon wird eine beliebige gültige Zertifizierungsstelle akzeptiert.

### <a name="prepare-your-azure-key-vault-account-and-certificate"></a>Vorbereiten Ihres Azure Key Vault-Kontos und Ihres Zertifikats
 
1. Azure Key Vault: Sie benötigen ein aktives Azure Key Vault-Konto. Dieses muss zu dem gleichen Abonnement gehören wie das Azure CDN-Profil und die CDN-Endpunkte, für die Sie benutzerdefiniertes HTTPS aktivieren möchten. Erstellen Sie bei Bedarf ein Azure Key Vault-Konto.
 
2. Azure Key Vault-Zertifikate: Falls Sie über ein Zertifikat verfügen, laden Sie es direkt in Ihr Azure Key Vault-Konto hoch. Sollten Sie über kein Zertifikat verfügen, erstellen Sie direkt über Azure Key Vault ein neues Zertifikat.

> [!NOTE]
> Das Zertifikat muss über eine vollständige Zertifikatkette mit Blatt- und Zwischenzertifikaten verfügen, und die Stammzertifizierungsstelle muss in der [Microsoft-Liste der vertrauenswürdigen Zertifizierungsstellen](https://ccadb-public.secure.force.com/microsoft/IncludedCACertificateReportForMSFT) enthalten sein.

### <a name="register-azure-cdn"></a>Registrieren von Azure CDN

Registrieren Sie Azure CDN über PowerShell als App in Ihrem Azure Active Directory.

1. Installieren Sie bei Bedarf [Azure PowerShell](/powershell/azure/install-az-ps) auf Ihrem lokalen Computer.

2. Führen Sie in PowerShell den folgenden Befehl aus:

     `New-AzADServicePrincipal -ApplicationId "205478c0-bd83-4e1b-a9d6-db63a3e1e1c8"`
    > [!NOTE]
    > **205478c0-bd83-4e1b-a9d6-db63a3e1e1c8** ist der Dienstprinzipal für **Microsoft.AzureFrontDoor-Cdn**.

    ```bash  
    New-AzADServicePrincipal -ApplicationId "205478c0-bd83-4e1b-a9d6-db63a3e1e1c8"

    Secret                : 
    ServicePrincipalNames : {205478c0-bd83-4e1b-a9d6-db63a3e1e1c8, 
                                https://microsoft.onmicrosoft.com/033ce1c9-f832-4658-b024-ef1cbea108b8}
    ApplicationId         : 205478c0-bd83-4e1b-a9d6-db63a3e1e1c8
    ObjectType            : ServicePrincipal
    DisplayName           : Microsoft.AzureFrontDoor-Cdn
    Id                    : c87be08f-686a-4d9f-8ef8-64707dbd413e
    Type                  :
    ```
### <a name="grant-azure-cdn-access-to-your-key-vault"></a>Gewähren von Zugriff auf Ihren Schlüsseltresor für Azure CDN
 
Gewähren Sie Azure CDN Berechtigungen für den Zugriff auf die Zertifikate (Geheimnisse) in Ihrem Azure Key Vault-Konto.

1. Wählen Sie in Ihrem Schlüsseltresor im Abschnitt **Einstellungen** die Option **Zugriffsrichtlinien** aus. Wählen Sie im rechten Bereich die Option **+ Zugriffsrichtlinie hinzufügen** aus:

    :::image type="content" source="./media/cdn-custom-ssl/cdn-new-access-policy.png" alt-text="Erstellen einer Key Vault-Zugriffsrichtlinie für CDN" border="true":::

2. Wählen Sie auf der Seite **Zugriffsrichtlinie hinzufügen** neben **Prinzipal auswählen** die Option **Nichts ausgewählt** aus. Geben Sie auf der Seite **Prinzipal** die Zeichenfolge **205478c0-bd83-4e1b-a9d6-db63a3e1e1c8** ein. Wählen Sie **Microsoft.AzureFrontdoor-Cdn** aus.  Wählen Sie **Auswählen** aus:

3. Suchen Sie unter **Prinzipal auswählen** nach **205478c0-bd83-4e1b-a9d6-db63a3e1e1c8**, und wählen Sie **Microsoft.AzureFrontDoor-Cdn** aus. Klicken Sie auf **Auswählen**.

    :::image type="content" source="./media/cdn-custom-ssl/cdn-access-policy-settings.png" alt-text="Auswählen des Dienstprinzipals von Azure CDN" border="true":::
    
4. Wählen Sie **Zertifikatberechtigungen** aus. Aktivieren Sie die Kontrollkästchen für **Abrufen** und **Auflisten**, um CDN Berechtigungen zum Abrufen und Auflisten der Zertifikate zu erteilen.

5. Wählen Sie **Geheimnisberechtigungen** aus. Aktivieren Sie die Kontrollkästchen für **Abrufen** und **Auflisten**, um CDN Berechtigungen zum Abrufen und Auflisten der Geheimnisse zu erteilen:

    :::image type="content" source="./media/cdn-custom-ssl/cdn-vault-permissions.png" alt-text="Auswählen von Key Vault-Berechtigungen für CDN" border="true":::

6. Wählen Sie **Hinzufügen**. 

> [!NOTE]
> Das Azure CDN kann nun auf diesen Schlüsseltresor und auf die darin gespeicherten Zertifikate (Geheimnisse) zugreifen. Alle in diesem Abonnement erstellten CDN-Instanzen haben Zugriff auf die Zertifikate in diesem Schlüsseltresor.
 
### <a name="select-the-certificate-for-azure-cdn-to-deploy"></a>Auswählen des bereitzustellenden Zertifikats für Azure CDN
 
1. Kehren Sie zum Azure CDN-Portal zurück, und wählen Sie das Profil und den CDN-Endpunkt aus, für die Sie benutzerdefiniertes HTTPS aktivieren möchten. 

2. Wählen Sie in der Liste mit den benutzerdefinierten Domänen die benutzerdefinierte Domäne aus, für die Sie HTTPS aktivieren möchten.

    Die Seite **Benutzerdefinierte Domäne** wird angezeigt.

3. Wählen Sie unter „Zertifikatverwaltungstyp“ die Option **Eigenes Zertifikat verwenden** aus. 

    :::image type="content" source="./media/cdn-custom-ssl/cdn-configure-your-certificate.png" alt-text="Screenshot: Konfigurieren des Zertifikats für den CDN-Endpunkt":::

4. Wählen Sie einen Schlüsseltresor, ein Zertifikat/Geheimnis und eine Zertifikat-/Geheimnisversion aus.

    Das Azure CDN listet folgende Informationen auf: 
    - Die Schlüsseltresorkonten für Ihre Abonnement-ID 
    - Die Zertifikate/Geheimnisse unter dem ausgewählten Schlüsseltresor 
    - Die verfügbaren Zertifikat-/Geheimnisversionen
 
    > [!NOTE]
    > Legen Sie die Zertifikat-/Geheimnisversion auf „Neueste“ fest, damit das Zertifikat automatisch zur neuesten Version rotiert wird, wenn eine neuere Version des Zertifikats in Ihrer Key Vault-Instanz verfügbar ist. Wenn eine bestimmte Version ausgewählt ist, müssen Sie die neue Version für die Zertifikatrotation manuell erneut auswählen. Es dauert bis zu 24 Stunden, bis die neue Version des Zertifikats/Geheimnisses bereitgestellt wird. 
   
5. Klicken Sie auf **Ein**, um HTTPS zu aktivieren.
  
6. Wenn Sie Ihr eigenes Zertifikat verwenden, ist keine Domänenüberprüfung erforderlich. Fahren Sie mit [Warten auf die Weitergabe](#wait-for-propagation) fort.

---

## <a name="validate-the-domain"></a>Überprüfen der Domäne

Fahren Sie bei Verwendung einer benutzerdefinierten Domäne, die Ihrem benutzerdefinierten Endpunkt mit einem CNAME-Eintrag zugeordnet ist, oder bei Verwendung Ihres eigenen Zertifikats mit [Benutzerdefinierte Domäne ist Ihrem CDN-Endpunkt mit einem CNAME-Eintrag zugeordnet](#custom-domain-is-mapped-to-your-cdn-endpoint-by-a-cname-record) fort. 

Sollte der CNAME-Eintrag für Ihren Endpunkt nicht mehr vorhanden sein oder die Unterdomäne „cdnverify“ enthalten, fahren Sie mit [Benutzerdefinierte Domäne ist Ihrem CDN-Endpunkt nicht zugeordnet](#custom-domain-isnt-mapped-to-your-cdn-endpoint) fort.

### <a name="custom-domain-is-mapped-to-your-cdn-endpoint-by-a-cname-record"></a>Benutzerdefinierte Domäne ist Ihrem CDN-Endpunkt mit einem CNAME-Eintrag zugeordnet

Wenn Sie Ihrem Endpunkt eine benutzerdefinierte Domäne hinzugefügt haben, haben Sie einen CNAME-Eintrag in der DNS-Domänenregistrierungsstelle erstellt, der dem Hostnamen Ihres CDN-Endpunkts zugeordnet ist. 

Wenn dieser CNAME-Eintrag noch vorhanden ist und die Unterdomäne „cdnverify“ nicht enthält, wird er von der DigiCert-Zertifizierungsstelle zur automatischen Überprüfung des Besitzes Ihrer benutzerdefinierten Domäne verwendet. 

Wenn Sie Ihr eigenes Zertifikat verwenden, ist keine Domänenüberprüfung erforderlich.

Ihr CNAME-Eintrag muss das folgende Format aufweisen:

* *Name* ist Ihr benutzerdefinierter Domänenname.
* *Wert* ist der Hostname Ihres CDN-Endpunkts.

| Name            | type  | Wert                 |
|-----------------|-------|-----------------------|
| <www.contoso.com> | CNAME | contoso.azureedge.net |

Weitere Informationen über CNAME-Einträge finden Sie unter [Erstellen Sie die CNAME-DNS-Einträge](./cdn-map-content-to-custom-domain.md).

Wenn Ihr CNAME-Eintrag im korrekten Format vorliegt, überprüft DigiCert automatisch Ihren benutzerdefinierten Domänennamen und erstellt ein Zertifikat für Ihre Domäne. DigiCert sendet Ihnen keine Bestätigungs-E-Mail, und Sie müssen Ihre Anforderung nicht genehmigen. Das Zertifikat ist ein Jahr gültig und wird automatisch verlängert, bevor es abläuft. Fahren Sie mit [Warten auf die Weitergabe](#wait-for-propagation) fort. 

Die automatische Überprüfung dauert normalerweise einige Stunden. Öffnen Sie ein Supportticket, falls Ihre Domäne nicht innerhalb von 24 Stunden validiert wurde.

>[!NOTE]
>Wenn Sie über einen CAA-Datensatz (Certificate Authority Authorization) bei Ihrem DNS-Anbieter verfügen, muss dieser DigiCert als gültige Zertifizierungsstelle enthalten. Ein CAA-Datensatz ermöglicht es Domänenbesitzern, ihrem jeweiligen DNS-Anbieter mitzuteilen, welche Zertifizierungsstellen zum Ausstellen von Zertifikaten für ihre Domäne autorisiert sind. Wenn eine Zertifizierungsstelle einen Auftrag für ein Zertifikat für eine Domäne empfängt, für die ein CAA-Datensatz vorliegt und die Zertifizierungsstelle nicht als autorisierter Aussteller angegeben ist, darf sie das Zertifikat für die Domäne oder Unterdomäne nicht ausstellen. Informationen zum Verwalten von CAA-Einträgen finden Sie unter [Manage CAA records](https://support.dnsimple.com/articles/manage-caa-record/) (Verwalten von CAA-Einträgen). Ein Tool für CAA-Einträge finden Sie unter [CAA Record Helper](https://sslmate.com/caa/) (Hilfsprogramm für CAA-Einträge).

### <a name="custom-domain-isnt-mapped-to-your-cdn-endpoint"></a>Benutzerdefinierte Domäne ist Ihrem CDN-Endpunkt nicht zugeordnet

>[!NOTE]
>Bei der Verwendung von **Azure CDN von Akamai** muss der folgende CNAME-Eintrag eingerichtet werden, um die automatisierte Domänenüberprüfung zu aktivieren: „_acme-challenge.&lt;Hostname der benutzerdefinierten Domäne&gt; -> CNAME -> &lt;Hostname der benutzerdefinierten Domäne&gt;.ak-acme-challenge.azureedge.net“

Wenn der CNAME-Eintrag die cdnverify-Unterdomäne enthält, folgen Sie den weiteren Anweisungen in diesem Schritt.

DigiCert sendet zur Überprüfung eine E-Mail an die folgenden E-Mail-Adressen. Vergewissern Sie sich, dass Sie die Bestätigung direkt über eine der folgenden Adressen vornehmen können:

* **admin@your-domain-name.com** 
* **administrator@your-domain-name.com** 
* **webmaster@your-domain-name.com** 
* **hostmaster@your-domain-name.com**  
* **postmaster@your-domain-name.com**  

Nach wenigen Minuten sollten Sie eine E-Mail erhalten, in der Sie die Anforderung genehmigen können. Falls Sie einen Spamfilter verwenden, fügen Sie verification@digicert.com der Zulassungsliste hinzu. Sollten Sie nach 24 Stunden noch keine E-Mail erhalten haben, setzen Sie sich mit dem Microsoft-Support in Verbindung.
    
![E-Mail zur Bestätigung der Domäne](./media/cdn-custom-ssl/domain-validation-email.png)

Wenn Sie den Genehmigungslink auswählen, werden Sie zum folgenden Onlineformular für die Genehmigung weitergeleitet: 
    
![Formular zur Bestätigung der Domäne](./media/cdn-custom-ssl/domain-validation-form.png)

Befolgen Sie die Anweisungen im Formular. Sie haben zwei Möglichkeiten zur Genehmigung:

- Sie können alle zukünftigen Aufträge genehmigen, die über dasselbe Konto und für dieselbe Stammdomäne (beispielsweise „contoso.com“) getätigt werden. Dies empfiehlt sich, wenn Sie planen, weitere benutzerdefinierte Domänen für die gleiche Stammdomäne hinzuzufügen.

- Sie können lediglich den spezifischen Hostnamen aus dieser Anforderung genehmigen. Für spätere Anforderungen ist eine weitere Genehmigung erforderlich.

Nach der Genehmigung führt DigiCert die Erstellung des Zertifikats für Ihren benutzerdefinierten Domänennamen durch. Das Zertifikat ist ein Jahr gültig und wird automatisch verlängert, bevor es abläuft.

## <a name="wait-for-propagation"></a>Warten auf die Weitergabe

Nach der Überprüfung des Domänennamens dauert es maximal 6 bis 8 Stunden, bis das HTTPS-Feature für die benutzerdefinierte Domäne aktiviert wird. Nach Abschluss des Prozesses ändert sich im Azure-Portal der HTTPS-Status für benutzerdefinierte Domänen in **Ein**. Die vier Schritte im Dialogfeld der benutzerdefinierten Domäne werden als abgeschlossen markiert. Die benutzerdefinierte Domäne ist jetzt bereit zur Verwendung von HTTPS.

![Dialogfeld „HTTPS aktivieren“](./media/cdn-custom-ssl/cdn-enable-custom-ssl-complete.png)

### <a name="operation-progress"></a>Vorgangsstatus

Die folgende Tabelle zeigt den Status des Vorgangs zum Aktivieren von HTTPS. Nach der Aktivierung von HTTPS werden im Dialogfeld der benutzerdefinierten Domäne vier Schritte angezeigt. Beim Durchlaufen der einzelnen Schritte werden weitere Details zum jeweiligen Teilschritt angezeigt. Nicht alle diese Teilschritte werden vorkommen. Nachdem ein Schritt erfolgreich abgeschlossen wurde, wird neben diesem ein grünes Häkchen angezeigt. 

| Vorgangsschritt | Details zum Teilschritt des Vorgangs | 
| --- | --- |
| 1: Übermitteln der Anforderung | Übermitteln der Anforderung |
| | Die HTTPS-Anforderung wird übermittelt. |
| | Die HTTPS-Anforderung wurde erfolgreich übermittelt. |
| 2: Überprüfen der Domäne | Die Domäne wird automatisch überprüft, wenn sie per CNAME dem CDN-Endpunkt zugeordnet ist. Andernfalls wird an die E-Mail-Adresse, die im Registrierungsdatensatz (WHOIS-Registrant) Ihrer Domäne angegeben ist, eine Überprüfungsanforderung gesendet.|
| | Der Besitz der Domäne wurde erfolgreich bestätigt. |
| | Die Überprüfungsanforderung für den Domänenbesitz ist abgelaufen (Kunde hat wahrscheinlich nicht innerhalb von 6 Tagen reagiert). HTTPS wird für Ihre Domäne nicht aktiviert. * |
| | Die Überprüfungsanforderung für den Domänenbesitz wurde vom Kunden zurückgewiesen. HTTPS wird für Ihre Domäne nicht aktiviert. * |
| 3: Zertifikatbereitstellung | Die Zertifizierungsstelle stellt zurzeit das Zertifikat aus, das zum Aktivieren von HTTPS für Ihre Domäne erforderlich ist. |
| | Das Zertifikat wurde ausgegeben und wird derzeit im CDN-Netzwerk bereitgestellt. Dies kann bis zu sechs Stunden dauern. |
| | Das Zertifikat wurde erfolgreich im CDN-Netzwerk bereitgestellt. |
| 4: Abschließen | HTTPS wurde in Ihrer Domäne erfolgreich aktiviert. |

\* Diese Meldung wird nur bei einem Fehler angezeigt. 

Sollte vor dem Übermitteln der Anforderung ein Fehler auftreten, wird die folgende Fehlermeldung angezeigt:

<code>
We encountered an unexpected error while processing your HTTPS request. Please try again and contact support if the issue persists.
</code>



## <a name="clean-up-resources---disable-https"></a>Bereinigen von Ressourcen – Deaktivieren von HTTPS

In diesem Abschnitt erfahren Sie, wie Sie HTTPS für Ihre benutzerdefinierte Domäne deaktivieren.

### <a name="disable-the-https-feature"></a>Deaktivieren des HTTPS-Features 

1. Suchen Sie im [Azure-Portal](https://portal.azure.com) nach **CDN-Profile**, und wählen Sie den Eintrag aus. 

2. Wählen Sie das Profil **Azure CDN Standard von Microsoft**, **Azure CDN Standard von Verizon** oder **Azure CDN Premium von Verizon** aus.

3. Wählen Sie in der Liste mit den Endpunkten den Endpunkt aus, der Ihre benutzerdefinierte Domäne enthält.

4. Wählen Sie die benutzerdefinierte Domäne aus, für die Sie HTTPS deaktivieren möchten.

    ![Liste mit benutzerdefinierten Domänen](./media/cdn-custom-ssl/cdn-custom-domain-HTTPS-enabled.png)

5. Klicken Sie auf **Aus**, um HTTPS zu deaktivieren, und wählen Sie dann **Anwenden** aus.

    ![Dialogfeld „HTTPS für benutzerdefinierte Domänen“](./media/cdn-custom-ssl/cdn-disable-custom-ssl.png)

### <a name="wait-for-propagation"></a>Warten auf die Weitergabe

Nachdem das HTTPS-Feature für die benutzerdefinierte Domäne deaktiviert wurde, dauert es maximal 6 bis 8 Stunden, bis die Änderung wirksam wird. Nach Abschluss des Prozesses ändert sich im Azure-Portal der HTTPS-Status für benutzerdefinierte Domänen in **Aus**. Die drei Schritte im Dialogfeld der benutzerdefinierten Domäne werden als abgeschlossen markiert. Die benutzerdefinierte Domäne kann HTTPS nicht mehr verwenden.

![Dialogfeld „HTTPS deaktivieren“](./media/cdn-custom-ssl/cdn-disable-custom-ssl-complete.png)

#### <a name="operation-progress"></a>Vorgangsstatus

Die folgende Tabelle zeigt den Status des Vorgangs zum Deaktivieren von HTTPS. Nach der Deaktivierung von HTTPS werden im Dialogfeld der benutzerdefinierten Domäne drei Schritte angezeigt. Beim Durchlaufen der einzelnen Schritte werden Details zum jeweiligen Schritt angezeigt. Nachdem ein Schritt erfolgreich abgeschlossen wurde, wird neben diesem ein grünes Häkchen angezeigt. 

| Vorgangsstatus | Vorgangsdetails | 
| --- | --- |
| 1: Übermitteln der Anforderung | Ihre Anforderung wird übermittelt. |
| 2: Aufheben der Zertifikatbereitstellung | Das Zertifikat wird gelöscht. |
| 3: Abschließen | Das Zertifikat wurde gelöscht. |

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

1. *Wer ist der Zertifikatanbieter, und welche Art von Zertifikat wird verwendet?*

    In folgenden Fällen wird für Ihre benutzerdefinierte Domäne ein dediziertes Zertifikat von DigiCert verwendet:
    
    * **Azure CDN von Verizon**
    * **Azure CDN von Microsoft**

2. *Basiert TLS/SSL auf IP oder auf SNI?*

    Sowohl für **Azure CDN von Verizon** als auch für **Azure CDN Standard von Microsoft** wird SNI-basiertes TLS/SSL verwendet.

3. *Was passiert, wenn ich die Domänenüberprüfungs-E-Mail von DigiCert nicht erhalte?*

    Wenn Sie die Unterdomäne *cdnverify* nicht verwenden und Ihr CNAME-Eintrag für den Hostnamen Ihres Endpunkts gilt, erhalten Sie keine Domänenüberprüfungs-E-Mail.
     
    Die Bestätigung erfolgt automatisch. Wenden Sie sich andernfalls an den Microsoft-Support, falls Sie nicht über einen CNAME-Eintrag verfügen und innerhalb von 24 Stunden keine E-Mail erhalten.

4. *Ist ein SAN-Zertifikat weniger sicher als ein dediziertes Zertifikat?*
    
    Ein SAN-Zertifikat bietet die gleichen Verschlüsselungs- und Sicherheitsstandards wie ein dediziertes Zertifikat. Zur Verbesserung der Serversicherheit verwenden alle ausgestellten TLS-/SSL-Zertifikate SHA-256.

5. *Benötige ich einen CAA-Datensatz (Certificate Authority Authorization) bei meinem DNS-Anbieter?*

    Aktuell wird kein CAA-Datensatz benötigt. Wenn Sie allerdings über einen solchen Datensatz verfügen, muss er DigiCert als gültige Zertifizierungsstelle enthalten.

6. *Am 20. Juni 2018 wurde für Azure CDN von Verizon mit der standardmäßigen Nutzung eines dedizierten Zertifikats mit SNI-basiertem TLS/SSL begonnen. Was passiert mit meinen vorhandenen benutzerdefinierten Domänen, für die ein Zertifikat vom Typ „Alternativer Antragstellername“ (Subject Alternative Names, SAN) und IP-basiertes TLS/SSL verwendet wird?*

    Ihre vorhandenen Domänen werden in den kommenden Monaten nach und nach zur Verwendung eines einzelnen Zertifikats migriert, wenn von Microsoft analysiert wird, dass nur SNI-Clientanforderungen für Ihre Anwendung erfolgen. 
    
    Wenn SNI-fremde Clients erkannt werden, bleiben Ihre Domänen im SAN-Zertifikat mit IP-basiertem TLS/SSL. Anforderungen an Ihren Dienst oder an SNI-fremde Clients bleiben unberührt.

7. *Wie werden eigene Zertifikate verlängert?*

    Laden Sie Ihr neues Zertifikat in Azure Key Vault hoch, um sicherzustellen, dass ein neueres Zertifikat in der PoP-Infrastruktur bereitgestellt wird. Wählen Sie in Azure CDN in Ihren TLS-Einstellungen die neueste Zertifikatversion und anschließend „Speichern“ aus. Azure CDN wird dann Ihr neues aktualisiertes Zertifikat verteilen. 

8. *Muss ich HTTPS nach dem Neustart des Endpunkts erneut aktivieren?*

    Ja. Wenn Sie **Azure CDN von Akamai** verwenden und der Endpunkt beendet und neu gestartet wird, müssen Sie die HTTPS-Einstellung erneut aktivieren, wenn die Einstellung zuvor aktiviert war.


## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> - Aktivieren des HTTPS-Protokolls für Ihre benutzerdefinierte Domäne
> - Verwenden eines CDN-verwalteten Zertifikats 
> - Verwenden Ihres eigenen Zertifikats
> - Validieren der Domäne
> - Deaktivieren des HTTPS-Protokolls für Ihre benutzerdefinierte Domäne

Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie das Caching auf Ihrem CDN-Endpunkt konfigurieren.

> [!div class="nextstepaction"]
> [Tutorial: Festlegen der Azure CDN-Cacheregeln](cdn-caching-rules-tutorial.md)

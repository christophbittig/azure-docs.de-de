---
title: Notfallrotation von AD FS-Zertifikaten | Microsoft-Dokumentation
description: In diesem Artikel wird erläutert, wie AD FS Zertifikate unverzüglich gesperrt und aktualisiert werden.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/22/2021
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: 3b03dc507a76254f8568989af27f76aa75ad4e20
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130222436"
---
# <a name="emergency-rotation-of-the-ad-fs-certificates"></a>Notfallrotation von AD FS-Zertifikaten
Wenn Sie die AD FS Zertifikate unverzüglich rotieren müssen, können Sie die folgenden in diesem Abschnitt beschriebenen Schritte ausführen.

> [!IMPORTANT]
> Wenn Sie die folgenden Schritte in der AD FS-Umgebung ausführen, werden die alten Zertifikate sofort gesperrt.  Da dies unverzüglich erfolgt, wird die normalerweise zulässige Zeit, in der Verbundpartner neue Zertifikate nutzen können, umgangen. Dies kann zu einem Dienstausfall führen, während die Vertrauensstellungen aktualisiert werden, sodass die neuen Zertifikate verwendet werden.  Der Dienst sollte wieder verfügbar sein, sobald alle Verbundpartner über die neuen Zertifikate verfügen.

> [!NOTE]
> Microsoft empfiehlt dringend, ein Hardwaresicherheitsmodul (HSM) zu verwenden, um Zertifikate zu schützen und zu sichern.
> Weitere Informationen finden Sie in den bewährten Methoden zum Sichern von AD FS im Artikel zu [Hardwaresicherheitsmodulen](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#hardware-security-module-hsm).

## <a name="determine-your-token-signing-certificate-thumbprint"></a>Ermitteln des Fingerabdrucks eines Tokensignaturzertifikats
Um das alte Tokensignaturzertifikat zu sperren, das zurzeit von AD FS verwendet wird, müssen Sie den Fingerabdruck des Tokensignaturzertifikats ermitteln.  Gehen Sie dazu folgendermaßen vor:

 1.    Stellen Sie eine Verbindung zum Microsoft-Onlinedienst her: `PS C:\>Connect-MsolService`.
 2.    Dokumentieren Sie den Fingerabdruck und das Ablaufdatum des lokalen Tokensignaturzertifikats und des Tokensignaturzertifikats für die Cloud.
`PS C:\>Get-MsolFederationProperty -DomainName <domain>` 
 3.  Kopieren Sie den Fingerabdruck.  Dieser wird später verwendet, um die vorhandenen Zertifikate zu entfernen.

Sie können den Fingerabdruck auch mit der AD FS-Verwaltung ermitteln. Navigieren Sie dazu zu „Dienst/Zertifikate“, klicken Sie mit der rechten Maustaste auf das Zertifikat, klicken Sie auf „Zertifikat anzeigen“, und wählen Sie dann „Details“ aus. 

## <a name="determine-whether-ad-fs-renews-the-certificates-automatically"></a>Ermitteln, ob AD FS die Zertifikate automatisch erneuert
Standardmäßig ist AD FS so konfiguriert, dass die Zertifikate für die Tokensignatur und Tokenentschlüsselung automatisch generiert werden, sowohl bei der Erstkonfiguration als auch zu dem Zeitpunkt, wenn sich die Zertifikate dem Ablaufdatum nähern.

Sie können den folgenden Windows PowerShell-Befehl ausführen: `PS C:\>Get-AdfsProperties | FL AutoCert*, Certificate*`.

Die AutoCertificateRollover-Eigenschaft beschreibt, ob AD FS so konfiguriert ist, dass die Tokensignatur- und die Tokenentschlüsselungszertifikate automatisch erneuert werden.  Befolgen Sie die unten unter [Generieren eines neuen selbstsignierten Zertifikats, wenn AutoCertificateRollover auf TRUE festgelegt ist](#generating-new-self-signed-certificate-if-autocertificaterollover-is-set-to-true) beschriebenen Anweisungen, wenn AutoCertificateRollover auf TRUE festgelegt ist.  Befolgen Sie die unten unter [Manuelles Generieren neuer Zertifikate, wenn AutoCertificateRollover auf FALSE festgelegt ist](#generating-new-certificates-manually-if-autocertificaterollover-is-set-to-false) beschriebenen Anweisungen, wenn AutoCertificateRollover auf FALSE festgelegt ist.


## <a name="generating-new-self-signed-certificate-if-autocertificaterollover-is-set-to-true"></a>Generieren eines neuen selbstsignierten Zertifikats, wenn AutoCertificateRollover auf TRUE festgelegt ist
In diesem Abschnitt erstellen Sie **zwei** Tokensignaturzertifikate.  Für das erste wird das Flag **-urgent** verwendet, wodurch das aktuelle primäre Zertifikat sofort ersetzt wird.  Das zweite wird für das sekundäre Zertifikat verwendet.  

>[!IMPORTANT]
>Der Grund für die Erstellung von zwei Zertifikaten liegt darin, dass Azure Informationen zum vorherigen Zertifikat speichert.  Durch Erstellung eines zweiten Zertifikats wird Azure gezwungen, die Informationen zum alten Zertifikat freizugeben und durch Informationen zum zweiten Zertifikat zu ersetzen.
>
>Wenn Sie das zweite Zertifikat nicht erstellen und Azure damit aktualisieren, kann es u. U. vorkommen, dass Benutzer mit dem alten Tokensignaturzertifikat authentifiziert werden.

Mit den folgenden Schritten können Sie die neuen Tokensignaturzertifikate generieren.

 1. Sie müssen am primären AD FS-Server angemeldet sein.
 2. Öffnen Sie Windows PowerShell als Administrator. 
 3. Vergewissern Sie sich, dass AutoCertificateRollover auf TRUE festgelegt ist.
`PS C:\>Get-AdfsProperties | FL AutoCert*, Certificate*`
 4. Generieren Sie ein neues Tokensignaturzertifikat: `Update-ADFSCertificate –CertificateType token-signing -Urgent`.
 5. Überprüfen Sie die Aktualisierung, indem Sie den folgenden Befehl ausführen: `Get-ADFSCertificate –CertificateType token-signing`.
 6. Generieren Sie jetzt das zweite Tokensignaturzertifikat: `Update-ADFSCertificate –CertificateType token-signing`.
 7. Sie können die Aktualisierung überprüfen, indem Sie erneut den folgenden Befehl ausführen: `Get-ADFSCertificate –CertificateType token-signing`.


## <a name="generating-new-certificates-manually-if-autocertificaterollover-is-set-to-false"></a>Manuelles Generieren neuer Zertifikate, wenn AutoCertificateRollover auf FALSE festgelegt ist
Wenn Sie nicht die automatisch generierten, selbstsignierten standardmäßigen Tokensignatur- und Tokenentschlüsselungszertifikate verwenden, müssen Sie diese Zertifikate manuell erneuern und konfigurieren.  Dazu müssen Sie zwei neue Tokensignaturzertifikate erstellen und importieren.  Anschließend stufen Sie ein Zertifikat zum primären Zertifikat herauf, sperren das alte Zertifikat und konfigurieren das zweite Zertifikat als sekundäres Zertifikat.

Zunächst müssen Sie zwei neue Zertifikate von Ihrer Zertifizierungsstelle abrufen und auf jedem Verbundserver in den persönlichen Zertifikatspeicher des lokalen Computers importieren. Entsprechende Anweisungen finden Sie im Artikel [Importieren eines Zertifikats](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754489(v=ws.11)).

>[!IMPORTANT]
>Der Grund für die Erstellung von zwei Zertifikaten liegt darin, dass Azure Informationen zum vorherigen Zertifikat speichert.  Durch Erstellung eines zweiten Zertifikats wird Azure gezwungen, die Informationen zum alten Zertifikat freizugeben und durch Informationen zum zweiten Zertifikat zu ersetzen.
>
>Wenn Sie das zweite Zertifikat nicht erstellen und Azure damit aktualisieren, kann es u. U. vorkommen, dass Benutzer mit dem alten Tokensignaturzertifikat authentifiziert werden.

### <a name="to-configure-a-new-certificate-as-a-secondary-certificate"></a>So konfigurieren Sie ein neues Zertifikat als sekundäres Zertifikat
Anschließend müssen Sie ein Zertifikat als sekundäres AD FS-Tokensignatur- oder Tokenentschlüsselungszertifikat konfigurieren und dann zum primären Zertifikat heraufstufen.

1. Nachdem Sie das Zertifikat importiert haben, Öffnen Sie die **AD FS-Verwaltungskonsole**.
2. Erweitern Sie **Dienst**, und wählen Sie **Zertifikate** aus.
3. Klicken Sie im Aktionsbereich auf **Tokensignaturzertifikat hinzufügen**.
4. Wählen Sie ein neues Zertifikat aus der Liste der angezeigten Zertifikate aus, und klicken Sie auf OK.

### <a name="to-promote-the-new-certificate-from-secondary-to-primary"></a>So stufen Sie das neue Zertifikat vom sekundären zum primären Zertifikat hoch
Nachdem das neue Zertifikat in AD FS importiert und konfiguriert wurde, müssen Sie es als primäres Zertifikat festlegen.
1. Öffnen Sie die **AD FS-Verwaltungskonsole**.
2. Erweitern Sie **Dienst**, und wählen Sie **Zertifikate** aus.
3. Klicken Sie auf das sekundäre Tokensignaturzertifikat.
4. Klicken Sie im Bereich **Aktionen** auf **Als primär festlegen**. Klicken Sie auf Ja, um die Eingabe zu bestätigen.
5. Nachdem Sie das neue Zertifikat als primäres Zertifikat heraufgestuft haben, müssen Sie das alte Zertifikat entfernen, da es weiterhin verwendet werden kann. Weitere Informationen finden Sie weiter unten im Abschnitt [Entfernen der alten Zertifikate](#remove-your-old-certificates).  

### <a name="to-configure-the-second-certificate-as-a-secondary-certificate"></a>So konfigurieren Sie das zweite Zertifikat als sekundäres Zertifikat
Nachdem Sie das erste Zertifikat hinzugefügt, als primär festgelegt und das alte Zertifikat entfernt haben, importieren Sie das zweite Zertifikat.  Anschließend müssen Sie das Zertifikat als sekundäres AD FS-Tokensignaturzertifikat konfigurieren.

1. Nachdem Sie das Zertifikat importiert haben, Öffnen Sie die **AD FS-Verwaltungskonsole**.
2. Erweitern Sie **Dienst**, und wählen Sie **Zertifikate** aus.
3. Klicken Sie im Aktionsbereich auf **Tokensignaturzertifikat hinzufügen**.
4. Wählen Sie ein neues Zertifikat aus der Liste der angezeigten Zertifikate aus, und klicken Sie auf OK.

## <a name="update-azure-ad-with-the-new-token-signing-certificate"></a>Aktualisieren von Azure AD mit dem neuen Tokensignaturzertifikat
Öffnen Sie das Microsoft Azure Active Directory-Modul für Windows PowerShell. Sie können auch Windows PowerShell öffnen und dann den folgenden Befehl ausführen: `Import-Module msonline`.

Stellen Sie eine Verbindung mit Azure AD her, indem Sie den folgenden Befehl ausführen: `Connect-MsolService`. Geben Sie dann Ihre globalen Administratoranmeldeinformationen ein.

>[!Note]
> Wenn Sie diese Befehle auf einem Computer ausführen, der nicht der primäre Verbundserver ist, müssen Sie zunächst den folgenden Befehl eingeben: `Set-MsolADFSContext –Computer <servername>`. Ersetzen Sie \<servername\> durch den Namen des AD FS-Servers. Geben Sie anschließend die Administratoranmeldeinformationen für den AD FS-Server ein, wenn Sie dazu aufgefordert werden.

Optional können Sie anhand der aktuellen Zertifikatsinformationen in Azure AD überprüfen, ob eine Aktualisierung erforderlich ist. Führen Sie dazu den folgenden Befehl aus: `Get-MsolFederationProperty`. Geben Sie den Namen der Verbunddomäne ein, wenn Sie dazu aufgefordert werden.

Führen Sie den folgenden Befehl aus, um die Zertifikatinformationen in Azure AD zu aktualisieren: `Update-MsolFederatedDomain`, und geben Sie dann den Domänennamen ein, wenn Sie dazu aufgefordert werden.

>[!Note]
> Wenn bei der Ausführung dieses Befehls ein Fehler angezeigt wird, führen Sie den folgenden Befehl aus: `Update-MsolFederatedDomain –SupportMultipleDomain`, und geben Sie dann den Domänennamen ein, wenn Sie dazu aufgefordert werden.

## <a name="replace-ssl-certificates"></a>Ersetzen von SSL-Zertifikaten
Falls Sie Ihr Tokensignaturzertifikat ersetzen müssen, weil es kompromittiert wurde, müssen Sie auch die SSL-Zertifikate für AD FS und Ihre WAP-Server sperren und ersetzen.  

Das Sperren der SSL-Zertifikate muss bei der Zertifizierungsstelle erfolgen, von der das Zertifikat ausgestellt wurde.  Diese Zertifikate werden oft von Drittanbietern wie GoDaddy ausgestellt.  Ein Beispiel finden Sie im Abschnitt zum Widerrufen eines Zertifikats in der GoDaddy-Hilfe zu SSL-Zertifikaten.  Weitere Informationen finden Sie im Artikel zur [Funktionsweise der Zertifikatsperrung](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee619754(v=ws.10)).

Nachdem das alte SSL-Zertifikat gesperrt und ein neues Zertifikat ausgestellt wurde, können Sie die SSL-Zertifikate ersetzen. Weitere Informationen finden Sie im Artikel zum [Ersetzen des SSL-Zertifikats für AD FS](/windows-server/identity/ad-fs/operations/manage-ssl-certificates-ad-fs-wap#replacing-the-ssl-certificate-for-ad-fs).

## <a name="remove-your-old-certificates"></a>Entfernen der alten Zertifikate
Nachdem Sie Ihre alten Zertifikate ersetzt haben, müssen Sie das alte Zertifikat entfernen, da es weiterhin verwendet werden kann. Führen Sie dazu folgende Schritte aus:

1. Sie müssen am primären AD FS-Server angemeldet sein.
2. Öffnen Sie Windows PowerShell als Administrator. 
4. Entfernen Sie das alte Tokensignaturzertifikat: `Remove-ADFSCertificate –CertificateType token-signing -thumbprint <thumbprint>`.

## <a name="updating-federation-partners-who-can-consume-federation-metadata"></a>Aktualisieren von Verbundpartnern, die Verbundmetadaten nutzen können
Wenn Sie ein neues Tokensignaturzertifikat oder Tokenentschlüsselungszertifikat erneuert und konfiguriert haben, müssen Sie sicherstellen, dass alle Verbundpartner (Ressourcenorganisations- oder Kontoorganisationspartner, die in AD FS durch Vertrauensstellungen der vertrauenden Seite und Anspruchsanbieter-Vertrauensstellungen dargestellt werden) die neuen Zertifikate übernommen haben.

## <a name="updating-federation-partners-who-can-not-consume-federation-metadata"></a>Aktualisieren von Verbundpartnern, die Verbundmetadaten NICHT nutzen können
Wenn Ihre Verbundpartner keine Verbundmetadaten nutzen können, müssen Sie ihnen manuell den öffentlichen Schlüssel des neuen Tokensignatur-/Tokenentschlüsselungszertifikats zusenden. Senden Sie den öffentlichen Schlüssel des neuen Zertifikats (CER-Datei oder P7B-Datei, wenn Sie die gesamte Kette einbeziehen möchten) an alle Ressourcenorganisations- oder Kontoorganisationspartner (die in AD FS durch Vertrauensstellungen der vertrauenden Seite und Anspruchsanbieter-Vertrauensstellungen dargestellt werden). Fordern Sie die Partner auf, die Änderungen auf ihrer Seite zu implementieren, damit den neuen Zertifikaten vertraut wird.



## <a name="revoke-refresh-tokens-via-powershell"></a>Sperren von Aktualisierungstoken über PowerShell
Jetzt möchten wir Aktualisierungstoken für Benutzer sperren, die u. U. über solche Token verfügen, und die Benutzer zwingen, sich erneut anzumelden, damit sie neue Token erhalten.  Dadurch werden Benutzer von ihrem Telefon, aktuellen Webmail-Sitzungen und anderen Elementen abgemeldet, die Token und Aktualisierungstoken verwenden.  Entsprechende Informationen finden Sie [hier](/powershell/module/azuread/revoke-azureaduserallrefreshtoken?preserve-view=true&view=azureadps-2.0). Hilfreiche Informationen finden Sie auch unter [Widerrufen des Benutzerzugriffs in Azure Active Directory](../../active-directory/enterprise-users/users-revoke-access.md).

## <a name="next-steps"></a>Nächste Schritte

- [Verwalten von SSL-Zertifikaten in AD FS und WAP in Windows Server 2016](/windows-server/identity/ad-fs/operations/manage-ssl-certificates-ad-fs-wap#replacing-the-ssl-certificate-for-ad-fs)
- [Anfordern und Konfigurieren von Tokensignatur- und Tokenentschlüsselungszertifikaten für AD FS](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn781426(v=ws.11)#updating-federation-partners)
- [Erneuern von Verbundzertifikaten für Microsoft 365 und Azure Active Directory](how-to-connect-fed-o365-certs.md)

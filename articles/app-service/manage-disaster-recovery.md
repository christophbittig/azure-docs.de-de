---
title: Wiederherstellung nach regionsweitem Ausfall
description: Erfahren Sie, wie Azure App Service Sie bei der Aufrechterhaltung der Geschäftskontinuität und Notfallwiederherstellung (Business Continuity Disaster Recovery, BCDR) unterstützt. Stellen Sie Ihre App nach einem regionsweiten Ausfall in Azure wieder her.
ms.topic: how-to
ms.date: 06/09/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 418c5077a685e8f5289644f4652638e605b6d456
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130240469"
---
# <a name="move-an-app-service-app-to-another-region"></a>Verschieben von App Service-Apps in eine andere Region

In diesem Artikel wird beschrieben, wie Sie während eines Notfalls, der eine ganze Azure-Region beeinträchtig, App Service-Ressourcen in einer anderen Azure-Region wieder online schalten können. Wenn eine gesamte Azure-Region durch einen Notfall offline geht, werden alle in dieser Region gehosteten App Service-Apps in den Notfallwiederherstellungsmodus versetzt. Ihnen stehen Funktionen zur Verfügung, die Sie beim Wiederherstellen der App in einer anderen Region oder beim Wiederherstellen von Dateien aus der betroffenen App unterstützen.

App Service-Ressourcen sind regionsspezifisch und können nicht in andere Regionen verschoben werden. Sie müssen die App in einer neuen App in einer anderen Region wiederherstellen und dann Spiegelungskonfigurationen oder -ressourcen für die neue App erstellen.

## <a name="prerequisites"></a>Voraussetzungen

- Keine. Zum [Wiederherstellen aus einer Momentaufnahme](app-service-web-restore-snapshots.md) ist in der Regel ein **Premium**-Tarif erforderlich, aber im Notfallwiederherstellungsmodus wird dieser automatisch für Ihre betroffene App aktiviert, unabhängig davon, in welchem Tarif sich die betroffene App befindet.

## <a name="prepare"></a>Vorbereiten

Ermitteln Sie alle App Service-Ressourcen, die die betroffene App zurzeit verwendet. Beispiel:

- App Service-Apps
- [App Service-Pläne](overview-hosting-plans.md)
- [Bereitstellungsslots](deploy-staging-slots.md)
- [In Azure erworbene benutzerdefinierte Domänen](manage-custom-dns-buy-domain.md)
- [TLS-/SSL-Zertifikate](configure-ssl-certificate.md)
- [Integration des virtuellen Azure-Netzwerks](./overview-vnet-integration.md)
- [Hybridverbindungen](app-service-hybrid-connections.md)
- [Verwaltete Identitäten](overview-managed-identity.md)
- [Sicherungseinstellungen](manage-backup.md)

Bestimmte Ressourcen (z.B. importierte Zertifikate oder Hybridverbindungen) umfassen eine Integration in andere Azure-Dienste. Informationen dazu, wie Sie diese Ressourcen innerhalb von Regionen verschieben, finden Sie in der Dokumentation zu den jeweiligen Diensten.

## <a name="restore-app-to-a-different-region"></a>Wiederherstellen einer App in einer anderen Region

1. Erstellen Sie eine neue App Service-App in einer *anderen* Azure-Region als der der betroffenen App. Dies ist die Ziel-App im Notfallwiederherstellungsszenario.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zur Verwaltungsseite der betroffenen App. In einer ausgefallenen Azure-Region wird für die betroffene App ein Warnungstext angezeigt. Klicken Sie auf den Warnungstext.

    ![Screenshot der Seite der betroffenen App. Eine Warnmeldung wird angezeigt, die die Situation beschreibt und einen Link zum Wiederherstellen der App enthält.](media/manage-disaster-recovery/restore-start.png)

1. Konfigurieren Sie auf der Seite **Sicherung wiederherstellen** den Wiederherstellungsvorgang gemäß der folgenden Tabelle. Wenn Sie fertig sind, klicken Sie auf **OK**.

   | Einstellung | Wert | BESCHREIBUNG |
   |-|-|-|
   | **Momentaufnahme (Vorschau)** | Erstellen einer Momentaufnahme. | Die beiden letzten Momentaufnahmen stehen zur Verfügung. |
   | **Wiederherstellungsziel** | **Vorhandene App** | Klicken Sie unten auf den Hinweis, der besagt **Klicken Sie hier, um die Wiederherstellungsziel-App zu ändern**, und wählen Sie die Ziel-App aus. In einem Notfallszenario können Sie die Momentaufnahme nur in einer App in einer anderen Azure-Region wiederherstellen. |
   | **Standortkonfiguration wiederherstellen** | **Ja** | |

    ![Screenshot der Seite „Sicherung wiederherstellen“. Eine spezifische Momentaufnahme, die Optionen, die in der vorangehenden Tabelle aufgeführt sind, und die Schaltfläche „OK“ sind hervorgehoben.](media/manage-disaster-recovery/restore-configure.png)

3. Konfigurieren Sie [alles andere](#prepare) in der Ziel-App mit denselben Einstellungen wie in der betroffenen App, und überprüfen Sie Ihre Konfiguration.

4. Wenn alle Voraussetzungen erfüllt sind und die benutzerdefinierte Domäne auf die Ziel-App zeigen kann, [ordnen Sie den Domänennamen neu zu](manage-custom-dns-migrate-domain.md#remap-the-active-dns-name).

## <a name="recover-app-content-only"></a>Nur App-Inhalte wiederherstellen

Wenn Sie nur die Dateien aus der betroffenen App wiederherstellen möchten, ohne sie selbst wiederherzustellen, führen Sie die folgenden Schritte aus:

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zur Verwaltungsseite der betroffenen App, und klicken Sie auf **Veröffentlichungsprofil abrufen**.

    ![Screenshot der Seite der betroffenen App. Eine Warnbenachrichtigung wird angezeigt, ist aber nicht hervorgehoben. Stattdessen ist das Element „Veröffentlichungsprofil abrufen“ hervorgehoben.](media/manage-disaster-recovery/get-publish-profile.png)

1. Öffnen Sie die heruntergeladene Datei, und suchen Sie das Veröffentlichungsprofil, das `ReadOnly - FTP` im Namen enthält. Dies ist das Notfallwiederherstellungsprofil. Beispiel:

    ```xml
    <publishProfile profileName="%app-name% - ReadOnly - FTP" publishMethod="FTP" publishUrl="ftp://%ftp-site%/site/wwwroot" ftpPassiveMode="True" userName="%app-name%\$%app-name%" userPWD="" destinationAppUrl="http://%app-name%.azurewebsites.net" SQLServerDBConnectionString="" mySQLDBConnectionString="" hostingProviderForumLink="" controlPanelLink="http://windows.azure.com" webSystem="WebSites">
        <databases />
    </publishProfile>
    ```
    
    Kopieren von drei Attributwerten: 
        
    - `publishUrl`: der FTP-Hostname
    - `userName` und `userPWD`: die FTP-Anmeldeinformationen

1. Verwenden Sie den FTP-Client Ihrer Wahl, und stellen Sie mithilfe des Hostnamens und der Anmeldeinformationen eine Verbindung mit dem FTP-Host der betroffenen App her.

1. Nachdem die Verbindung hergestellt ist, laden Sie den gesamten Ordner */site/wwwroot* herunter. Der folgende Screenshot zeigt, wie Sie den Download in [FileZilla](https://filezilla-project.org/) durchführen.

    ![Screenshot einer FileZilla-Dateihierarchie. Der Ordner „wwwroot“ ist hervorgehoben, und sein Kontextmenü wird angezeigt. In diesem Menü ist „Herunterladen“ hervorgehoben.](media/manage-disaster-recovery/download-content.png)

## <a name="next-steps"></a>Nächste Schritte
[Wiederherstellen einer App in Azure auf der Grundlage einer Momentaufnahme](app-service-web-restore-snapshots.md)
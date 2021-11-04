---
title: 'Konfigurieren des QnA Maker-Diensts: QnA Maker'
description: In diesem Dokument werden erweiterte Konfigurationen für Ihre QnA Maker-Ressourcen erläutert.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 08/25/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: e8e6cf62e2d6c09059af00fc7fa3d5f2c447ae2a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131069214"
---
# <a name="configure-qna-maker-resources"></a>Konfigurieren von QnA Maker-Ressourcen

Benutzer können QnA Maker für die Verwendung einer anderen Cognitive Search-Ressource konfigurieren. Bei Verwendung der allgemein verfügbaren Version von QnA Maker können sie außerdem App-Diensteinstellungen konfigurieren.

[!INCLUDE [Custom question answering](../includes/new-version.md)]

## <a name="configure-qna-maker-to-use-different-cognitive-search-resource"></a>Konfigurieren von QnA Maker zur Verwendung einer anderen Cognitive Search-Ressource

> [!NOTE]
> Wenn Sie den dem QnA Maker zugeordneten Azure Search-Dienst ändern, verlieren Sie den Zugriff auf alle bereits darin vorhandenen Wissensdatenbanken. Stellen Sie sicher, dass Sie die vorhandenen Wissensdatenbanken exportieren, bevor Sie den Azure Search-Dienst ändern.

Wenn Sie einen QnA-Dienst und dessen Abhängigkeiten (z.B. Search) über das Portal erstellen, wird automatisch ein Suchdienst erstellt und mit dem QnA Maker-Dienst verknüpft. Nachdem diese Ressourcen erstellt wurden, können Sie die App Service-Einstellung aktualisieren, um einen bereits vorhandenen Suchdienst zu nutzen und den soeben erstellten Suchdienst zu entfernen.

Die **App Service**-Ressource von QnA Maker verwendet die Cognitive Search-Ressource. Um die von QnA Maker verwendete Cognitive Search-Ressource zu ändern, müssen Sie die Einstellung im Azure-Portal ändern.

1. Rufen Sie den **Administratorschlüssel** und den **Namen** der Cognitive Search-Ressource ab, die von QnA Maker verwendet werden soll.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und suchen Sie den **App Service**, der Ihrer QnA Maker-Ressource zugeordnet ist. Beide weisen denselben Namen auf.

1. Wählen Sie **Einstellungen** und dann **Konfiguration** aus. Dadurch werden alle vorhandenen Einstellungen für den App Service des QnA Makers angezeigt.

    > [!div class="mx-imgBorder"]
    > ![Screenshot des Azure-Portals mit dargestellten Konfigurationseinstellungen für den App Service](../media/qnamaker-how-to-upgrade-qnamaker/change-search-service-app-service-configuration.png)

1. Ändern Sie die Werte für die folgenden Schlüssel:

    * **AzureSearchAdminKey**
    * **AzureSearchName**

1. Um die neuen Einstellungen zu verwenden, müssen Sie den App Service neu starten. Wählen Sie **Übersicht** und dann **Neu starten** aus.

    > [!div class="mx-imgBorder"]
    > ![Screenshot des Azure-Portals beim Neustart von App Service nach dem Ändern der Konfigurationseinstellungen](../media/qnamaker-how-to-upgrade-qnamaker/screenshot-azure-portal-restart-app-service.png)

Wenn Sie einen QnA-Dienst über Azure Resource Manager-Vorlagen erstellen, können Sie alle Ressourcen erstellen und die App Service-Erstellung so steuern, dass sie einen vorhandenen Suchdienst nutzt.

Weitere Informationen zum Konfigurieren der App Service-Anwendungseinstellungen finden Sie [hier](../../../app-service/configure-common.md#configure-app-settings).

## <a name="get-the-latest-runtime-updates"></a>Abrufen der neuesten Runtime-Updates

Die QnA Maker-Runtime ist Teil der Azure App Service-Instanz, die beim [Erstellen eines QnA Maker-Diensts](./set-up-qnamaker-service-azure.md) im Azure-Portal bereitgestellt wird. Updates für die Runtime werden in regelmäßigen Abständen bereitgestellt. Die QnA Maker App Service-Instanz befindet sich nach der Websiteerweiterung vom April 2019 (Version 5+) im automatischen Updatemodus. Dieses Update ist so konzipiert, dass es während Upgrades keine Downtime berücksichtigt.

Sehen Sie sich die aktuelle Version unter https://www.qnamaker.ai/UserSettings an. Wenn Ihre Version älter als Version 5.x ist, müssen Sie App Service neu starten, damit die neuesten Updates angewendet werden:

1. Wechseln Sie im [Azure-Portal](https://portal.azure.com) zu Ihrem QnA Maker-Dienst (Ressourcengruppe).

    > [!div class="mx-imgBorder"]
    > ![Azure-Ressourcengruppe von QnA Maker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Wählen Sie die App Service-Instanz aus, und öffnen Sie den Abschnitt **Übersicht**.

    > [!div class="mx-imgBorder"]
    > ![QnA Maker App Service-Instanz](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)


1. Starten Sie App Service neu. Der Updateprozess sollte innerhalb weniger Sekunden abgeschlossen sein. Abhängige Anwendungen oder Bots, in denen dieser QnA Maker-Dienst verwendet wird, stehen während dieses Neustartzeitraums für Endbenutzer nicht zur Verfügung.

    ![Neustarten der QnA Maker App Service-Instanz](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="configure-app-service-idle-setting-to-avoid-timeout"></a>Konfigurieren der App Service-Leerlaufeinstellung zur Vermeidung von Timeouts

Die App Service-Instanz, von der die QnA Maker-Vorhersageruntime für eine veröffentlichte Wissensdatenbank bereitgestellt wird, verfügt über eine Leerlauftimeoutkonfiguration, durch die standardmäßig automatisch ein Timeout ausgelöst wird, wenn sich der Dienst im Leerlauf befindet. Dies hat für QnA Maker zur Folge, dass für die generateAnswer-API Ihrer Vorhersageruntime gelegentlich ein Timeout auftritt, wenn eine Weile kein Datenverkehr übermittelt wurde.

Legen Sie den Leerlauf auf „Immer aktiv“ fest, um zu gewährleisten, dass die Vorhersageendpunkt-App geladen bleibt, auch wenn kein Datenverkehr übermittelt wird.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Suchen Sie nach der App Service-Instanz Ihrer QnA Maker-Ressource, und wählen Sie sie aus. Sie hat den gleichen Namen wie die QnA Maker-Ressource, aber einen anderen **Typ** (App Service).
1. Navigieren Sie zu **Einstellungen**, und wählen Sie **Konfiguration** aus.
1. Wählen Sie im Konfigurationsbereich **Allgemeine Einstellungen** aus, navigieren Sie zu **Immer aktiv**, und wählen Sie **Ein** aus.

    > [!div class="mx-imgBorder"]
    > ![Wählen Sie im Konfigurationsbereich **Allgemeine Einstellungen** aus, navigieren Sie zu **Immer aktiv**, und wählen Sie **Ein** aus.](../media/qnamaker-how-to-upgrade-qnamaker/configure-app-service-idle-timeout.png)

1. Wählen Sie zum Speichern der Konfiguration **Speichern** aus.
1. Sie werden gefragt, ob Sie die App neu starten möchten, um die neue Einstellung zu verwenden. Wählen Sie **Weiter**.

Weitere Informationen zum Konfigurieren der allgemeinen App Service-Einstellungen finden Sie [hier](../../../app-service/configure-common.md#configure-general-settings).

## <a name="business-continuity-with-traffic-manager"></a>Geschäftskontinuität mit Traffic Manager

Das Hauptziel des Geschäftskontinuitätsplans besteht darin, einen ausfallsicheren Wissensdatenbank-Endpunkt zu erstellen, der sicherstellen kann, dass keine Ausfallzeiten für den Bot oder die ihn verwendende Anwendung entstehen.

> [!div class="mx-imgBorder"]
> ![QnA Maker-Geschäftskontinuitätsplan](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

Das oben dargestellte allgemeine Konzept lautet wie folgt:

1. Richten Sie zwei parallele [QnA Maker-Dienste](set-up-qnamaker-service-azure.md) in [Azure-Regionspaaren](../../../best-practices-availability-paired-regions.md) ein.

1. [Sichern](../../../app-service/manage-backup.md) Sie Ihren primären QnA Maker-App-Dienst, und [stellen](../../../app-service/web-sites-restore.md) Sie ihn bei der Sekundäreinrichtung wieder her. Dadurch wird sichergestellt, dass beide Setups mit demselben Hostnamen und denselben Schlüsseln arbeiten.

1. Halten Sie die primären und sekundären Azure-Suchindizes synchron. Verwenden Sie das [hier](https://github.com/pchoudhari/QnAMakerBackupRestore) aufgeführte GitHub-Beispiel, um zu erfahren, wie Azure-Indizes gesichert und wiederhergestellt werden.

1. Sichern Sie die Application Insights-Daten mithilfe des [fortlaufenden Exports](../../../azure-monitor/app/export-telemetry.md).

1. Verwenden Sie [Traffic Manager](../../../traffic-manager/traffic-manager-overview.md) nach der Einrichtung der primären und sekundären Stapel, um die zwei Endpunkte zu konfigurieren und eine Routingmethode einzurichten.

1. Sie müssten ein TLS-Zertifikat (Transport Layer Security), früher SSL-Zertifikat (Secure Sockets Layer) genannt, für Ihren Traffic Manager-Endpunkt erstellen. [Binden Sie das TLS/SSL-Zertifikat](../../../app-service/configure-ssl-bindings.md) an Ihre App-Dienste.

1. Verwenden Sie dann den Traffic Manager-Endpunkt in Ihrem Bot oder in Ihrer App.

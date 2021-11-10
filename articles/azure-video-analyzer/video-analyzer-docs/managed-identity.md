---
title: Verwaltete Identitäten mit Azure Video Analyzer
description: In diesem Thema wird erläutert, wie verwaltete Identitäten mit Azure Video Analyzer verwendet werden.
ms.service: azure-video-analyzer
ms.topic: how-to
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 46d3936c1822b4409dad8ad373352b062c6c9b77
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131852767"
---
# <a name="managed-identity"></a>Verwaltete Identität

Die Verwaltung von Geheimnissen und Anmeldeinformationen für eine sichere Kommunikation zwischen verschiedenen Diensten stellt für Entwickler eine häufige Herausforderung dar. Bei Azure machen verwaltete Identitäten die Verwaltung von Anmeldeinformationen für Entwickler überflüssig, indem sie eine Identität für die Azure-Ressource in Azure Active Directory (Azure AD) bereitstellen und diese verwenden, um Azure AD-Tokens zu erhalten.

Wenn Sie ein Azure Video Analyzer-Konto erstellen, müssen Sie es ein Azure-Speicherkonto zuordnen. Wenn Sie Video Analyzer verwenden, um das Live-Video von einer Kamera aufzuzeichnen, werden diese Daten als Blobs in einem Container im Speicherkonto gespeichert. Optional können Sie Ihrem Video Analyzer IoT Hub Konto eine Verbindung zuordnen. Dies ist erforderlich, wenn Sie das Video Analyzer-Edgemodul als [transparentes Gateway](./cloud/use-remote-device-adapter.md) verwenden. Sie müssen eine verwaltete Identität verwenden, um dem Video Analyzer-Konto den entsprechenden Zugriff auf das Speicherkonto und den IoT Hub (falls für Ihre Lösung erforderlich) wie folgt zu gewähren.

## <a name="user-assigned-managed-identity-for-video-analyzer"></a>Vom Benutzer zugewiesene verwaltete Identität für Video Analyzer

* Erstellen Sie eine [benutzerseitig zugewiesene verwaltete Identität (UAMI)](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)

> [!NOTE]
> Sie benötigen ein Azure-Abonnement, in dem Sie Zugriff auf die Rolle [Mitwirkender](../../role-based-access-control/built-in-roles.md#contributor) und die Rolle [Benutzerzugriffsadministrator](../../role-based-access-control/built-in-roles.md#user-access-administrator) für die Ressourcengruppe haben, unter der Sie neue Ressourcen erstellen (vom Benutzer zugewiesene verwaltete Identität, Speicherkonto, Video Analyzer-Konto). Wenn Sie nicht über die richtigen Berechtigungen verfügen, wenden Sie sich an Ihren Kontoadministrator, damit er Ihnen diese Berechtigungen erteilt. Das zugehörige Storage-Konto muss sich in derselben Region befinden wie das Video-Analyzer-Konto. Es wird empfohlen, ein [allgemeines Standardspeicherkonto der Version 2](../../storage/common/storage-account-overview.md#types-of-storage-accounts) zu verwenden.
Sie benötigen auch Zugriff auf die Rolle „Mitwirkender“ für den IoT Hub, wenn Sie eine Rolle an Ihr Video Analyzer-Konto anfügen möchten.

### <a name="enable-video-analyzer-account-to-access-storage-account"></a>Aktivieren des Video Analyzer-Kontos für den Zugriff Storage Kontos

* Erstellen Sie ein Azure-Speicherkonto.

* Fügen Sie der verwalteten Identität die Rollen [Blob-Daten-Speicher-Contributor](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) und [Leser](../../role-based-access-control/built-in-roles.md#reader) für das obengenannte Speicherkonto hinzu.

* Erstellen Sie das Video Analyzer-Konto, und geben Sie die vom Benutzer zugewiesene verwaltete Identität und das Speicherkonto als Werte für die relevanten Eigenschaften an.

Video Analyzer kann dann in Ihrem Namen anhand der verwalteten Identität auf das Speicherkonto zugreifen.

### <a name="enable-video-analyzer-account-to-access-iot-hub"></a>Aktivieren des Video Analyzer-Kontos für den Zugriff Storage Kontos

Im vorherigen Abschnitt haben Sie eine UAMI erstellt, die Video Analyzer den Zugriff auf das Speicherkonto ermöglicht hat. Wählen Sie einen IoT-Hub zum Anfügen aus. Dem Video Analyzer-Konto wird über die verwaltete Identität Zugriff auf den Hub gewährt, und der IoT-Hub wird mit Ihrem Video Analyzer-Konto verknüpft. Weitere Informationen zur Funktionsweise der verwalteten Identität mit IoT Hub finden Sie unter IoT Hub [verwaltete Identität.](../../iot-hub/iot-hub-managed-identity.md)

* Gehen Sie zum Azure-Portal, dann zum Video Analyzer Management Blade, wählen Sie **IoT Hub** unter **Einstellungen** im linken Fensterbereich
* Wählen Sie dann **Anhängen** im Bereich „IoT-Hub anhängen“. Geben Sie im Konfigurationsbereich **Attach IoT Hub** die erforderlichen Feldwerte ein:
    * Abonnement - Wählen Sie den Namen des Azure-Abonnements aus, IoT Hub erstellt wird.
    * IoT Hub - Wählen Sie einen vorhandenen IoT Hub aus, der an das Video Analyzer-Konto angefügt werden muss.
    * Verwaltete Identität - Wählen Sie die vom Benutzer zugewiesene verwaltete Identität (die zuvor im vorherigen Abschnitt erstellt wurde) aus, die für den Zugriff auf die IoT Hub
* Klicken Sie auf **Speichern**, um IoT Hub mit Ihrem Video Analyzer-Konto zu verknüpfen

In [diesem](create-video-analyzer-account.md) Artikel finden Sie ein Beispiel der Verwendung des Azure-Portals, um die obengenannten Aufgaben zu erfüllen.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu den Funktionen verwalteter Identitäten für Sie und Ihre Azure-Anwendungen finden Sie unter [Was sind verwaltete Identitäten für Azure-Ressourcen?](../../active-directory/managed-identities-azure-resources/overview.md).

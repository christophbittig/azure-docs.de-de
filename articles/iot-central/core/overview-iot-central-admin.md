---
title: Azure IoT Central – Administratorhandbuch
description: Azure IoT Central ist eine IoT-Anwendungsplattform, die die Erstellung von IoT-Lösungen vereinfacht. Dieser Artikel bietet einen Überblick über die Administratorrolle in IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 03/25/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: b91a9c451c0160523029cd8688dfa9a91f433b37
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131085922"
---
# <a name="iot-central-administrator-guide"></a>IoT Central – Administratorhandbuch

Mit einer IoT Central-Anwendung können Sie Millionen von Geräten während ihres gesamten Lebenszyklus überwachen und verwalten. Dieser Leitfaden richtet sich an Administratoren, die IoT Central-Anwendungen verwalten.

In IoT Central führt ein Administrator folgende Aufgaben aus:

- Er verwaltet Benutzer und Rollen in der Anwendung.
- Erstellt und verwaltet Organisationen
- Er verwaltet die Sicherheit, z. B. die Geräteauthentifizierung.
- Er konfiguriert Anwendungseinstellungen.
- Er aktualisiert Anwendungen.
- Er exportiert Anwendungen und gibt sie frei.
- Er überwacht die Anwendungsintegrität.

## <a name="users-and-roles"></a>Benutzer und Rollen

IoT Central verwendet ein rollenbasiertes Zugriffssteuerungssystem zum Verwalten von Benutzerberechtigungen in einer Anwendung. IoT Central hat drei integrierte Rollen für Administratoren, Lösungsentwickler und Operator. Ein Administrator kann benutzerdefinierte Rollen mit bestimmten Berechtigungssätzen erstellen. Ein Administrator ist dafür zuständig, Benutzer zu einer Anwendung hinzuzufügen und sie Rollen zuzuweisen.

Weitere Informationen finden Sie unter [Verwalten von Benutzern und Rollen in Ihrer IoT Central-Anwendung](howto-manage-users-roles.md).

## <a name="organizations"></a>Organisationen

Mit Organisationen können Sie eine Hierarchie definieren, mit der Sie verwalten, welche Benutzer welche Geräte in Ihrer IoT Central-Anwendung anzeigen können. Die Rolle des Benutzers bestimmt, welche Geräte er sehen und auf welche Umgebungen er zugreifen kann.

Weitere Informationen finden Sie unter [Erstellen einer IoT Central-Organisation](howto-create-organizations.md).

## <a name="application-security"></a>Anwendungssicherheit

Geräte, die eine Verbindung mit Ihrer IoT Central-Anwendung herstellen, verwenden normalerweise X.509-Zertifikate oder SAS (Shared Access Signature) als Anmeldeinformationen. Der Administrator verwaltet die Gruppenzertifikate oder Schlüssel, von denen die Geräteanmeldeinformationen abgeleitet werden.

Weitere Informationen finden Sie unter [X.509-Gruppenregistrierung](concepts-get-connected.md#x509-group-enrollment), [SAS-Gruppenregistrierung](concepts-get-connected.md#sas-group-enrollment) und [Registrieren von X.509-Gerätezertifikaten](how-to-roll-x509-certificates.md).

Der Administrator kann auch die API-Token erstellen und verwalten, mit denen sich eine Clientanwendung bei Ihrer IoT Central Anwendung authentifiziert. Clientanwendungen verwenden die REST-API für die Interaktion mit IoT Central.

Für Datenexporte kann der Administrator [verwaltete Identitäten](../../active-directory/managed-identities-azure-resources/overview.md) konfigurieren, um die Verbindungen mit den [Exportzielen](howto-export-data.md) zu schützen. Weitere Informationen finden Sie unter:

- [Konfigurieren einer verwalteten Identität (Azure-Portal)](howto-manage-iot-central-from-portal.md#configure-a-managed-identity)
- [Konfigurieren einer verwalteten Identität (REST-API)](howto-manage-iot-central-with-rest-api.md)
- [Konfigurieren einer verwalteten Identität (Azure CLI)](howto-manage-iot-central-from-cli.md#configure-a-managed-identity)

## <a name="configure-an-application"></a>Konfigurieren einer Anwendung

Der Administrator kann das Verhalten und Erscheinungsbild einer IoT Central-Anwendung konfigurieren. Weitere Informationen finden Sie unter:

- [Ändern von Anwendungsnamen und -URLs](howto-administer.md#change-application-name-and-url)
- [Anpassen der Benutzeroberfläche](howto-customize-ui.md)
- [Verschieben einer Anwendung in einen anderen Tarif](howto-faq.yml#how-do-i-move-from-a-free-to-a-standard-pricing-plan-)
- [Konfigurieren von Dateiuploads](howto-configure-file-uploads.md)

## <a name="export-an-application"></a>Exportieren einer Anwendung

Ein Administrator kann folgende Aufgaben ausführen:

- Eine Kopie einer Anwendung erstellen, wenn Sie nur eine doppelte Kopie Ihrer Anwendung benötigen. So benötigen Sie beispielsweise vielleicht eine doppelte Kopie zum Testen.
- Eine Anwendungsvorlage aus einer vorhandenen Anwendung erstellen, wenn Sie vorhaben, mehrere Kopien zu erstellen.

Weitere Informationen finden Sie unter [Erstellen und Verwenden einer benutzerdefinierten Anwendungsvorlage](howto-create-iot-central-application.md#create-and-use-a-custom-application-template).

## <a name="migrate-to-a-new-version"></a>Migrieren zu einer neuen Version

Ein Administrator kann eine Anwendung zu einer neueren Version migrieren. Zurzeit ist eine neu erstellte Anwendung eine V3-Anwendung. Ein Administrator muss eine V2-Anwendung möglicherweise zu einer V3-Anwendung migrieren.

Weitere Informationen finden Sie unter [Migrieren Ihrer V2-IoT Central-Anwendung zu V3](howto-migrate.md).

## <a name="monitor-application-health"></a>Überwachen der Anwendungsintegrität

Ein Administrator kann die Integrität von verbundenen Geräten und die Integrität von laufenden Datenexporten mithilfe von IoT Central-Metriken bewerten.

Zum Anzeigen der Metriken kann ein Administrator Diagramme im Azure-Portal, in einer REST-API oder aber in PowerShell oder Azure CLI-Abfragen verwenden.

Weitere Informationen finden Sie unter [Überwachen der Anwendungsintegrität](howto-manage-iot-central-from-portal.md#monitor-application-health).

## <a name="monitor-connected-iot-edge-devices"></a>Überwachen von verbundenen IoT Edge-Geräten

Wenn Sie erfahren möchten, wie Sie Ihre IoT Edge-Flotte mithilfe von Azure Monitor und integrierten Metriken überwachen können, lesen Sie [Sammeln und Transportieren von Metriken](../../iot-edge/how-to-collect-and-transport-metrics.md).

## <a name="tools"></a>Tools

Viele der Tools, die Sie als Administrator verwenden, stehen in jeder IoT Central Anwendung im Abschnitt **Verwaltung** zur Verfügung. Sie können auch die folgenden Tools zur Ausführung von einigen administrativen Aufgaben verwenden:

- [Azure-Befehlszeile](howto-manage-iot-central-from-cli.md)
- [Azure portal](howto-manage-iot-central-from-portal.md)

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun mehr über die Verwaltung Ihrer Azure IoT Central-Anwendung erfahren haben, wird im nächsten Schritt empfohlen, sich mit dem [Verwalten von Benutzern und Rollen](howto-manage-users-roles.md) in Azure IoT Central vertraut zu machen.

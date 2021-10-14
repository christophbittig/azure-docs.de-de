---
title: Verwalten von IoT Central-Organisationen | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Sie eine Organisationshierarchie erstellen und verwalten, um zu steuern, welche Benutzer auf welche Geräte in Ihrer IoT Central-Organisation zugreifen können. Verwenden Sie Organisationen, um mehrinstanzenfähige IoT Central-Anwendungen zu erstellen.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 08/20/2021
ms.topic: how-to
ms.openlocfilehash: e15e2b095cd6b6876728ff773a7b4617e325d77d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128656757"
---
# <a name="manage-iot-central-organizations"></a>Verwalten von IoT Central-Organisationen

Mit Organisationen können Sie eine Hierarchie definieren, mit der Sie verwalten, welche Benutzer welche Geräte in Ihrer IoT Central-Anwendung anzeigen können. Die Rolle des Benutzers bestimmt, welche Geräte er sehen und auf welche Umgebungen er zugreifen kann.

Organisationen sind hierarchisch:

:::image type="content" source="media/howto-create-organization/organizations-hierarchy.png" alt-text="Diagramm: Beispielhierarchie von Organisationen" border="false":::

Wenn Sie Ihre erste Organisationshierarchie erstellen, sehen Sie, dass sich der Name Ihrer Anwendung auf der Stammebene befindet. Jede Organisation, die Sie hinzufügen, wird zu einer untergeordneten Organisation oder Blattorganisation. Organisationshierarchien verwenden Vererbung. Beispielsweise in der Hierarchie, die im vorherigen Diagramm dargestellt ist:

- *Adatum Solar*-Benutzer haben Zugriff auf Geräte in *Nordamerika*, *Kanada*, *Europa* und *Lateinamerika*.
- *Contoso*-Benutzer haben Zugriff auf alle Geräte in allen Organisationen in der Hierarchie.

Benutzer in gleichgeordneten Organisationen können die Geräte der anderen Benutzer nicht sehen.

Wenn Sie Benutzern Zugriff auf Ihre Anwendung gewähren, können sie umso mehr sehen und verwalten, je höher sie in der Hierarchie stehen. Die Organisation steuert, welche Geräte der Benutzer sehen kann. Die Rolle definiert die Möglichkeiten, die ein Benutzer hat, z. B. Geräte erstellen, lesen und löschen.

Der folgende Screenshot zeigt eine Organisationshierarchiedefinition in IoT Central:

:::image type="content" source="media/howto-create-organization/organizations-definition.png" alt-text="Screenshot: Definition der Organisationshierarchie":::

## <a name="create-a-hierarchy"></a>Erstellen einer Hierarchie

Zur Verwendung von Organisationen müssen Sie Ihre Organisationshierarchie definieren. Jede Organisation in der Hierarchie fungiert als logischer Container, in dem Sie Geräte platzieren, Dashboards und Gerätegruppen speichern sowie Benutzer einladen. Um Ihre Organisationen zu erstellen, wechseln Sie in Ihrer IoT Central-Anwendung in den Bereich **Verwaltung**, wählen Sie die Registerkarte **Organisationen** und dann **+ Neu** aus, oder verwenden Sie das Kontextmenü für eine bestehende Organisation. Um eine Organisation oder mehrere Organisationen gleichzeitig zu erstellen, wählen Sie **+ Weitere Organisation hinzufügen** aus:

:::image type="content" source="media/howto-create-organization/create-organizations-hierarchy.png" alt-text="Screenshot: Optionen zum Erstellen einer Organisationshierarchie":::

> [!TIP]
> Die anfängliche Einrichtung von Organisationen muss von einem Mitglied der Rolle **App-Administrator** durchgeführt werden.

Um eine Organisation einem neuen übergeordneten Element neu zuzuweisen, wählen Sie **Bearbeiten** und dann ein neues übergeordnetes Element aus.

Zum Löschen einer Organisation müssen Sie alle zugehörigen Elemente wie Dashboards, Geräte, Benutzer, Gerätegruppen und Aufträge löschen oder in eine andere Organisation verschieben.

> [!TIP]
> Sie können auch die REST-API verwenden, um [Organisationen zu erstellen und zu verwalten](/rest/api/iotcentral/1.1-previewdataplane/organizations).

## <a name="assign-devices"></a>Zuweisen von Geräten

Nachdem Sie Ihre Organisationshierarchie definiert haben, weisen Sie Ihre Geräte Organisationen zu. Jedes Gerät gehört nur zu einer einzigen Organisation. Wählen Sie daher die entsprechende Organisation in der Hierarchie aus.

Wenn Sie ein neues Gerät in Ihrer Anwendung erstellen, weisen Sie es einer Organisation in Ihrer Hierarchie zu:

:::image type="content" source="media/howto-create-organization/assign-device.png" alt-text="Screenshot: Zuweisen eines Geräts zu einer Organisation":::

Zum Zuweisen oder erneuten Zuweisen eines vorhandenen Geräts zu einer Organisation wählen Sie das Gerät in der Geräteliste und dann **Organisation** aus:

:::image type="content" source="media/howto-create-organization/change-device-organization.png" alt-text="Screenshot: Wechseln der Organisation, der ein Gerät zugeordnet ist":::

> [!TIP]
> Sie können in der Geräteliste erkennen, zu welcher Organisation ein Gerät gehört. Verwenden Sie das Filtertool in der Geräteliste, um Geräte in einer bestimmten Organisation anzuzeigen.

Wenn Sie ein Gerät einer anderen Organisation neu zuweisen, bleiben die Daten des Geräts basierend auf dem Zeitpunkt der Datenerfassung bei der Organisation. Beispiel:

- Ein Gerät ist vom ersten bis zum siebten Tag Teil der Organisation **Contoso/Customer1** und wird dann am achten Tag in die Organisation **Contoso/Customer4** verschoben.
- Am neunten Tag können **Contoso/Customer4**-Benutzer Daten aus den Tagen 8 und 9 anzeigen.
- Am neunten Tag können **Contoso/Customer1**-Benutzer Daten der Tage 1 bis 7 anzeigen.

### <a name="device-first-registration"></a>Erstmalige Registrierung von Geräten

Geräte können sich selbst bei Ihrer IoT Central-Anwendung registrieren, ohne vorher zur Geräteliste hinzugefügt zu werden. In diesem Fall fügt IoT Central das Gerät der Stammorganisation in der Hierarchie hinzu. Sie können das Gerät dann einer anderen Organisation neu zuweisen.

Stattdessen können Sie das CSV-Importfeature verwenden, um eine Massenregistrierung der Geräte bei Ihrer Anwendung durchzuführen und sie den Organisationen zuzuweisen. Weitere Informationen finden Sie unter [Importieren von Geräten](howto-manage-devices.md#import-devices).

### <a name="gateways"></a>Gateways

Sie weisen Organisationen Gateway- und Blattgeräte zu. Sie müssen ein Gateway und die zugehörigen Blattgeräte nicht derselben Organisation zuweisen. Wenn Sie sie verschiedenen Organisationen zuweisen, ist es möglich, dass ein Benutzer das Gateway, aber nicht die Blattgeräte sehen kann (oder die Blattgeräte, aber nicht das Gateway).

## <a name="roles"></a>Rollen

Wenn Sie die erste Organisation in Ihrer Anwendung erstellen, fügt IoT Central Ihrer Anwendung drei neue Rollen hinzu: **Org Administrator** (Organisationsadministrator), **Org Operator** (Organisationsoperator) und **Org Viewer** (Organisationszuschauer). Diese Rollen sind erforderlich, da ein Organisationsbenutzer nicht auf bestimmte anwendungsweite Funktionen wie Tarife, Branding und Farben, API-Token und anwendungsweite Informationen zur Registrierungsgruppe zugreifen kann.

:::image type="content" source="media/howto-create-organization/organization-roles.png" alt-text="Screenshot: Die drei Organisationsrollen":::

Sie können diese Rollen verwenden, wenn Sie Benutzer zu einer Organisation in Ihrer Anwendung einladen.

### <a name="custom-organization-roles"></a>Benutzerdefinierte Organisationsrollen

Um eine benutzerdefinierte Rolle für die Benutzer Ihrer Organisation zu erstellen, erstellen Sie eine neue Rolle, und wählen Sie den Rollentyp **Organisation** aus:

:::image type="content" source="media/howto-create-organization/custom-organization-role.png" alt-text="Screenshot: Erstellen einer benutzerdefinierten Organisationsrolle":::

Wählen Sie dann die Berechtigungen für die Rolle aus:

:::image type="content" source="media/howto-create-organization/organization-role-permissions.png" alt-text="Screenshot: Auswählen benutzerdefinierter Berechtigungen für eine Organisationsrolle":::

## <a name="invite-users"></a>Benutzer einladen

Nachdem Sie Ihre Organisationshierarchie erstellt und den Organisationen entsprechende Geräte zugewiesen haben, laden Sie Benutzer zu Ihrer Anwendung ein, und weisen Sie sie den Organisationen zu.

Navigieren Sie zum Einladen eines Benutzers zu **Verwaltung > Benutzer**. Geben Sie die E-Mail-Adresse des Benutzers, die Organisation, der er zugewiesen ist, und die Rolle(n) ein, in der/denen der Benutzer Mitglied ist. Die ausgewählte Organisation filtert die Liste der verfügbaren Rollen, um sicherzustellen, dass Sie den Benutzer einer gültigen Rolle zuweisen:

:::image type="content" source="media/howto-create-organization/assign-user-organization.png" alt-text="Screenshot: Zuweisen eines Benutzers zu einer Organisation und einer Rolle":::

Sie können denselben Benutzer mehreren Organisationen zuweisen. Der Benutzer kann in jeder Organisation, der er zugewiesen ist, eine andere Rolle besitzen:

| Name | Role | Organization |
| ---- | ---- | ------------ |
| user1@contoso.com | Org Administrator (Organisationsadministrator) | Contoso Inc/Lamna Health |
| user1@contoso.com | Org Viewer (Organisationszuschauer) | Contoso Inc/Adatum Solar |

## <a name="use-organizations"></a>Verwenden von Organisationen

Nachdem Sie Ihre Organisationshierarchie erstellt haben, können Sie Organisationen in Bereichen Ihrer Anwendung wie den folgenden verwenden:

- [Organisationsdashboards](howto-manage-dashboards.md), die Benutzern Informationen zu Geräten in ihrer Organisation anzeigen.
- [Gerätegruppen](tutorial-use-device-groups.md) für Geräte in bestimmten Organisationen.
- [Analysen](howto-create-analytics.md) für Geräte in bestimmten Organisationen.
- [Aufträge](howto-manage-devices-in-bulk.md#create-and-run-a-job) zur Massenverwaltung von Geräten in bestimmten Organisationen.

## <a name="default-organization"></a>Standardorganisation

Sie können eine Organisation als Standardorganisation festlegen, die in Ihrer Anwendung verwendet werden soll. Die Standardorganisation wird immer dann zur Standardoption, wenn Sie eine Organisation auswählen, z. B. beim Hinzufügen eines neuen Benutzers zu Ihrer IoT Central-Anwendung.

Wählen Sie in der oberen Menüleiste **Einstellungen** aus, um die Standardorganisation festzulegen:

:::image type="content" source="media/howto-create-organization/set-default-organization.png" alt-text="Screenshot: Festlegen der Standardorganisation":::

> [!TIP]
> Dies ist eine persönliche Einstellung, die nur für Sie gilt.

## <a name="add-organizations-to-an-existing-application"></a>Hinzufügen von Organisationen zu einer vorhandenen Anwendung

Eine Anwendung kann Geräte, Benutzer und Benutzerfunktionen wie Dashboards, Gerätegruppen und Aufträge enthalten, bevor Sie eine Organisationshierarchie hinzufügen.

Wenn Sie mit dem Hinzufügen von Organisationen beginnen, bleiben alle vorhandenen Geräte, Benutzer und Erfahrungen der Stammorganisation in der Anwendung zugeordnet:

- Sie können Geräte einer neuen Organisation neu zuweisen.
- Sie können Benutzer einer neuen Organisation zuweisen und deren Zuweisung zur Stammebene aufheben.
- Sie können eine neue Erfahrung wie Dashboards, Gerätegruppen und Aufträge erstellen und sie Organisationen in der Hierarchie zuordnen.

## <a name="limits"></a>Grenzwerte

Folgende Grenzwerte gelten für Organisationen:

- Die Hierarchie darf nicht mehr als fünf Ebenen umfassen.
- Die Gesamtzahl der Organisationen darf nicht mehr als 200 betragen. Jeder Knoten in der Hierarchie zählt als Organisation.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun erfahren haben, wie Sie Azure IoT Central-Organisationen verwalten, werden als nächstes die Informationen empfohlen, wie Sie [IoT-Daten mithilfe des Datenexports in Cloudziele exportieren](howto-export-data.md).

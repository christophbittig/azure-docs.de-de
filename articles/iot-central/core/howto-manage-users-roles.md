---
title: Verwalten von Benutzern und Rollen in Ihrer Azure IoT Central-Anwendung | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie als Administrator Benutzer und Rollen in Ihrer Azure IoT Central-Anwendung verwalten.
author: lmasieri
ms.author: lmasieri
ms.date: 04/16/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: acb26e18fd208f73053505d6e3ab5cca0f33fd2e
ms.sourcegitcommit: cd7d099f4a8eedb8d8d2a8cae081b3abd968b827
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/25/2021
ms.locfileid: "112963201"
---
# <a name="manage-users-and-roles-in-your-iot-central-application"></a>Verwalten von Benutzern und Rollen in Ihrer IoT Central-Anwendung

In diesem Artikel wird beschrieben, wie Sie Benutzer in Ihrer Azure IoT Central-Anwendung hinzufügen, bearbeiten und löschen. Außerdem erfahren Sie, wie Sie Rollen in Ihrer Anwendung verwalten.

Um auf den Abschnitt **Verwaltung** zugreifen und diesen verwenden zu können, muss Ihnen die Rolle **Administrator** für eine Azure IoT Central-Anwendung zugewiesen sein. Wenn Sie eine Azure IoT Central-Anwendung erstellen, wird Ihnen automatisch die Rolle **Administrator** für die diese Anwendung hinzugefügt.

## <a name="add-users"></a>Hinzufügen von Benutzern

Jeder Benutzer muss ein Benutzerkonto besitzen, um sich bei einer Anwendung anmelden und darauf zugreifen zu können. Von IoT Central werden aktuell Microsoft-Konten und Azure Active Directory-Konten, aber keine Azure Active Directory-Gruppen unterstützt.

Weitere Informationen finden Sie unter [Hilfe zum Microsoft-Konto](https://support.microsoft.com/products/microsoft-account?category=manage-account) und [Vorgehensweise: Hinzufügen oder Löschen von Benutzern in Azure Active Directory](../../active-directory/fundamentals/add-users-azure-active-directory.md).

1. Um einen Benutzer einer IoT Central-Anwendung hinzuzufügen, rufen Sie im Abschnitt **Verwaltung** die Seite **Benutzer** auf.

    :::image type="content" source="media/howto-manage-users-roles/manage-users-pnp.png" alt-text="Screenshot: Verwalten von Benutzern":::

1. Wählen Sie auf der Seite **Benutzer** die Option **+ Benutzer zuweisen** aus, um einen Benutzer hinzuzufügen.

1. Wählen Sie aus der Dropdownliste **Rolle** eine Rolle für den Benutzer aus. Weitere Informationen zu Rollen finden Sie in diesem Artikel im Abschnitt [Verwalten von Rollen](#manage-roles).

    :::image type="content" source="media/howto-manage-users-roles/add-user-pnp.png" alt-text="Screenshot: Hinzufügen eines Benutzers und Auswählen einer Rolle":::

  > [!NOTE]
  > Ein Benutzer mit einer benutzerdefinierten Rolle, die ihm die Berechtigung zum Hinzufügen anderer Benutzer gewährt, kann nur Benutzer einer Rolle mit denselben oder niedrigeren Berechtigungen wie die eigene Rolle hinzufügen.

  > [!NOTE]
  > Wenn ein Benutzer aus Azure Active Directory gelöscht und anschließend erneut hinzugefügt wird, kann sich der Benutzer nicht mehr bei der IoT Central-Anwendung anmelden. Der Administrator der Anwendung muss den Benutzer auch in der Anwendung löschen und erneut hinzufügen, um den Zugriff zu reaktivieren.

### <a name="edit-the-roles-that-are-assigned-to-users"></a>Bearbeiten der den Benutzern zugewiesenen Rollen

Rollen können nach ihrer Zuweisung nicht mehr geändert werden. Um die einem Benutzer zugewiesene Rolle zu ändern, löschen Sie den Benutzer und fügen den Benutzer dann erneut mit einer anderen Rolle hinzu.

> [!NOTE]
> Die zugewiesenen Rollen gelten nur für die IoT Central-Anwendung und können nicht über das Azure-Portal verwaltet werden.

## <a name="delete-users"></a>Löschen von Benutzern

Aktivieren Sie zum Löschen von Benutzern auf der Seite **Benutzer** die entsprechenden Kontrollkästchen. Wählen Sie anschließend die Option **Löschen**.

## <a name="manage-roles"></a>Verwalten von Rollen

Mit Rollen können Sie steuern, wer in Ihrer Organisation verschiedene Aufgaben in IoT Central ausführen darf. Es gibt drei integrierte Rollen, die Sie Benutzern Ihrer Anwendung zuweisen können. Sie können auch [benutzerdefinierte Rollen erstellen](#create-a-custom-role), wenn Sie eine präzisere Steuerung benötigen.

:::image type="content" source="media/howto-manage-users-roles/manage-roles-pnp.png" alt-text="Screenshot: Auswahl von „Rollen verwalten“":::


### <a name="administrator"></a>Administrator

Benutzer mit der Rolle **Administrator** können jeden Aspekt der Anwendung verwalten und steuern, einschließlich der Abrechnung.

Dem Benutzer, der eine Anwendung erstellt, wird automatisch die Rolle **Administrator** zugewiesen. Es muss immer mindestens ein Benutzer mit der Rolle **Administrator** vorhanden sein.

### <a name="builder"></a>Generator

Benutzer mit der Rolle **Ersteller** können alle Aspekte der App verwalten, sie können jedoch keine Änderungen auf den Registerkarten „Verwaltung“ und „Kontinuierlicher Datenexport“ vornehmen.

### <a name="operator"></a>Operator

Benutzer mit der Rolle **Operator** können die Integrität und den Status des Geräts überwachen. Sie dürfen keine Änderungen an Gerätevorlagen vornehmen und die Anwendung nicht verwalten. Anwendungsoperatoren können Geräte hinzufügen und löschen, Gerätesätze verwalten sowie Analysen und Aufträge ausführen.

## <a name="create-a-custom-role"></a>Erstellen einer benutzerdefinierten Rolle

Wenn Sie für Ihre Lösung eine präzisere Zugriffssteuerung benötigen, können Sie Rollen mit benutzerdefinierten Berechtigungen erstellen. Um eine benutzerdefinierte Rolle zu erstellen, navigieren Sie im Abschnitt **Verwaltung** Ihrer Anwendung zur Seite **Rollen**. Wählen Sie dann **+ Neue Rolle** aus, und fügen Sie einen Namen und eine Beschreibung für die Rolle hinzu. Wählen Sie die Berechtigungen aus, die für die Rolle erforderlich sind, und wählen Sie dann **Speichern** aus.

Sie können Ihrer benutzerdefinierten Rolle Benutzer auf die gleiche Weise hinzufügen wie bei einer integrierten Rolle.

:::image type="content" source="media/howto-manage-users-roles/create-custom-role-pnp.png" alt-text="Screenshot: Erstellen einer benutzerdefinierten Rolle":::


### <a name="custom-role-options"></a>Optionen für benutzerdefinierte Rollen

Wenn Sie eine benutzerdefinierte Rolle definieren, wählen Sie den Berechtigungssatz aus, der einem Benutzer gewährt wird, wenn er Mitglied der Rolle ist. Einige Berechtigungen sind von anderen abhängig. Wenn Sie z. B. einer Rolle die Berechtigung zum Aktualisieren persönlicher Dashboards hinzufügen, wird automatisch auch die Berechtigung zum Anzeigen persönlicher Dashboards hinzugefügt. In den folgenden Tabellen werden die verfügbaren Berechtigungen und ihre Abhängigkeiten zusammengefasst, die Sie beim Erstellen von benutzerdefinierten Rollen verwenden können.

#### <a name="managing-devices"></a>Verwalten von Geräten

**Berechtigungen für Gerätevorlagen**

| Name | Abhängigkeiten |
| ---- | -------- |
| Sicht | Keine     |
| Verwalten | Sicht <br/> Weitere Abhängigkeiten: Anzeigen von Geräteinstanzen  |
| Vollzugriff | Anzeigen, verwalten <br/> Weitere Abhängigkeiten: Anzeigen von Geräteinstanzen |

**Berechtigungen für Geräteinstanzen**

| Name | Abhängigkeiten |
| ---- | -------- |
| Sicht | Keine <br/> Weitere Abhängigkeiten: Anzeigen von Gerätevorlagen und Gerätegruppen |
| Aktualisieren | Sicht <br/> Weitere Abhängigkeiten: Anzeigen von Gerätevorlagen und Gerätegruppen  |
| Erstellen | Sicht <br/> Weitere Abhängigkeiten: Anzeigen von Gerätevorlagen und Gerätegruppen  |
| Löschen | Sicht <br/> Weitere Abhängigkeiten: Anzeigen von Gerätevorlagen und Gerätegruppen  |
| Befehle ausführen | Aktualisieren, anzeigen <br/> Weitere Abhängigkeiten: Anzeigen von Gerätevorlagen und Gerätegruppen  |
| Anzeigen von Rohdaten | Sicht <br/> Weitere Abhängigkeiten: Anzeigen von Gerätevorlagen und Gerätegruppen  |
| Vollzugriff | Anzeigen, aktualisieren, erstellen, löschen, Befehle ausführen, Rohdaten anzeigen <br/> Weitere Abhängigkeiten: Anzeigen von Gerätevorlagen und Gerätegruppen  |

**Berechtigungen für Gerätegruppen**

| Name | Abhängigkeiten |
| ---- | -------- |
| Sicht | Keine <br/> Weitere Abhängigkeiten: Anzeigen von Gerätevorlagen und Geräteinstanzen |
| Aktualisieren | Sicht <br/> Weitere Abhängigkeiten: Anzeigen von Gerätevorlagen und Geräteinstanzen   |
| Erstellen | Anzeigen, aktualisieren <br/> Weitere Abhängigkeiten: Anzeigen von Gerätevorlagen und Geräteinstanzen   |
| Löschen | Sicht <br/> Weitere Abhängigkeiten: Anzeigen von Gerätevorlagen und Geräteinstanzen  |
| Vollzugriff | Anzeigen, aktualisieren, erstellen, löschen <br/> Weitere Abhängigkeiten: Anzeigen von Gerätevorlagen und Geräteinstanzen |

**Berechtigungen für die Gerätekonnektivitätsverwaltung**

| Name | Abhängigkeiten |
| ---- | -------- |
| Instanz lesen | Keine <br/> Weitere Abhängigkeiten: Anzeigen von Gerätevorlagen, Gerätegruppen und Geräteinstanzen |
| Instanz verwalten | Instanz lesen <br /> Weitere Abhängigkeiten: Anzeigen von Gerätevorlagen, Gerätegruppen und Geräteinstanzen |
| Global lesen | Keine   |
| Global verwalten | Global lesen |
| Vollzugriff | Instanz lesen, Instanz verwalten, global lesen, global verwalten <br/> Weitere Abhängigkeiten: Anzeigen von Gerätevorlagen, Gerätegruppen und Geräteinstanzen |

**Berechtigungen für Aufträge**

| Name | Abhängigkeiten |
| ---- | -------- |
| Sicht | Keine <br/> Weitere Abhängigkeiten: Anzeigen von Gerätevorlagen, Geräteinstanzen und Gerätegruppen |
| Aktualisieren | Sicht <br/> Weitere Abhängigkeiten: Anzeigen von Gerätevorlagen, Geräteinstanzen und Gerätegruppen |
| Erstellen | Anzeigen, aktualisieren <br/> Weitere Abhängigkeiten: Anzeigen von Gerätevorlagen, Geräteinstanzen und Gerätegruppen |
| Löschen | Sicht <br/> Weitere Abhängigkeiten: Anzeigen von Gerätevorlagen, Geräteinstanzen und Gerätegruppen |
| Execute | Sicht <br/> Weitere Abhängigkeiten: Anzeigen von Gerätevorlagen, Geräteinstanzen und Gerätegruppen; Aktualisieren von Geräteinstanzen; Ausführen von Befehlen auf Geräteinstanzen |
| Vollzugriff | Anzeigen, aktualisieren, erstellen, löschen, ausführen <br/> Weitere Abhängigkeiten: Anzeigen von Gerätevorlagen, Geräteinstanzen und Gerätegruppen; Aktualisieren von Geräteinstanzen; Ausführen von Befehlen auf Geräteinstanzen |

**Berechtigungen für Regeln**

| Name | Abhängigkeiten |
| ---- | -------- |
| Sicht | Keine <br/> Weitere Abhängigkeiten: Anzeigen von Gerätevorlagen |
| Aktualisieren | Sicht <br/> Weitere Abhängigkeiten: Anzeigen von Gerätevorlagen |
| Erstellen | Anzeigen, aktualisieren <br/> Weitere Abhängigkeiten: Anzeigen von Gerätevorlagen |
| Löschen | Sicht <br/> Weitere Abhängigkeiten: Anzeigen von Gerätevorlagen |
| Vollzugriff | Anzeigen, aktualisieren, erstellen, löschen <br/> Weitere Abhängigkeiten: Anzeigen von Gerätevorlagen |

#### <a name="managing-the-app"></a>App verwalten

**Berechtigungen für Anwendungseinstellungen**

| Name | Abhängigkeiten |
| ---- | -------- |
| Sicht | Keine     |
| Aktualisieren | Sicht   |
| Kopieren | Sicht <br/> Weitere Abhängigkeiten: Anzeigen von Gerätevorlagen, Geräteinstanzen, Gerätegruppen, Dashboards, Datenexporten, Brandings, Hilfelinks, benutzerdefinierten Rollen und Regeln |
| Löschen | Sicht   |
| Vollzugriff | Anzeigen, aktualisieren, kopieren, löschen <br/> Weitere Abhängigkeiten: Anzeigen von Gerätevorlagen, Gerätegruppen, Anwendungsdashboards, Datenexporten, Brandings, Hilfelinks, benutzerdefinierten Rollen und Regeln |

**Berechtigungen für den Export von Anwendungsvorlagen**

| Name | Abhängigkeiten |
| ---- | -------- |
| Sicht | Keine     |
| Exportieren | Sicht <br/> Weitere Abhängigkeiten: Anzeigen von Gerätevorlagen, Geräteinstanzen, Gerätegruppen, Dashboards, Datenexporten, Brandings, Hilfelinks, benutzerdefinierten Rollen und Regeln |
| Vollzugriff | Anzeigen, exportieren <br/> Weitere Abhängigkeiten: Anzeigen von Gerätevorlagen, Gerätegruppen, Anwendungsdashboards, Datenexporten, Brandings, Hilfelinks, benutzerdefinierten Rollen und Regeln |

**Berechtigungen zum Hochladen von Gerätedateien**

| Name | Abhängigkeiten |
| ---- | -------- |
| Sicht | Keine     |
| Verwalten | Sicht   |
| Vollzugriff | Anzeigen, verwalten |

**Berechtigungen für die Abrechnung**

| Name | Abhängigkeiten |
| ---- | -------- |
| Verwalten | Keine     |
| Vollzugriff | Verwalten |

#### <a name="managing-users-and-roles"></a>Benutzer und Schlüssel verwalten

**Berechtigungen für benutzerdefinierte Rollen**

| Name | Abhängigkeiten |
| ---- | -------- |
| Sicht | Keine |
| Aktualisieren | Sicht |
| Erstellen | Anzeigen, aktualisieren |
| Löschen | Sicht |
| Vollzugriff | Anzeigen, aktualisieren, erstellen, löschen |

**Berechtigungen für die Benutzerverwaltung**

| Name | Abhängigkeiten |
| ---- | -------- |
| Sicht | Keine <br/> Weitere Abhängigkeiten: Anzeigen benutzerdefinierter Rollen |
| Hinzufügen | Sicht <br/> Weitere Abhängigkeiten: Anzeigen benutzerdefinierter Rollen |
| Löschen | Sicht <br/> Weitere Abhängigkeiten: Anzeigen benutzerdefinierter Rollen |
| Vollzugriff | Anzeigen, hinzufügen, löschen <br/> Weitere Abhängigkeiten: Anzeigen benutzerdefinierter Rollen |

> [!NOTE]
> Ein Benutzer mit einer benutzerdefinierten Rolle, die ihm die Berechtigung zum Hinzufügen anderer Benutzer gewährt, kann nur Benutzer einer Rolle mit denselben oder niedrigeren Berechtigungen wie die eigene Rolle hinzufügen.

#### <a name="customizing-the-app"></a>App anpassen

**Berechtigungen für Anwendungsdashboards**

| Name | Abhängigkeiten |
| ---- | -------- |
| Sicht | Keine     |
| Aktualisieren | Sicht   |
| Erstellen | Anzeigen, aktualisieren |
| Löschen | Sicht   |
| Vollzugriff | Anzeigen, aktualisieren, erstellen, löschen |

**Berechtigungen für persönliche Dashboards**

| Name | Abhängigkeiten |
| ---- | -------- |
| Sicht | Keine     |
| Aktualisieren | Sicht   |
| Erstellen | Anzeigen, aktualisieren   |
| Löschen | Sicht   |
| Vollzugriff | Anzeigen, aktualisieren, erstellen, löschen |

**Berechtigungen für Branding, Favicons und Farben**

| Name | Abhängigkeiten |
| ---- | -------- |
| Sicht | Keine     |
| Aktualisieren | Sicht   |
| Vollzugriff | Anzeigen, aktualisieren |

**Berechtigungen für Hilfelinks**

| Name | Abhängigkeiten |
| ---- | -------- |
| Sicht | Keine     |
| Aktualisieren | Sicht   |
| Vollzugriff | Anzeigen, aktualisieren |

#### <a name="extending-the-app"></a>App erweitern

**Berechtigungen für Datenexporte**

| Name | Abhängigkeiten |
| ---- | -------- |
| Sicht | Keine     |
| Aktualisieren | Sicht   |
| Erstellen | Anzeigen, aktualisieren  |
| Löschen | Sicht   |
| Vollzugriff | Anzeigen, aktualisieren, erstellen, löschen |

**Berechtigungen für API-Token**

| Name | Abhängigkeiten |
| ---- | -------- |
| Sicht | Keine  <br/> Weitere Abhängigkeiten: Anzeigen benutzerdefinierter Rollen |
| Erstellen | Sicht <br/> Weitere Abhängigkeiten: Anzeigen benutzerdefinierter Rollen |
| Löschen | Sicht <br/> Weitere Abhängigkeiten: Anzeigen benutzerdefinierter Rollen |
| Vollzugriff | Anzeigen, erstellen, löschen <br/> Weitere Abhängigkeiten: Anzeigen benutzerdefinierter Rollen |

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun mit der Verwaltung von Benutzern und Rollen in Ihrer IoT Central-Anwendung vertraut sind, können Sie sich als Nächstes mit dem [Anpassen der Anwendungsbenutzeroberfläche](howto-customize-ui.md) befassen.

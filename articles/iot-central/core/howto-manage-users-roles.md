---
title: Verwalten von Benutzern und Rollen in Ihrer Azure IoT Central-Anwendung | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie als Administrator Benutzer und Rollen in Ihrer Azure IoT Central-Anwendung verwalten.
author: dominicbetts
ms.author: dobett
ms.date: 08/20/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 4398ab5ed46276c397e812cb4ffbcd1e6e1296e6
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "131997569"
---
# <a name="manage-users-and-roles-in-your-iot-central-application"></a>Verwalten von Benutzern und Rollen in Ihrer IoT Central-Anwendung

In diesem Artikel wird beschrieben, wie Sie Benutzer in Ihrer Azure IoT Central-Anwendung hinzufügen, bearbeiten und löschen. Außerdem erfahren Sie, wie Sie Rollen in Ihrer Anwendung verwalten.

Um auf den Abschnitt **Verwaltung** zugreifen und ihn verwenden zu können, muss Ihnen die Rolle **App-Administrator** für eine Azure IoT Central-Anwendung oder eine benutzerdefinierte Rolle mit Administratorberechtigungen zugewiesen sein. Wenn Sie eine Azure IoT Central-Anwendung erstellen, werden Sie der Rolle **Administrator** für diese Anwendung automatisch hinzugefügt.

## <a name="add-users"></a>Hinzufügen von Benutzern

Jeder Benutzer muss ein Benutzerkonto besitzen, um sich bei einer Anwendung anmelden und darauf zugreifen zu können. Von IoT Central werden aktuell Microsoft-Konten und Azure Active Directory-Konten, aber keine Azure Active Directory-Gruppen unterstützt.

Weitere Informationen finden Sie unter [Hilfe zum Microsoft-Konto](https://support.microsoft.com/products/microsoft-account?category=manage-account) und [Vorgehensweise: Hinzufügen oder Löschen von Benutzern in Azure Active Directory](../../active-directory/fundamentals/add-users-azure-active-directory.md).

1. Um einen Benutzer einer IoT Central-Anwendung hinzuzufügen, rufen Sie im Abschnitt **Verwaltung** die Seite **Benutzer** auf.

    :::image type="content" source="media/howto-manage-users-roles/manage-users-pnp.png" alt-text="Screenshot: Verwalten von Benutzern":::

1. Wählen Sie auf der Seite **Benutzer** die Option **+ Benutzer zuweisen** aus, um einen Benutzer hinzuzufügen.

1. Wenn Ihre Anwendung [Organisationen](howto-create-organizations.md) verwendet,wählen Sie im Dropdownmenü **Organisation** die Organisation aus, die dem Benutzer zugewiesen werden soll.

1. Wählen Sie aus der Dropdownliste **Rolle** eine Rolle für den Benutzer aus. Weitere Informationen zu Rollen finden Sie in diesem Artikel im Abschnitt [Verwalten von Rollen](#manage-roles).

    :::image type="content" source="media/howto-manage-users-roles/add-user-pnp.png" alt-text="Screenshot: Hinzufügen eines Benutzers und Auswählen einer Rolle":::

    Die verfügbaren Rollen sind abhängig von der Organisation, der der Benutzer zugeordnet ist. Sie können Rollen des Typs **App** Benutzern, die der Stammorganisation zugeordnet sind, und Rollen des Typs **Organisation** Benutzern zuweisen, die einer anderen Organisation in der Hierarchie zugeordnet sind.

    > [!NOTE]
    > Ein Benutzer mit einer benutzerdefinierten Rolle, die ihm die Berechtigung zum Hinzufügen anderer Benutzer gewährt, kann nur Benutzer einer Rolle mit denselben oder niedrigeren Berechtigungen wie die eigene Rolle hinzufügen.
  
    > [!NOTE]
    > Wenn ein Benutzer aus Azure Active Directory gelöscht und anschließend erneut hinzugefügt wird, kann sich der Benutzer nicht mehr bei der IoT Central-Anwendung anmelden. Der Administrator der Anwendung muss den Benutzer auch in der Anwendung löschen und erneut hinzufügen, um den Zugriff zu reaktivieren.

### <a name="edit-the-roles-and-organizations-that-are-assigned-to-users"></a>Bearbeiten der Rollen und Organisationen, die Benutzern zugewiesen sind

Rollen und Organisationen können nach ihrer Zuweisung nicht mehr geändert werden. Wenn Sie die einem Benutzer zugewiesene Rolle oder Organisation ändern möchten, löschen Sie den Benutzer, und fügen Sie ihn dann erneut mit einer anderen Rolle bzw. Organisation hinzu.

> [!NOTE]
> Die zugewiesenen Rollen gelten nur für die IoT Central-Anwendung und können nicht über das Azure-Portal verwaltet werden.

## <a name="delete-users"></a>Löschen von Benutzern

Aktivieren Sie zum Löschen von Benutzern auf der Seite **Benutzer** die entsprechenden Kontrollkästchen. Wählen Sie anschließend die Option **Löschen**.

## <a name="manage-roles"></a>Verwalten von Rollen

Mit Rollen können Sie steuern, wer in Ihrer Organisation verschiedene Aufgaben in IoT Central ausführen darf. Es gibt drei integrierte Rollen, die Sie Benutzern Ihrer Anwendung zuweisen können. Sie können auch [benutzerdefinierte Rollen erstellen](#create-a-custom-role), wenn Sie eine präzisere Steuerung benötigen.

:::image type="content" source="media/howto-manage-users-roles/manage-roles-pnp.png" alt-text="Screenshot: Auswahl von „Rollen verwalten“":::

### <a name="app-administrator"></a>App-Administrator

Benutzer mit der Rolle **App-Administrator** können jeden Teil der Anwendung verwalten und steuern, einschließlich der Abrechnung.

Dem Benutzer, der eine Anwendung erstellt, wird die Rolle **App-Administrator** automatisch zugewiesen. Es muss immer mindestens einen Benutzer mit der Rolle **App-Administrator** geben.

### <a name="app-builder"></a>App-Ersteller

Benutzer mit der Rolle **App-Ersteller** können jeden Teil der App verwalten, aber keine Änderungen auf den Registerkarten „Verwaltung“ oder „Kontinuierlicher Datenexport“ vornehmen.

### <a name="app-operator"></a>App-Operator

Benutzer mit der Rolle **App-Operator** können die Integrität und den Status des Geräts überwachen. Sie dürfen keine Änderungen an Gerätevorlagen vornehmen und die Anwendung nicht verwalten. Anwendungsoperatoren können Geräte hinzufügen und löschen, Gerätesätze verwalten sowie Analysen und Aufträge ausführen.

### <a name="org-administrator"></a>Org Administrator (Organisationsadministrator)

IoT Central fügt diese Rolle automatisch hinzu, wenn Sie Ihrer Anwendung eine Organisation hinzufügen. Diese Rolle schränkt den Zugriff von Organisationsadministratoren bei einigen anwendungsweiten Funktionen wie Abrechnung, Branding, Farben, API-Token und Registrierungsgruppeninformationen ein.

Benutzer mit der Rolle **Organisationsadministrator** können Benutzer zur Anwendung einladen, Unterorganisationen innerhalb ihrer Organisationshierarchie erstellen und die Geräte innerhalb ihrer Organisation verwalten.

### <a name="org-operator"></a>Organisationsoperator

IoT Central fügt diese Rolle automatisch hinzu, wenn Sie Ihrer Anwendung eine Organisation hinzufügen. Diese Rolle schränkt Organisationsoperators beim Zugriff auf einige anwendungsweite Funktionen ein.

Benutzer mit der Rolle **Organisationsoperator** können Aufgaben wie das Hinzufügen von Geräten, Ausführen von Befehlen, Anzeigen von Gerätedaten, Erstellen von Dashboards und Erstellen von Gerätegruppen ausführen.

### <a name="org-viewer"></a>Org Viewer (Organisationszuschauer)

IoT Central fügt diese Rolle automatisch hinzu, wenn Sie Ihrer Anwendung eine Organisation hinzufügen.

Benutzer mit der Rolle **Org Viewer** können Elemente wie Geräte und deren Daten, Organisationsdashboards, Gerätegruppen und Gerätevorlagen anzeigen.

## <a name="create-a-custom-role"></a>Erstellen einer benutzerdefinierten Rolle

Wenn Sie für Ihre Lösung eine präzisere Zugriffssteuerung benötigen, können Sie Rollen mit benutzerdefinierten Berechtigungen erstellen. Um eine benutzerdefinierte Rolle zu erstellen, navigieren Sie im Abschnitt **Verwaltung** Ihrer Anwendung zur Seite **Rollen**, und wählen Sie eine der folgenden Optionen aus:

- Wählen Sie **+ Neu** aus, fügen Sie einen Namen und eine Beschreibung für Ihre Rolle hinzu, und wählen Sie **Anwendung** oder **Organisation** als Rollentyp aus. Bei dieser Option können Sie eine Rollendefinition von Grund auf neu erstellen.
- Navigieren Sie zu einer vorhandenen Rolle, und wählen Sie **Kopieren** aus. Bei dieser Option können Sie mit einer vorhandenen Rollendefinition beginnen und sie dann anpassen.

:::image type="content" source="media/howto-manage-users-roles/create-custom-role-pnp.png" alt-text="Screenshot: Erstellen einer benutzerdefinierten Rolle":::

> [!WARNING]
> Nachdem Sie eine Rolle erstellt haben, können Sie den Rollentyp nicht mehr ändern.

Wenn Sie einen Benutzer zu Ihrer Anwendung einladen und ihn Folgendem zuordnen:

- – der Stammorganisation. Dann sind nur Rollen des Typs **Anwendungsrollen** verfügbar.
- – einer beliebigen anderen Organisation. Dann sind nur Rollen des Typs **Organisation** verfügbar.

Sie können Ihrer benutzerdefinierten Rolle Benutzer genauso wie bei einer integrierten Rolle hinzufügen.

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

**Berechtigungen für die Organisationsverwaltung**

| Name | Abhängigkeiten |
| ---- | -------- |
| Sicht | Keine |
| Aktualisieren | Sicht |
| Erstellen | Anzeigen, aktualisieren |
| Löschen | Sicht |
| Vollzugriff | Anzeigen, aktualisieren, erstellen, löschen |

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

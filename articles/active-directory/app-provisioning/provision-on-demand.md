---
title: Bedarfsorientierte Bereitstellung von Benutzern mithilfe von Azure Active Directory
description: Erfahren Sie, wie Sie Benutzer bei Bedarf in Azure Active Directory bestimmen.
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 05/11/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 021e582887ec2927fa1e3180c41c522bd0e09fbf
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "129990955"
---
# <a name="on-demand-provisioning-in-azure-active-directory"></a>On-demand Benutzerbereitstellung in Azure Active Directory
Mit der bedarfsorientierten Bereitstellung können Sie einen Benutzer innerhalb von Sekunden in einer Anwendung bereitstellen. Unter anderem können Sie dieses Feature für Folgendes verwenden:

* zum schnellen Behandeln von Konfigurationsproblemen
* zum Überprüfen von definierten Ausdrücken
* zum Testen von Bereichsfiltern

## <a name="how-to-use-on-demand-provisioning"></a>Verwenden der bedarfsorientierten Bereitstellung

1. Melden Sie sich beim **Azure-Portal** an.
1. Navigieren Sie zu **Alle Dienste** > **Unternehmensanwendungen**.
1. Wählen Sie Ihre Anwendung aus, und wechseln Sie zur Konfigurationsseite für die Bereitstellung.
1. Konfigurieren Sie die Bereitstellung, indem Sie Ihre Administratoranmeldeinformationen angeben.
1. Klicken Sie auf **Bei Bedarf bereitstellen**.
1. Suchen Sie anhand von Vorname, Nachname, Anzeigename, Benutzerprinzipalname oder E-Mail-Adresse nach einem Benutzer.
   > [!NOTE]
   > Für die HR-Cloudbereitstellungs-App (Workday/SuccessFactors zu AD/Azure AD) wird ein anderer Eingabewert verwendet. Geben Sie bei einem Workday-Szenario für den Benutzer in Workday „WID“ an. Geben Sie bei einem SuccessFactors-Szenario den Wert „personIdExternal“ des Benutzers in SuccessFactors an. 
 
1. Klicken Sie unten auf der Seite auf **Bereitstellen**.

:::image type="content" source="media/provision-on-demand/on-demand-provision-user.jpg" alt-text="Screenshot: Benutzeroberfläche des Azure-Portals für die bedarfsorientierte Benutzerbereitstellung":::

## <a name="understand-the-provisioning-steps"></a>Grundlegendes zu den Bereitstellungsschritten

Am bedarfsorientierten Bereitstellungsprozess sollen die Schritte veranschaulicht werden, die der Bereitstellungsdienst beim Bereitstellen eines Benutzers ausführt. Die Bereitstellung eines Benutzers umfasst in der Regel fünf Schritte. Einer oder mehrere dieser Schritte, die in den folgenden Abschnitten erläutert werden, werden während der bedarfsorientierten Bereitstellung veranschaulicht.

### <a name="step-1-test-connection"></a>Schritt 1: Verbindung testen

Der Bereitstellungsdienst versucht, den Zugriff auf die Zielanwendung zu autorisieren, indem er eine Anforderung für einen „Testbenutzer“ ausgibt. Der Bereitstellungsdienst erwartet die Antwort, dass der Dienst autorisiert ist, um mit den Bereitstellungsschritten fortzufahren. Dieser Schritt tritt nur bei einem Fehler auf. Wenn der Schritt erfolgreich ist, wird er in der Umgebung für die bedarfsorientierte Bereitstellung nicht angezeigt.

#### <a name="troubleshooting-tips"></a>Tipps zur Problembehandlung

* Stellen Sie sicher, dass Sie gültige Anmeldeinformationen (z. B. das geheime Token und die Mandanten-URL) für die Zielanwendung bereitgestellt haben. Welche Anmeldeinformationen erforderlich sind, hängt von der jeweiligen Anwendung ab. Ausführliche Tutorials für die Konfiguration finden Sie in [dieser Liste](../saas-apps/tutorial-list.md). 
* Stellen Sie sicher, dass die Zielanwendung das Filtern anhand von übereinstimmenden Attributen unterstützt, die im Bereich **Attributzuordnungen** definiert wurden. Unter Umständen müssen Sie die unterstützten Filter in der vom Anwendungsentwickler bereitgestellten API-Dokumentation nachschlagen.
* Für SCIM-Anwendungen (System for Cross-Domain Identity Management) können Sie ein Tool wie Postman verwenden. Mithilfe solcher Tools können Sie sicherstellen, dass die Anwendung so auf Autorisierungsanforderungen reagiert, wie der Azure AD-Bereitstellungsdienst (Azure Active Directory) dies erwartet. Hier finden Sie eine [Beispielanforderung](./use-scim-to-provision-users-and-groups.md#request-3).

### <a name="step-2-import-user"></a>Schritt 2: Benutzer importieren

Als Nächstes ruft der Bereitstellungsdienst den Benutzer aus dem Quellsystem ab. Die Benutzerattribute, die vom Dienst abgerufen werden, werden später für Folgendes verwendet:

* zur Überprüfung, ob der Benutzer für die Bereitstellung infrage kommt
* zur Überprüfung des Zielsystems auf einen vorhandenen Benutzer
* zum Bestimmen, welche Benutzerattribute in das Zielsystem exportiert werden sollen

#### <a name="view-details"></a>Anzeigen der Details


Im Abschnitt **Details anzeigen** werden die Eigenschaften des Benutzers angezeigt, die aus dem Quellsystem (z. B. Azure AD) importiert wurden.

#### <a name="troubleshooting-tips"></a>Tipps zur Problembehandlung

* Beim Importieren des Benutzers kann ein Fehler auftreten, wenn im Benutzerobjekt im Zielsystem das übereinstimmende Attribut fehlt. Testen Sie einen der folgenden Ansätze, um diesen Fehler zu beheben:

  * Aktualisieren Sie das Benutzerobjekt mit einem Wert für das übereinstimmende Attribut.
  * Ändern Sie das übereinstimmende Attribut in der Bereitstellungskonfiguration.

* Wenn ein erwartetes Attribut in der importierten Liste fehlt, vergewissern Sie sich, dass das Attribut im Benutzerobjekt im Quellsystem mit einem Wert versehen ist. Der Bereitstellungsdienst unterstützt derzeit keine Attribute mit Nullwert.
* Stellen Sie sicher, dass die Seite **Attributzuordnung** der Bereitstellungskonfiguration das erwartete Attribut enthält.

### <a name="step-3-determine-if-user-is-in-scope"></a>Schritt 3: Gültigkeitsbereich des Benutzers ermitteln

Als Nächstes ermittelt der Bereitstellungsdienst, ob sich der Benutzer im [Gültigkeitsbereich](./how-provisioning-works.md#scoping) für die Bereitstellung befindet. Der Dienst berücksichtigt unter anderem folgende Aspekte:

* ob der Benutzer der Anwendung zugewiesen ist
* ob der Bereich auf **Sync assigned** (Zugewiesene synchronisieren) oder **Sync all** (Alle synchronisieren) festgelegt ist
* in Ihrer Bereitstellungskonfiguration definierte Bereichsfilter  

#### <a name="view-details"></a>Anzeigen der Details

Im Abschnitt **Details anzeigen** werden die ausgewerteten Bedingungen für den Gültigkeitsbereich angezeigt. Möglicherweise wird eine oder mehrere der folgenden Eigenschaften angezeigt:

* **Aktiv im Quellsystem**: Für den Benutzer wurde in Azure AD die Eigenschaft `IsActive` auf **true** festgelegt.
* **Der Anwendung zugewiesen**: Der Benutzer ist in Azure AD der Anwendung zugewiesen.
* **Gültigkeitsbereich „Alle synchronisieren“** : Diese Einstellung für den Gültigkeitsbereich lässt alle Benutzer und Gruppen im Mandanten zu.
* **Benutzer hat erforderliche Rolle**: Der Benutzer verfügt über die Rollen, die für eine Bereitstellung in der Anwendung erforderlich sind. 
* **Bereichsfilter**: Diese Eigenschaft wird ebenfalls angezeigt, wenn Sie Bereichsfilter für Ihre Anwendung definiert haben. Der Filter wird in folgendem Format angezeigt: {Titel des Bereichsfilters} {Attribut des Bereichsfilters} {Operator des Bereichsfilters} {Wert des Bereichsfilters}.

#### <a name="troubleshooting-tips"></a>Tipps zur Problembehandlung

* Stellen Sie sicher, dass Sie eine gültige Bereichsrolle definiert haben. Vermeiden Sie beispielsweise den Operator [Greater_Than](./define-conditional-rules-for-provisioning-user-accounts.md#create-a-scoping-filter) bei nicht ganzzahligen Werten.
* Lesen Sie die [Tipps für die Bereitstellung von Benutzern mit der Standardzugriffsrolle](./application-provisioning-config-problem-no-users-provisioned.md#provisioning-users-assigned-to-the-default-access-role), wenn ein Benutzer die erforderliche Rolle nicht besitzt.

### <a name="step-4-match-user-between-source-and-target"></a>Schritt 4: Benutzer zwischen Quell- und Zielsystem abgleichen

In diesem Schritt versucht der Dienst, den im Importschritt abgerufenen Benutzer mit einem Benutzer im Zielsystem abzugleichen.

#### <a name="view-details"></a>Anzeigen der Details

Die Seite **Details anzeigen** enthält die Eigenschaften der Benutzer, die mit dem Zielsystem abgeglichen wurden. Die im Kontextbereich angezeigten Eigenschaften variieren wie folgt:

* Wenn es im Zielsystem keine übereinstimmenden Benutzer gibt, werden keine Eigenschaften angezeigt.
* Wenn es einen übereinstimmenden Benutzer im Zielsystem gibt, werden die Eigenschaften des abgeglichenen Benutzers im Zielsystem angezeigt.
* Wenn es mehrere übereinstimmende Benutzer gibt, werden die Eigenschaften dieser abgeglichenen Benutzer angezeigt.
* Wenn Ihre Attributzuordnungen mehrere übereinstimmende Attribute enthalten, werden die einzelnen übereinstimmenden Attribute nacheinander ausgewertet, und die abgeglichenen Benutzer für diese werden angezeigt.

#### <a name="troubleshooting-tips"></a>Tipps zur Problembehandlung

* Der Bereitstellungsdienst kann einen Benutzer im Quellsystem ggf. nicht eindeutig mit einem Benutzer im Zielsystem abgleichen. Beheben Sie dieses Problem, indem Sie sicherstellen, dass das abzugleichende Attribut eindeutig ist.
* Stellen Sie sicher, dass die Zielanwendung das Filtern anhand des abzugleichenden Attributs unterstützt.  

### <a name="step-5-perform-action"></a>Schritt 5: Aktion ausführen

Schließlich führt der Bereitstellungsdienst eine Aktion (z. B. Erstellen, Aktualisieren, Löschen oder Überspringen des Benutzers) aus.

Hier sehen Sie ein Beispiel für die Anzeige nach der erfolgreichen bedarfsorientierten Bereitstellung eines Benutzers:

:::image type="content" source="media/provision-on-demand/success-on-demand-provision.jpg" alt-text="Screenshot: erfolgreiche bedarfsorientierte Bereitstellung eines Benutzers":::

#### <a name="view-details"></a>Anzeigen der Details

Im Abschnitt **Details anzeigen** werden die Attribute angezeigt, die in der Zielanwendung geändert wurden. Diese Anzeige beinhaltet das endgültige Ergebnis der Aktivität des Bereitstellungsdiensts und die exportierten Attribute. Wenn bei diesem Schritt ein Fehler auftritt, handelt es sich bei den angezeigten Attributen um die Attribute, die der Bereitstellungsdienst zu ändern versucht hat.

#### <a name="troubleshooting-tips"></a>Tipps zur Problembehandlung

* Die Fehler, die beim Exportieren von Änderungen auftreten, können sehr unterschiedlich sein. In der [Dokumentation zur Protokollbereitstellung](../reports-monitoring/concept-provisioning-logs.md#error-codes) werden häufige Fehler beschrieben.

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

* **Muss man die Bereitstellung deaktivieren, um die bedarfsorientierte Bereitstellung zu verwenden?** Bei Anwendungen, die ein langlebiges Bearertoken oder die Kombination aus Benutzername und Kennwort für die Autorisierung verwenden, sind keine weiteren Schritte erforderlich. Bei Anwendungen, die OAuth für die Autorisierung verwenden, muss derzeit der Bereitstellungsauftrag angehalten werden, bevor die bedarfsorientierte Bereitstellung verwendet werden kann. Anwendungen wie G Suite, Box, Workplace by Facebook und Slack gehören zu dieser Kategorie. Wir arbeiten daran, die bedarfsorientierte Bereitstellung für alle Anwendungen zu unterstützen, ohne Bereitstellungsaufträge anhalten zu müssen.

* **Wie lange dauert die bedarfsorientierte Bereitstellung?** Die bedarfsorientierte Bereitstellung dauert in der Regel weniger als 30 Sekunden.

## <a name="known-limitations"></a>Bekannte Einschränkungen

Zurzeit gibt es einige bekannte Einschränkungen bei der bedarfsorientierten Bereitstellung. Reichen Sie [Vorschläge und Feedback](https://aka.ms/appprovisioningfeaturerequest) ein, damit wir über kommende Verbesserungen entscheiden können.

> [!NOTE]
> Die folgenden Einschränkungen gelten nur für die bedarfsorientierte Bereitstellung. Weitere Informationen dazu, ob eine Anwendung Bereitstellungsgruppen, Löschungen oder andere Features unterstützt, finden Sie im Tutorial für die jeweilige Anwendung.

* Die AWS-Anwendung (Amazon Web Services) unterstützt die bedarfsgesteuerte Bereitstellung nicht. 
* Die bedarfsorientierte Bereitstellung von Gruppen und Rollen wird nicht unterstützt.
* Die bedarfsorientierte Bereitstellung unterstützt das Deaktivieren von Benutzern, deren Zuweisung zur Anwendung aufgehoben wurde. Das Deaktivieren oder Löschen von Benutzern, die in Azure AD deaktiviert oder gelöscht wurden, wird jedoch nicht unterstützt. Diese Benutzer werden bei der Benutzersuche nicht angezeigt.

## <a name="next-steps"></a>Nächste Schritte

* [Behandeln von Bereitstellungsproblemen](./application-provisioning-config-problem.md)
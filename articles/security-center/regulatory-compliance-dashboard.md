---
title: 'Tutorial: Überprüfungen der Einhaltung gesetzlicher Bestimmungen – Microsoft Defender für Cloud'
description: 'Tutorial: Hier erfahren Sie, wie Sie die Einhaltung gesetzlicher Bestimmungen mit Microsoft Defender für Cloud verbessern.'
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: tutorial
ms.date: 08/09/2021
ms.author: memildin
ms.openlocfilehash: 6051f0625408345b0e8665bb3ddcccf97e940367
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131452849"
---
# <a name="tutorial-improve-your-regulatory-compliance"></a>Tutorial: Verbessern der Einhaltung gesetzlicher Vorschriften

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Mit Microsoft Defender für Cloud kann der Prozess zur Einhaltung gesetzlicher Anforderungen optimiert werden, indem das **Dashboard für die Einhaltung gesetzlicher Bestimmungen** verwendet wird. 

Ihre Hybrid Cloud-Umgebung wird kontinuierlich von Defender für Cloud bewertet, um die Risikofaktoren gemäß den Kontrollen und bewährten Methoden in den Standards, die auf Ihre Abonnements angewendet wurden, zu analysieren. Das Dashboard gibt den Status Ihrer Einhaltung dieser Standards wieder. 

Wenn Sie Defender für Cloud für ein Azure-Abonnement aktivieren, wird diesem Abonnement automatisch der [Azure-Sicherheitsvergleichstest](/security/benchmark/azure/introduction) zugewiesen. Diese weit verbreitete Benchmark basiert auf den Kontrollen des [Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) und des [National Institute of Standards and Technology (NIST)](https://www.nist.gov/) und konzentriert sich auf cloudzentrierte Sicherheit.

Im Dashboard zur Einhaltung gesetzlicher Bestimmungen wird der Status aller Bewertungen angezeigt, die in Ihrer Umgebung für Ihre ausgewählten Standards und Bestimmungen durchgeführt werden. Wenn Sie aufgrund der Empfehlungen Maßnahmen ergreifen und die Risikofaktoren Ihrer Umgebung reduzieren, verbessert sich Ihr Konformitätsstatus.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Evaluieren der Einhaltung gesetzlicher Bestimmungen im entsprechenden Dashboard
> * Verbessern Ihres Konformitätsstatus durch das Ergreifen von Maßnahmen aufgrund von Empfehlungen
> * Herunterladen von PDF-/CSV-Berichten sowie Zertifizierungsberichten zu Ihrem Konformitätsstatus
> * Einrichten von Warnungen zu Änderungen Ihres Konformitätsstatus
> * Exportieren Ihrer Konformitätsdaten als kontinuierlichen Datenstrom und als wöchentliche Momentaufnahmen

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Zum Durchlaufen der in diesem Tutorial behandelten Features müssen folgende Voraussetzungen erfüllt sein:

- [Aktivieren Sie erweiterte Sicherheitsfeatures.](defender-for-cloud-introduction.md) Sie können diese 30 Tage lang kostenlos aktivieren.
- Sie müssen mit einem Konto angemeldet sein, das über Lesezugriff auf die Daten zur Richtlinienkonformität verfügt. (**Sicherheitsleseberechtigter** reicht nicht aus.) Hierzu kann die Rolle **Globaler Leser** für das Abonnement verwendet werden. Ihnen müssen mindestens die Rollen **Mitwirkender bei Ressourcenrichtlinien** und **Sicherheitsadministrator** zugewiesenen sein.

## <a name="assess-your-regulatory-compliance"></a>Bewerten der Einhaltung gesetzlicher Bestimmungen

Auf dem Dashboard zur Einhaltung gesetzlicher Bestimmungen werden Ihre ausgewählten Konformitätsstandards mit allen zugehörigen Anforderungen angezeigt. Die unterstützten Anforderungen sind hierbei jeweils den entsprechenden Sicherheitsbewertungen zugeordnet. Der Status dieser Bewertungen spiegelt Ihre Einhaltung des Standards wider.

Verwenden Sie das Dashboard zur Einhaltung gesetzlicher Bestimmungen, um sich auf Konformitätslücken im Zusammenhang mit den von Ihnen ausgewählten Standards und Bestimmungen zu konzentrieren. Diese fokussierte Ansicht ermöglicht auch eine kontinuierliche Überwachung Ihrer Compliance in dynamischen Cloud- und Hybridumgebungen.

1. Wählen Sie im Menü „Defender für Cloud“ die Option **Einhaltung gesetzlicher Bestimmungen** aus.

    Im oberen Bildschirmbereich befindet sich ein Dashboard mit einer Übersicht über Ihren Konformitätsstatus und die unterstützten Bestimmungen. Es sind Ihr Gesamtstatus der Konformität und die Anzahl von bestandenen und nicht bestandenen Bewertungen der einzelnen Standards angegeben.

    :::image type="content" source="./media/regulatory-compliance-dashboard/compliance-dashboard.png" alt-text="Dashboard für die Einhaltung gesetzlicher Bestimmungen." lightbox="./media/regulatory-compliance-dashboard/compliance-dashboard.png":::

1. Wählen Sie eine Registerkarte für einen Konformitätsstandard aus, der für Sie relevant ist (1). Sie sehen, auf welche Abonnements der Standard angewendet wird (2), und die Liste mit allen Kontrollen für den Standard (3) wird angezeigt. Sie können für eine Kontrolle jeweils die Details zu den bestandenen und nicht bestandenen Bewertungen (4) sowie die Anzahl betroffener Ressourcen (5) anzeigen. Einige Steuerelemente sind ausgegraut. Diese Steuerelemente sind keinen Defender für Cloud-Bewertungen zugeordnet. Überprüfen Sie deren Anforderungen, und bewerten Sie sie in Ihrer Umgebung. Einige sind möglicherweise prozessbezogen und nicht technischer Art.

    :::image type="content" source="./media/regulatory-compliance-dashboard/compliance-drilldown.png" alt-text="Erkunden der Compliancedetails für einen bestimmten Standard.":::

## <a name="improve-your-compliance-posture"></a>Verbessern Ihres Konformitätsstatus

Verbessern Sie mit den Informationen im Dashboard für die Einhaltung gesetzlicher Bestimmungen Ihren Konformitätsstatus, indem Sie direkt im Dashboard Maßnahmen zu den Empfehlungen ergreifen.

1.  Wählen Sie auf dem Dashboard eine beliebige der nicht bestandenen Bewertungen aus, um die Details zur jeweiligen Empfehlung anzuzeigen. Jede Empfehlung enthält verschiedene Problembehandlungsschritte.

1.  Wählen Sie eine bestimmte Ressource aus, um weitere Details anzuzeigen und die Ursache der Empfehlung für diese Ressource zu beheben. <br>Wählen Sie unter dem Standard **Azure CIS 1.1.0** beispielsweise die Empfehlung **Datenträgerverschlüsselung sollte auf virtuelle Computer angewendet werden** aus.

    :::image type="content" source="./media/regulatory-compliance-dashboard/sample-recommendation.png" alt-text="Auswahl einer Empfehlung aus einem Standard führt direkt zur Seite mit den Details der Empfehlung.":::

1. Wenn Sie in diesem Beispiel auf der Seite mit den Empfehlungsdetails die Option **Aktion ausführen** auswählen, gelangen Sie zu den Azure Virtual Machine-Seiten im Azure-Portal. Hier können Sie die Verschlüsselung auf der Registerkarte **Sicherheit** aktivieren:

    :::image type="content" source="./media/regulatory-compliance-dashboard/encrypting-vm-disks.png" alt-text="Schaltfläche „Aktion ausführen“ auf der Seite mit den Details zur Empfehlung führt zu den Lösungsoptionen.":::

    Weitere Informationen zum Anwenden von Empfehlungen finden Sie unter [Implementieren von Sicherheitsempfehlungen in Microsoft Defender für Cloud](review-security-recommendations.md).

1.  Nachdem Sie Schritte zur Umsetzung von Empfehlungen ausgeführt haben, wird das Ergebnis im Bericht des Compliancedashboards angezeigt, da sich Ihre Compliancebewertung verbessert hat.

    > [!NOTE]
    > Da die Bewertungen ungefähr alle zwölf Stunden durchgeführt werden, sehen die Auswirkungen auf Ihre Konformitätsdaten jeweils erst nach der nächsten Ausführung der relevanten Bewertung.

## <a name="generate-compliance-status-reports-and-certificates"></a>Generieren von Konformitätsstatusberichten und -zertifikaten

- Wählen Sie **Bericht herunterladen** aus, um einen PDF-Bericht mit einer Zusammenfassung Ihres aktuellen Compliancestatus für einen bestimmten Standard zu generieren.

    Der Bericht enthält eine allgemeine Zusammenfassung Ihres Konformitätsstatus für den ausgewählten Standard auf der Grundlage von Defender für Cloud-Bewertungsdaten. Der Bericht ist nach den Kontrollen dieses bestimmten Standards strukturiert. Der Bericht kann an Stakeholder weitergegeben und als Nachweis bei internen und externen Prüfungen genutzt werden.

    :::image type="content" source="./media/regulatory-compliance-dashboard/download-report.png" alt-text="Verwenden der Symbolleiste im Dashboard für die Einhaltung gesetzlicher Bestimmungen von Defender für Cloud, um Konformitätsberichte herunterladen":::

- Verwenden Sie die Option **Überwachungsberichte**, um **Zertifizierungsberichte** von Azure und Dynamics für die auf Ihre Abonnements angewendeten Standards herunterzuladen. 

    :::image type="content" source="media/release-notes/audit-reports-regulatory-compliance-dashboard.png" alt-text="Verwenden der Symbolleiste im Dashboard für die Einhaltung gesetzlicher Bestimmungen, um Azure- und Dynamics-Zertifizierungsberichte herunterzuladen":::

    Wählen Sie die Registerkarte für die relevanten Berichtstypen (PCI, SOC, ISO usw.) aus und verwenden Sie die Filter, um nach den benötigten spezifischen Berichten zu suchen:

    :::image type="content" source="media/release-notes/audit-reports-list-regulatory-compliance-dashboard-ga.png" alt-text="Filtern der Liste der verfügbaren Azure-Überwachungsberichte mithilfe von Registerkarten und Filtern.":::

    Auf der Registerkarte „PCI“ können Sie z. B. eine ZIP-Datei herunterladen, die ein digital signiertes Zertifikat enthält, das die Konformität von Microsoft Azure, Dynamics 365 und anderen Onlinediensten mit dem ISO22301-Framework nachweist, zusammen mit den erforderlichen Unterlagen zur Interpretation und Präsentation des Zertifikats. 

    > [!NOTE]
    > Wenn Sie einen dieser Zertifizierungsberichte herunterladen, wird Ihnen der folgende Datenschutzhinweis angezeigt:
    > 
    > _Durch das Herunterladen dieser Datei geben Sie Microsoft die Erlaubnis, den aktuellen Benutzer und die ausgewählten Abonnements zum Zeitpunkt des Downloads zu speichern. Diese Daten werden verwendet, um Sie im Falle von Änderungen oder Aktualisierungen des heruntergeladenen Auditberichts zu benachrichtigen. Diese Daten werden von Microsoft und den Überwachungsfirmen, welche die Zertifizierung/Berichte erstellen, nur verwendet, wenn eine Benachrichtigung erforderlich ist._

## <a name="configure-frequent-exports-of-your-compliance-status-data"></a>Konfigurieren häufiger Exporte Ihrer Konformitätsstatusdaten

Wenn Sie Ihren Konformitätsstatus mit anderen Überwachungstools in Ihrer Umgebung nachverfolgen möchten, können Sie den einfachen Exportmechanismus von Defender für Cloud nutzen. Konfigurieren Sie den **fortlaufenden Export**, um ausgewählte Daten an eine Azure Event Hub-Instanz oder einen Log Analytics-Arbeitsbereich zu senden. Weitere Informationen finden Sie unter [Fortlaufendes Exportieren von Defender für Cloud-Daten](continuous-export.md).

Verwenden Sie die Daten des fortlaufenden Exports an eine Azure Event Hub-Instanz oder einen Log Analytics-Arbeitsbereich wie folgt:

- Exportieren Sie alle Daten, die für die Einhaltung der gesetzlichen Vorgaben relevant sind, als **kontinuierlichen Datenstrom**:

    :::image type="content" source="media/regulatory-compliance-dashboard/export-compliance-data-stream.png" alt-text="Fortlaufender Export von Daten zur Einhaltung gesetzlicher Bestimmungen." lightbox="media/regulatory-compliance-dashboard/export-compliance-data-stream.png":::

- Exportieren Sie **wöchentliche Momentaufnahmen** Ihrer Daten zur Einhaltung gesetzlicher Bestimmungen:

    :::image type="content" source="media/regulatory-compliance-dashboard/export-compliance-data-snapshot.png" alt-text="Fortlaufender Export einer wöchentlichen Momentaufnahme mit Daten zur Einhaltung gesetzlicher Bestimmungen." lightbox="media/regulatory-compliance-dashboard/export-compliance-data-snapshot.png":::

> [!TIP]
> Sie können Berichte für einen einzelnen Zeitpunkt auch manuell direkt aus dem Dashboard für die Einhaltung gesetzlicher Bestimmungen exportieren. Generieren Sie diese **PDF-/CSV-Berichte** oder **Azure- und Dynamics-Zertifizierungsberichte** mithilfe der Symbolleistenoptionen **Bericht herunterladen** oder **Prüfberichte**. Weitere Informationen finden Sie unter [Bewerten der Einhaltung gesetzlicher Bestimmungen](#assess-your-regulatory-compliance). 


## <a name="run-workflow-automations-when-there-are-changes-to-your-compliance"></a>Durchführen von Workflowautomatisierungen bei Änderungen Ihres Konformitätsstatus

Das Feature zur Workflowautomatisierung von Defender für Cloud kann Logic Apps auslösen, wenn sich der Zustand einer Ihrer Bewertungen der Einhaltung gesetzlicher Bestimmungen ändert.

Beispielsweise könnten Sie es wünschen, dass Defender für Cloud einem*einer bestimmtem*bestimmten Benutzer*in eine E-Mail sendet, wenn bei einer Konformitätsbewertung ein Fehler auftritt. Sie müssen zunächst die Logik-App (mithilfe von [Azure Logic Apps](../logic-apps/logic-apps-overview.md)) erstellen und dann den Trigger in einer neuer Workflowautomatisierung einrichten, wie unter [Automatisieren von Antworten auf Defender für Cloud-Trigger](workflow-automation.md) erläutert.

:::image type="content" source="media/release-notes/regulatory-compliance-triggers-workflow-automation.png" alt-text="Verwenden von Änderungen bei Bewertungen der Einhaltung gesetzlicher Bestimmungen zum Auslösen der Workflowautomatisierung" lightbox="media/release-notes/regulatory-compliance-triggers-workflow-automation.png":::




## <a name="faq---regulatory-compliance-dashboard"></a>Häufig gestellte Fragen: Dashboard für die Einhaltung gesetzlicher Bestimmungen

- [Welche Standards werden auf dem Compliancedashboard unterstützt?](#what-standards-are-supported-in-the-compliance-dashboard)
- [Warum sind einige Kontrollen abgeblendet?](#why-do-some-controls-appear-grayed-out)
- [Wie kann ich einen integrierten Standard (beispielsweise PCI-DSS, ISO 27001 oder SOC2 TSP) aus dem Dashboard entfernen?](#how-can-i-remove-a-built-in-standard-like-pci-dss-iso-27001-or-soc2-tsp-from-the-dashboard)
- [Ich habe die in der Empfehlung vorgeschlagenen Änderungen vorgenommen, dies wird jedoch im Dashboard nicht widergespiegelt.](#i-made-the-suggested-changed-based-on-the-recommendation-yet-it-isnt-being-reflected-in-the-dashboard)
- [Welche Berechtigungen benötige ich für den Zugriff auf das Compliancedashboard?](#what-permissions-do-i-need-to-access-the-compliance-dashboard)
- [Das Dashboard für die Einhaltung gesetzlicher Bestimmungen lädt bei mir nicht.](#the-regulatory-compliance-dashboard-isnt-loading-for-me)
- [Wie kann ich einen nach Standard aufgeschlüsselten Bericht über bestandene und nicht bestandene Kontrollen auf meinem Dashboard anzeigen?](#how-can-i-view-a-report-of-passing-and-failing-controls-per-standard-in-my-dashboard)
- [Wie kann ich einen Bericht mit Konformitätsdaten in einem PDF-fremden Format herunterladen?](#how-can-i-download-a-report-with-compliance-data-in-a-format-other-than-pdf)
- [Wie kann ich Ausnahmen für einige der Richtlinien im Dashboard für die Einhaltung gesetzlicher Bestimmungen erstellen?](#how-can-i-create-exceptions-for-some-of-the-policies-in-the-regulatory-compliance-dashboard)
- [Welche Microsoft Defender-Pläne oder -Lizenzen benötige ich, um das Dashboard für die Einhaltung gesetzlicher Bestimmungen zu verwenden?](#what-microsoft-defender-plans-or-licenses-do-i-need-to-use-the-regulatory-compliance-dashboard)
- [Woher weiß ich, welche Benchmark oder welcher Standard verwendet werden soll?](#how-do-i-know-which-benchmark-or-standard-to-use)

### <a name="what-standards-are-supported-in-the-compliance-dashboard"></a>Welche Standards werden auf dem Compliancedashboard unterstützt?
Standardmäßig wird auf dem Dashboard für die Einhaltung gesetzlicher Bestimmungen der Azure-Sicherheitsvergleichstest angezeigt. Beim Azure-Sicherheitsvergleichstest handelt es sich um von Microsoft erstellte, Azure-spezifische Richtlinien zu bewährten Methoden für Sicherheit und Compliance, die auf allgemeinen Complianceframeworks basieren. Weitere Informationen finden Sie in der [Einführung zum Vergleichstest für die Azure-Sicherheit](../security/benchmarks/introduction.md)

Wenn Sie Ihre Konformität mit einem anderen Standard nachverfolgen möchten, müssen Sie ihn explizit Ihrem Dashboard hinzufügen.
 
Sie können weitere Standards hinzufügen, etwa: Azure CIS 1.3.0, NIST SP 800-53, NIST SP 800-171, SWIFT CSP CSCF-v2020, UK Official und UK NHS, HIPAA, Canada Federal PBMM, ISO 27001, SOC2-TSP und PCI-DSS 3.2.1.  

Dem Dashboard werden weitere Standards hinzugefügt und in die Informationen unter [Anpassen der Standards in Ihrem Dashboard für die Einhaltung gesetzlicher Bestimmungen](update-regulatory-compliance-packages.md) eingeschlossen.

### <a name="why-do-some-controls-appear-grayed-out"></a>Warum sind einige Kontrollen abgeblendet?
Für jeden Konformitätsstandard auf dem Dashboard gibt es eine Liste mit den zugehörigen Kontrollen. Sie können für eine Kontrolle jeweils die Details zu den bestandenen und nicht bestandenen Bewertungen anzeigen.

Einige Steuerelemente sind ausgegraut. Diese Steuerelemente sind keinen Defender für Cloud-Bewertungen zugeordnet. Einige sind möglicherweise prozedur- oder prozessbezogen und können daher nicht von Defender für Cloud überprüft werden. Für einige werden automatisierte Richtlinien oder Bewertungen erst noch implementiert. Und für einige Kontrollen ist ggf. die Plattform zuständig, wie unter [Gemeinsame Verantwortung in der Cloud](../security/fundamentals/shared-responsibility.md) erläutert. 

### <a name="how-can-i-remove-a-built-in-standard-like-pci-dss-iso-27001-or-soc2-tsp-from-the-dashboard"></a>Wie kann ich einen integrierten Standard (beispielsweise PCI-DSS, ISO 27001 oder SOC2 TSP) aus dem Dashboard entfernen? 
Wenn Sie das Dashboard für die Einhaltung gesetzlicher Bestimmungen anpassen und sich nur auf die für Sie relevanten Standards konzentrieren möchten, können Sie alle angezeigten gesetzlichen Standards entfernen, die für Ihre Organisation nicht relevant sind. Eine Anleitung zum Entfernen von Standards finden Sie unter [Entfernen eines Standards aus Ihrem Dashboard](update-regulatory-compliance-packages.md#remove-a-standard-from-your-dashboard).

### <a name="i-made-the-suggested-changed-based-on-the-recommendation-yet-it-isnt-being-reflected-in-the-dashboard"></a>Ich habe die in der Empfehlung vorgeschlagenen Änderungen vorgenommen, dies wird jedoch auf dem Dashboard nicht widergespiegelt.
Nach der Umsetzung von Empfehlungen kann es bis zu zwölf Stunden dauern, bis die Änderungen an Ihren Konformitätsdaten sichtbar werden. Bewertungen werden etwa alle 12 Stunden durchgeführt, und die Auswirkungen auf Ihre Konformitätsdaten sind jeweils erst nach Abschluss einer Bewertung sichtbar.
 
### <a name="what-permissions-do-i-need-to-access-the-compliance-dashboard"></a>Welche Berechtigungen benötige ich für den Zugriff auf das Compliancedashboard?
Zum Anzeigen von Konformitätsdaten benötigen Sie auch mindestens **Lesezugriff** auf die Konformitätsdaten der Richtlinie. „Sicherheitsleseberechtigter“ reicht nicht aus. Wenn Sie „Globaler Leser“ für das Abonnement sind, ist das ausreichend.

Für den Zugriff auf das Dashboard und die Verwaltung von Standards werden mindestens die Rollen **Mitwirkender bei Ressourcenrichtlinien** und **Sicherheitsadministrator** benötigt.


### <a name="the-regulatory-compliance-dashboard-isnt-loading-for-me"></a>Das Dashboard für die Einhaltung gesetzlicher Bestimmungen lädt bei mir nicht.
Um das Dashboard für die Einhaltung gesetzlicher Bestimmungen zu verwenden, muss Defender für Microsoft Defender für Cloud auf Abonnementebene aktiviert sein. Sollte das Dashboard nicht ordnungsgemäß geladen werden, versuchen Sie Folgendes:

1. Löschen Sie den Cache des Browsers.
1. Versuchen Sie es mit einem anderen Browser.
1. Öffnen Sie das Dashboard von einer anderen Netzwerkadresse aus.


### <a name="how-can-i-view-a-report-of-passing-and-failing-controls-per-standard-in-my-dashboard"></a>Wie kann ich einen nach Standard aufgeschlüsselten Bericht über bestandene und nicht bestandene Kontrollen auf meinem Dashboard anzeigen?
Auf dem Hauptdashboard können Sie einen Bericht über bestandene und nicht bestandene Kontrollen für (1) die Top 4 der niedrigsten Konformitätsstandards auf dem Dashboard erstellen. Wenn Sie den Status aller bestandenen/nicht bestandenen Kontrollen anzeigen möchten, wählen Sie (2) **Alle *x* anzeigen** aus, wobei „x“ die Anzahl der nachverfolgten Standards ist. Auf einer Kontextebene wird der Konformitätsstatus für jeden nachverfolgten Standard angezeigt.

:::image type="content" source="media/regulatory-compliance-dashboard/summaries-of-compliance-standards.png" alt-text="Zusammenfassungsabschnitt des Dashboards für die Einhaltung gesetzlicher Bestimmungen.":::


### <a name="how-can-i-download-a-report-with-compliance-data-in-a-format-other-than-pdf"></a>Wie kann ich einen Bericht mit Konformitätsdaten in einem PDF-fremden Format herunterladen?
Wenn Sie **Bericht herunterladen** auswählen, wählen Sie den Standard und das Format (PDF oder CSV) aus. Der resultierende Bericht umfasst die Abonnements, den Sie im Filter des Portals ausgewählt haben.

- Der PDF-Bericht enthält einen Zusammenfassungsstatus für den von Ihnen ausgewählten Standard.
- Der CSV-Bericht liefert detaillierte Ergebnisse pro Ressource, da er sich auf Richtlinien bezieht, die der jeweiligen Kontrolle zugeordnet sind.

Derzeit kann nur ein Bericht für die bereitgestellten gesetzlichen Standards heruntergeladen werden, nicht aber für eine benutzerdefinierte Richtlinie.


### <a name="how-can-i-create-exceptions-for-some-of-the-policies-in-the-regulatory-compliance-dashboard"></a>Wie kann ich Ausnahmen für einige der Richtlinien auf dem Dashboard für die Einhaltung gesetzlicher Bestimmungen erstellen?
Für Richtlinien, die in Defender für Cloud integriert und in der Sicherheitsbewertung enthalten sind, können Sie direkt im Portal Ausnahmen für einzelne oder mehrere Ressourcen erstellen, wie unter [Ausschließen von Ressourcen und Empfehlungen aus der Sicherheitsbewertung](exempt-resource.md) erläutert.

Für andere Richtlinien können Sie direkt in der Richtlinie eine Ausnahme erstellen. Eine entsprechende Anleitung finden Sie unter [Azure Policy-Ausnahmenstruktur](../governance/policy/concepts/exemption-structure.md).


### <a name="what-microsoft-defender-plans-or-licenses-do-i-need-to-use-the-regulatory-compliance-dashboard"></a>Welche Microsoft Defender-Pläne oder -Lizenzen benötige ich, um das Dashboard für die Einhaltung gesetzlicher Bestimmungen zu verwenden?
Wenn Sie *irgendeinen* der Microsoft Defender-Pläne für *irgendwelche* Ihrer Azure-Ressourcen aktiviert haben, können Sie auf das Dashboard für die Einhaltung gesetzlicher Bestimmungen von Defender für Cloud und alle zugehörigen Daten zugreifen.


### <a name="how-do-i-know-which-benchmark-or-standard-to-use"></a>Woher weiß ich, welche Benchmark oder welcher Standard verwendet werden soll?
[Azure Security Benchmark](/security/benchmark/azure/introduction) (ASB) ist der kanonische Satz von Sicherheitsempfehlungen und bewährten Methoden, die von Microsoft definiert wurden und auf allgemeine Compliancekontrollframeworks ausgerichtet sind, einschließlich [CIS Microsoft Azure Foundations Benchmark](https://www.cisecurity.org/benchmark/azure/) und [NIST SP 800-53](https://csrc.nist.gov/publications/detail/sp/800-53/rev-5/final). ASB ist eine umfassende Benchmark und wurde entwickelt, um die neuesten Sicherheitsfunktionen einer Vielzahl von Azure-Diensten zu empfehlen. Wir empfehlen ASB Kunden, deren Sicherheitsstatus maximiert und deren Konformitätsstatus an Branchenstandards ausgerichtet werden soll.

Die [CIS-Benchmark](https://www.cisecurity.org/benchmark/azure/) wird von einer unabhängigen Entität – Center for Internet Security (CIS) – erstellt und enthält Empfehlungen für eine Teilmenge der wichtigsten Azure-Dienste. Wir arbeiten mit CIS zusammen, um sicherzustellen, dass die Empfehlungen die neuesten Optimierungen in Azure beinhalten, hin und wieder sind sie jedoch trotzdem veraltet. Dennoch nutzen einige Kunden diese objektive Drittanbieterbewertung von CIS gerne als erste und primäre Sicherheitsbaseline.

Seit der Veröffentlichung von Azure Security Benchmark haben sich viele Kunden dazu entschieden, sie als Ersatz für die CIS-Benchmarks einzusetzen.


## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie gelernt, wie das Dashboard für die Einhaltung gesetzlicher Bestimmungen von Defender für Cloud für Folgendes verwendet werden kann:

> [!div class="checklist"]
> * Anzeigen und Überwachen Ihres Konformitätsstatus in Bezug auf die für Sie wichtigen Standards und Bestimmungen
> * Verbessern Ihres Konformitätsstatus durch das Lösen relevanter Empfehlungen und Verfolgen der verbesserten Compliancebewertung

Mit dem Dashboard zur Einhaltung gesetzlicher Bestimmungen kann der Konformitätsprozess stark vereinfacht werden. Darüber hinaus kann eine erhebliche Verkürzung des Zeitraums erzielt werden, der für das Sammeln von Konformitätsnachweisen für Ihre Azure-, Hybrid- und Multicloudumgebung erforderlich ist.

Weitere Informationen finden Sie auf den folgenden verwandten Seiten:

- [Anpassen der Standards in Ihrem Dashboard für die Einhaltung gesetzlicher Bestimmungen](update-regulatory-compliance-packages.md): Es wird beschrieben, wie Sie auswählen, welche Standards in Ihrem Dashboard zur Einhaltung gesetzlicher Bestimmungen angezeigt werden. 
- [Verwalten von Sicherheitsempfehlungen in Defender für Cloud](review-security-recommendations.md): Hier erfahren Sie, wie Sie Empfehlungen in Defender für Cloud verwenden können, um Ihre Azure-Ressourcen zu schützen.

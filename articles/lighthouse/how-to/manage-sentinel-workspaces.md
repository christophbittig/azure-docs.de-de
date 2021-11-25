---
title: Verwalten von Microsoft Sentinel-Arbeitsbereichen im großen Stil
description: Azure Lighthouse unterstützt Sie bei der effektiven Verwaltung von Microsoft Sentinel in Bezug auf delegierte Kundenressourcen.
ms.date: 11/05/2021
ms.topic: how-to
ms.openlocfilehash: 042a7d376a82ed70782db252e3ef17274b7cf7c7
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132289924"
---
# <a name="manage-microsoft-sentinel-workspaces-at-scale"></a>Verwalten von Microsoft Sentinel-Arbeitsbereichen im großen Stil

Als Dienstanbieter haben Sie möglicherweise mehrere Kundenmandanten in [Azure Lighthouse](../overview.md) integriert. Azure Lighthouse ermöglicht Dienstanbietern das gleichzeitige Ausführen von Vorgängen für mehrere Azure Active Directory-Mandanten (Azure AD) im großen Maßstab, wodurch Verwaltungsaufgaben effizienter werden.

Microsoft Sentinel bietet Sicherheits- und Bedrohungsanalysen und stellt eine zentrale Lösung für die Warnungs- und Bedrohungserkennung, die proaktive Suche sowie die Reaktion auf Bedrohungen bereit. Mit Azure Lighthouse können Sie mehrere Microsoft Sentinel-Arbeitsbereiche mandantenübergreifend im großen Maßstab verwalten. Dies ermöglicht Szenarien wie das Ausführen von Abfragen über mehrere Arbeitsbereiche hinweg oder das Erstellen von Arbeitsmappen, um Daten aus Ihren verbundenen Datenquellen zu visualisieren und zu überwachen und so Erkenntnisse zu gewinnen. IP-Informationen, wie etwa Abfragen und Playbooks, verbleiben in Ihrem verwaltenden Mandanten, können aber verwendet werden, um Sicherheitsverwaltung in den Mandanten des Kunden durchzuführen.

Dieses Thema bietet eine skalierbare Übersicht zur Verwendung von [Microsoft Sentinel](../../sentinel/overview.md), um mandantenübergreifende Transparenz und verwaltete Sicherheitsdienste zu gewährleisten.

> [!TIP]
> Zwar beziehen wir uns in diesem Thema auf Dienstanbieter und Kunden, doch gelten diese Anweisungen auch für [Unternehmen, die Azure Lighthouse zum Verwalten mehrerer Mandanten verwenden](../concepts/enterprise.md).

> [!NOTE]
> Sie können delegierte Ressourcen verwalten, die sich in unterschiedlichen [Regionen](../../availability-zones/az-overview.md#regions) befinden. Die Delegierung von Abonnements auf eine [nationale Cloud](../../active-directory/develop/authentication-national-cloud.md) und die öffentliche Azure-Cloud oder zwei separate nationale Clouds wird nicht unterstützt.

## <a name="architectural-considerations"></a>Architekturaspekte

Für einen Anbieter von verwalteten Sicherheitsdiensten (Managed Security Service Provider, MSSP), der mithilfe von Microsoft Sentinel ein Security-as-a-Service-Angebot entwickeln möchte, ist möglicherweise die Konzentration auf ein einzelnes Security Operations Center (SOC) erforderlich, um mehrere Microsoft Sentinel-Arbeitsbereiche, die bei einzelnen Mandanten von Kunden bereitgestellt sind, zentral zu überwachen, verwalten und konfigurieren. Analog dazu kann es auch für Unternehmen mit mehreren Azure AD-Mandanten sinnvoll sein, mehrere Microsoft Sentinel-Arbeitsbereiche, die über ihre Mandanten verteilt bereitgestellt werden, zentral zu verwalten.

Das Bereitstellungsmodell bietet folgende Vorzüge:

- Der Besitz der Daten verbleibt bei den einzelnen verwalteten Mandanten.
- Anforderung zur Speicherung von Daten innerhalb geografischer Grenzen wird unterstützt.
- Die Isolation der Daten ist sichergestellt, da Daten für mehrere Kunden nicht im gleichen Arbeitsbereich gespeichert werden.
- Datenexfiltration aus den verwalteten Mandanten wird verhindert, was die Sicherstellung von Datencompliance erleichtert.
- Mit den verbundenen Kosten werden die einzelnen verwalteten Mandanten belastet, nicht der verwaltende Mandant.
- Daten aus allen Datenquellen und Datenconnectors, die in Microsoft Sentinel integriert sind (wie etwa Azure AD-Aktivitätsprotokolle, Office-365-Protokolle oder Benachrichtigungen von Microsoft Threat Protection), verbleiben innerhalb der einzelnen Kundenmandanten.
- Die Netzwerklatenz wird verringert.
- Neue Niederlassungen oder Kunden lassen sich einfach hinzufügen oder entfernen.
- Möglichkeit zur Verwendung einer Ansicht mit mehreren Arbeitsbereichen beim Arbeiten über Azure Lighthouse.
- Zum Schutz Ihres geistigen Eigentums können Sie Playbooks und Arbeitsmappen verwenden, um mandantenübergreifend zu arbeiten, ohne Code direkt mit Kunden zu teilen. Nur Analyse- und Huntingregeln müssen direkt im Mandanten jedes Kunden gespeichert werden.

> [!IMPORTANT]
> Wenn alle Arbeitsbereiche im Mandanten des Kunden erstellt werden, müssen die Microsoft.SecurityInsights- und Microsoft.OperationalInsights-Ressourcenanbieter auch in einem Abonnement im verwaltenden Mandanten [registriert](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider) werden.

Ein alternatives Bereitstellungsmodell besteht in der Erstellung eines Microsoft Sentinel-Arbeitsbereichs im verwaltenden Mandanten. In diesem Modell ermöglicht Azure Lighthouse die übergreifende Protokollsammlung aus Datenquellen über verwaltete Mandanten hinweg. Es gibt jedoch einige Datenquellen, die nicht mandantenübergreifend verbunden werden können, z. B. Microsoft Defender für Cloud. Aufgrund dieser Einschränkung ist dieses Modell für viele Dienstanbieterszenarien nicht geeignet.

## <a name="granular-azure-role-based-access-control-azure-rbac"></a>Granulare rollenbasierte Zugriffssteuerung von Azure (Azure RBAC)

Für jedes von MSSP verwaltete Kundenabonnement muss ein [Onboarding in Azure Lighthouse](onboard-customer.md) durchgeführt werden. Dies ermöglicht es bestimmten Benutzern im Verwaltungsmandanten, auf Verwaltungsvorgänge in Microsoft Sentinel-Arbeitsbereichen zuzugreifen, die in Kundenmandanten bereitgestellt wurden.

Beim Erstellen Ihrer Autorisierungen können Sie die in Microsoft Sentinel integrierten Rollen an Benutzer, Gruppen oder Dienstprinzipale in Ihrem Verwaltungsmandanten zuweisen:

- [Microsoft Sentinel Reader](../../role-based-access-control/built-in-roles.md#microsoft-sentinel-reader) (Leser)
- [Microsoft Sentinel Responder](../../role-based-access-control/built-in-roles.md#microsoft-sentinel-responder) (Antwortdienst)
- [Microsoft Sentinel-Mitwirkender](../../role-based-access-control/built-in-roles.md#microsoft-sentinel-contributor)

Es kann außerdem sinnvoll sein, zusätzliche integrierte Rollen zuzuweisen, um zusätzliche Funktionen auszuführen. Informationen zu spezifischen Rollen, die zusammen mit Microsoft Sentinel verwendet werden können, finden Sie unter [Berechtigungen in Microsoft Sentinel](../../sentinel/roles.md).

Nach dem Onboarding Ihrer Kunden können sich bestimmte Benutzer bei Ihrem Verwaltungsmandanten anmelden und mithilfe der zugewiesenen Rollen [direkt auf den Microsoft Sentinel-Arbeitsbereich des Kunden zugreifen](../../sentinel/multiple-tenants-service-providers.md).

## <a name="view-and-manage-incidents-across-workspaces"></a>Anzeigen und Verwalten von Vorfällen über die Grenzen von Arbeitsbereichen hinweg

Wenn Sie Microsoft Sentinel-Ressourcen für mehrere Kunden verwalten, können Sie Vorfälle in mehreren Arbeitsbereichen, die sich über mehrere Mandanten erstrecken, zugleich anzeigen lassen und verwalten. Weitere Informationen finden Sie unter [Arbeiten mit Vorfällen in vielen Arbeitsbereichen gleichzeitig](../../sentinel/multiple-workspace-view.md) und [Erweitern von Microsoft Sentinel auf Arbeitsbereiche und Mandanten](../../sentinel/extend-sentinel-across-workspaces-tenants.md).

> [!NOTE]
> Achten Sie darauf, dass den Benutzern in Ihrem Verwaltungsmandanten Lese- und Schreibberechtigungen für alle verwalteten Arbeitsbereiche zugewiesen wurden. Wenn ein Benutzer für einige Arbeitsbereiche nur Leseberechtigungen besitzt, werden möglicherweise Warnmeldungen angezeigt, wenn er Vorfälle in diesen Arbeitsbereichen auswählt, und der Benutzer ist dann nicht in der Lage, diese Vorfälle oder andere, die zusammen mit ihnen ausgewählt wurden, zu bearbeiten (auch wenn der Benutzer für die anderen Vorfälle über Berechtigungen verfügt).

## <a name="configure-playbooks-for-mitigation"></a>Konfigurieren von Playbooks zur Entschärfung

[Playbooks](../../sentinel/tutorial-respond-threats-playbook.md) können zur automatischen Entschärfung verwendet werden, wenn ein Alarm ausgelöst wird. Diese Playbooks können manuell ausgeführt werden, oder aber automatisch, wenn bestimmte Warnungen ausgelöst werden. Die Playbooks können wahlweise im Verwaltungsmandanten oder im Kundenmandanten bereitgestellt werden, wobei sich die Konfiguration der Antwortprozeduren danach richtet, Benutzer welches Mandanten in Reaktion auf eine Sicherheitsbedrohung aktiv werden müssen.

## <a name="create-cross-tenant-workbooks"></a>Erstellen von mandantenübergreifenden Arbeitsmappen

Mithilfe von [Azure Monitor-Workbooks (Arbeitsmappen) in Microsoft Sentinel](../../sentinel/overview.md#workbooks) können Sie Daten aus Ihren verbundenen Datenquellen anzeigen und überwachen, um Erkenntnisse zu gewinnen. Sie können die integrierten Arbeitsmappenvorlagen in Microsoft Sentinel verwenden oder eigene, benutzerdefinierte Arbeitsmappen für Ihre Umstände erstellen.

Sie können Arbeitsmappen in Ihrem Verwaltungsmandanten bereitstellen und in großem Maßstab Dashboards erstellen, um Daten übergreifend über Kundenmandanten zu überwachen und abzufragen. Weitere Informationen finden Sie unter [Arbeitsbereichsübergreifende Überwachung](../../sentinel/extend-sentinel-across-workspaces-tenants.md#using-cross-workspace-workbooks).

Darüber hinaus können Sie Arbeitsmappen direkt in einem einzelnen von Ihnen verwalteten Mandanten bereitstellen, um für diesen Kunden spezifische Szenarien zu behandeln.

## <a name="run-log-analytics-and-hunting-queries-across-microsoft-sentinel-workspaces"></a>Ausführen von Log-Analytics- und Hunting-Abfragen für Microsoft Sentinel-Arbeitsbereiche

Erstellen und speichern Sie Log Analytics-Abfragen zur Bedrohungserkennung einschließlich [Hunting-Abfragen](../../sentinel/extend-sentinel-across-workspaces-tenants.md#cross-workspace-hunting) zentral im Verwaltungsmandanten. Diese Abfragen können dann mit dem Vereinigungsoperator (Union Operator) und dem Arbeitsbereichsausdruck () übergreifend in allen Microsoft Sentinel-Arbeitsbereichen Ihrer Kunden ausgeführt werden. Weitere Informationen finden Sie unter [Arbeitsbereichsübergreifende Abfragen](../../sentinel/extend-sentinel-across-workspaces-tenants.md#cross-workspace-querying).

## <a name="use-automation-for-cross-workspace-management"></a>Verwenden von Automation für die arbeitsbereichsübergreifende Verwaltung

Sie können die Automation verwenden, um mehrere Microsoft Sentinel-Arbeitsbereiche zu verwalten und [Hunting-Abfragen](../../sentinel/hunting.md), Playbooks und Arbeitsmappen zu konfigurieren. Weitere Informationen finden Sie unter [Arbeitsbereichsübergreifende Verwaltung mithilfe von Automatisierung](../../sentinel/extend-sentinel-across-workspaces-tenants.md#cross-workspace-management-using-automation).

## <a name="monitor-security-of-office-365-environments"></a>Überwachen der Sicherheit von Office 365-Umgebungen

Verwenden Sie Azure Lighthouse in Verbindung mit Microsoft Sentinel, um die Sicherheit von Office-365-Umgebungen mandantenübergreifend zu überwachen. Zunächst müssen [im verwalteten Mandanten vorkonfigurierte Office-365-Datenconnectors aktiviert werden](../../sentinel/data-connectors-reference.md#microsoft-office-365), damit Informationen zu Benutzer- und Verwaltungsaktivitäten in Exchange und SharePoint (einschließlich OneDrive) in einen Microsoft Sentinel-Arbeitsbereich innerhalb des verwalteten Mandanten erfasst werden können. Dies umfasst Details zu Aktionen wie z. B. Dateidownloads, gesendeten Zugriffsanforderungen, Änderungen an Gruppenereignissen und Postfachvorgängen sowie Informationen zu dem Benutzer, der die Aktionen ausgeführt hat. [Office 365-DLP-Warnungen](https://techcommunity.microsoft.com/t5/azure-sentinel/ingest-office-365-dlp-events-into-azure-sentinel/ba-p/1031820) werden auch als Teil des integrierten Office 365-Connectors unterstützt.

[Microsoft Defender-für-Cloud-Apps-Connector](../../sentinel/data-connectors-reference.md#microsoft-cloud-app-security-mcas) zum Streamen von Warnungen und Cloud-Discovery-Protokollen an Microsoft Sentinel. So erhalten Sie Einblicke in Cloud-Apps, erweiterte Analysen zur Erkennung und Abwehr von Cyberbedrohungen und Kontrolle darüber, wie Daten übertragen werden. Aktivitätsprotokolle für Defender-für-Cloud-Apps können [mithilfe von CEF (Common Event Format) genutzt werden](https://techcommunity.microsoft.com/t5/azure-sentinel/ingest-box-com-activity-events-via-microsoft-cloud-app-security/ba-p/1072849).

Nach dem Einrichten von Office-365-Datenconnectors können Sie mandantenübergreifende Microsoft Sentinel-Funktionen verwenden, z. B. das Anzeigen und Analysieren von Daten in Arbeitsmappen, Verwenden von Abfragen zum Erstellen von benutzerdefinierten Warnungen und das Konfigurieren von Playbooks als Reaktion auf Bedrohungen.

## <a name="protect-intellectual-property"></a>Schutz des geistigen Eigentums

Bei der Arbeit mit Kunden kommt es möglicherweise auf den Schutz des geistigen Eigentums an, das Sie in Microsoft Sentinel entwickelt haben, z. B. Microsoft Sentinel-Analyseregeln, Suchabfragen, Playbooks und Arbeitsmappen. Es gibt verschiedene Methoden, mit denen Sie sicherstellen können, dass Kunden keinen vollständigen Zugriff auf den in diesen Ressourcen verwendeten Code haben.

Weitere Informationen finden Sie unter [Schutz des geistigen Eigentums von MSSP in Microsoft Sentinel](../../sentinel/mssp-protect-intellectual-property.md).

## <a name="next-steps"></a>Nächste Schritte

- Informationen zu [Microsoft Sentinel](../../sentinel/overview.md)
- Weitere Informationen finden Sie [auf der Microsoft Sentinel-Preisseite](https://azure.microsoft.com/pricing/details/azure-sentinel/).
- Erkunden Sie [`Sentinel All-in-One`](https://github.com/Azure/Azure-Sentinel/tree/master/Tools/Sentinel-All-In-One), ein Projekt zum Beschleunigen der Bereitstellungs- und Erstkonfigurationsaufgaben einer Microsoft Sentinel-Umgebung.
- Erfahren Sie über [Mandantenübergreifende Verwaltungsmöglichkeiten](../concepts/cross-tenant-management-experience.md).

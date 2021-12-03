---
title: Verschieben Sie Ihre Funktionsanwendung zwischen Regionen in Azure Functions
description: Erfahren Sie, wie Sie Azure Functions-Ressourcen von einer Region in eine andere verschieben können, indem Sie eine Kopie Ihrer vorhandenen Azure Function-Ressourcen in der Zielregion erstellen.
ms.topic: how-to
ms.service: azure-functions
author: nzthiago
ms.date: 05/11/2021
ms.custom: subject-moving-resources
ms.openlocfilehash: c106ec2e5e4592937974f040e1ae14aabc45f354
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132138839"
---
# <a name="move-your-function-app-between-regions-in-azure-functions"></a>Verschieben Sie Ihre Funktionsanwendung zwischen Regionen in Azure Functions

Dieser Artikel beschreibt, wie Sie Azure Functions-Ressourcen in eine andere Azure-Region verschieben können. Sie können Ihre Ressourcen aus einem der folgenden Gründe in eine andere Region verschieben:
 + Nutzen Sie die Vorteile einer neuen Azure-Region
 + Bereitstellung von Funktionen oder Diensten, die nur in bestimmten Regionen verfügbar sind
 + Erfüllen interner Richtlinien- und Governanceanforderungen
 + Reagieren Sie auf Kapazitätsplanungsanforderungen

Azure Functions-Ressourcen sind regionsspezifisch und können nicht zwischen Regionen verschoben werden. Sie müssen eine Kopie der Ressourcen Ihrer bestehenden Funktionsanwendung in der Zielregion erstellen und dann den Code Ihrer Funktionen in der neuen Anwendung neu verteilen.

Wenn minimale Ausfallzeiten erforderlich sind, sollten Sie Ihre Funktionsanwendung in beiden Regionen ausführen, um eine Notfallwiederherstellung Architektur zu implementieren:
+ [Georedundante Notfallwiederherstellung in Azure Functions](functions-geo-disaster-recovery.md)
+ [Notfallwiederherstellung und geografische Verteilung in Azure Durable Functions](durable/durable-functions-disaster-recovery-geo-distribution.md)

## <a name="prerequisites"></a>Voraussetzungen

+ Stellen Sie sicher, dass die Zielregion Azure Functions und alle zugehörigen Dienste unterstützt, deren Ressourcen Sie verschieben möchten
+ Zugriff auf den ursprünglichen Quellcode der zu migrierenden Funktionen haben

## <a name="prepare"></a>Vorbereiten

Identifizieren Sie alle Ressourcen der Funktionsanwendung, die in der Quellregion verwendet werden, einschließlich der folgenden:

+ Funktionen-App
+ [Hostingplan](functions-scale.md#overview-of-plans)
+ [Bereitstellungsslots](functions-deployment-slots.md)
+ [In Azure erworbene benutzerdefinierte Domänen](../app-service/manage-custom-dns-buy-domain.md)
+ [TLS/SSL-Zertifikate und Einstellungen](../app-service/configure-ssl-certificate.md)
+ [Konfigurierte Netzwerkoptionen](functions-networking-options.md)
+ [Verwaltete Identitäten](../app-service/overview-managed-identity.md)
+ [Konfigurierte Anwendungseinstellungen](functions-how-to-use-azure-function-app-settings.md) - Benutzer mit ausreichendem Zugriff können alle Einstellungen der Quellanwendung mit Hilfe der Funktion "Erweiterte Bearbeitung" im Portal kopieren
+ [Skalierende Konfigurationen](functions-scale.md#scale)

Ihre Funktionen können sich mit anderen Ressourcen verbinden, indem Sie Auslöser oder Bindungen verwenden. Informationen dazu, wie Sie diese Ressourcen innerhalb von Regionen verschieben, finden Sie in der Dokumentation zu den jeweiligen Diensten.

Sie sollten auch in der Lage sein, [eine Vorlage aus vorhandenen Ressourcen](../azure-resource-manager/templates/export-template-portal.md) zu exportieren.

## <a name="move"></a>Move

Stellen Sie die Funktionsanwendung in der Zielregion bereit und überprüfen Sie die konfigurierten Ressourcen. 

### <a name="redeploy-function-app"></a>Funktionsanwendung erneut bereitstellen

Wenn Sie Zugriff auf die Bereitstellungs- und Automatisierungsressourcen haben, mit denen die Funktionsanwendung in der Quellregion erstellt wurde, führen Sie die gleichen Bereitstellungsschritte in der Zielregion erneut aus, um Ihre Anwendung zu erstellen und erneut bereitzustellen. 

Wenn Sie nur Zugriff auf den Quellcode, aber nicht auf die Bereitstellungs- und Automatisierungsressourcen haben, können Sie die Funktionsanwendung mit einer der verfügbaren [Bereitstellungstechnologien](functions-deployment-technologies.md) oder mit einer der [kontinuierlichen Bereitstellungsmethoden](functions-continuous-deployment.md) auf der Zielregion bereitstellen und konfigurieren.

### <a name="review-configured-resources"></a>Überprüfung der konfigurierten Ressourcen

Überprüfen und konfigurieren Sie die im obigen Schritt [Vorbereiten](#prepare) identifizierten Ressourcen in der Zielregion, wenn sie nicht während der Bereitstellung konfiguriert wurden.

### <a name="move-considerations"></a>Überlegungen zum Umzug
+ Wenn Ihre Bereitstellungsressourcen und die Automatisierung keine Funktionsanwendung erstellen, [ erstellen Sie eine Anwendung desselben Typs in einem neuen Hosting-Plan](functions-scale.md#overview-of-plans) in der Zielregion
+ Funktionsanwendungsnamen sind in Azure global eindeutig, so dass die Anwendung in der Zielregion nicht denselben Namen haben kann wie die in der Quellregion
+ Referenzen und Anwendungseinstellungen, die Ihre Funktionsanwendung mit Abhängigkeiten verbinden, müssen überprüft und bei Bedarf aktualisiert werden. Wenn Sie zum Beispiel eine Datenbank verschieben, die von Ihren Funktionen aufgerufen wird, müssen Sie auch die Anwendungseinstellungen oder die Konfiguration aktualisieren, um eine Verbindung zur Datenbank in der Zielregion herzustellen. Einige Anwendungseinstellungen wie der Application Insights-Instrumentierungsschlüssel oder das von der Funktionsanwendung verwendete Azure-Speicherkonto können bereits auf der Zielregion konfiguriert sein und müssen nicht aktualisiert werden
+ Denken Sie daran, Ihre Konfiguration zu überprüfen und Ihre Funktionen in der Zielregion zu testen
+ Wenn Sie eine benutzerdefinierte Domäne konfiguriert haben, [zeichnen Sie den Domänennamen um](../app-service/manage-custom-dns-migrate-domain.md#remap-the-active-dns-name)
+ Für Funktionen, die auf Dedicated-Tarifen laufen, prüfen Sie auch den [App Service Migrationsplan](../app-service/manage-move-across-regions.md), falls der Plan mit Webanwendungen gemeinsam genutzt wird.

## <a name="clean-up-source-resources"></a>Bereinigen der Quellressourcen

Löschen Sie nach Abschluss der Verschiebung die Funktionsanwendung und den Hosting-Plan aus der Quellregion. Bei Premium- oder Dedicated-Tarifen zahlen Sie für Funktionsanwendungen, auch wenn die Anwendung selbst nicht ausgeführt wird.

## <a name="next-steps"></a>Nächste Schritte

+ Im [Azure Architecture Center](/azure/architecture/browse/?expanded=azure&products=azure-functions) finden Sie Beispiele für Azure Funktions, die in mehreren Regionen als Teil fortgeschrittener Lösungsarchitekturen ausgeführt werden.

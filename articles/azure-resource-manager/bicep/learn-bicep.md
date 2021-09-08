---
title: Entdecken Sie Bicep bei Microsoft Learn
description: Hier finden Sie eine Übersicht über die Lerneinheiten zu Bicep, die bei Microsoft Learn verfügbar sind.
ms.topic: conceptual
ms.date: 08/08/2021
ms.openlocfilehash: a0459e44c4abd69810bcc70974ea2e160adf5a5e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122339690"
---
# <a name="bicep-on-microsoft-learn"></a>Bicep bei Microsoft Learn

Eine Schritt-für-Schritt-Anleitung für die Verwendung von Bicep zum Bereitstellen Ihrer Infrastruktur in Azure finden Sie in verschiedenen Microsoft Learn-Modulen.

## <a name="introductory-path"></a>Lernpfade für den Einstieg

Am besten beginnen Sie mit dem Lernpfad [Bereitstellen und Verwalten von Ressourcen in Azure mithilfe von Bicep](/learn/paths/bicep-deploy/). Sie erhalten dadurch eine Einführung in das Konzept von Infrastructure-as-Code. In diesem Lernpfad werden Sie durch die verschiedenen Schritte geführt, die für die Erstellung immer komplexerer Bicep-Dateien notwendig sind.

Der Lernpfad enthält die folgenden Module:

| Learn-Modul | BESCHREIBUNG |
| ------------ | ----------- |
| [Einführung in Infrastructure-as-Code mit Bicep](/learn/modules/introduction-to-infrastructure-as-code-using-bicep/) | Dieses Modul beschreibt die Vorteile der Verwendung von Infrastructure-as-Code, Azure Resource Manager und Bicep, um Ihre Cloudbereitstellungen schnell und sicher zu skalieren. Es hilft Ihnen dabei, die Arten von Bereitstellungen zu bestimmen, für die Bicep ein geeignetes Bereitstellungstool ist. |
| [Erstellen Sie Ihre erste Bicep-Vorlage](/learn/modules/build-first-bicep-template/) | In diesem Modul definieren Sie die Azure-Ressourcen innerhalb einer Bicep-Vorlage. Sie verbessern die Konsistenz und Zuverlässigkeit Ihrer Bereitstellungen, reduzieren Sie den erforderlichen manuellen Aufwand, und skalieren Sie Ihre Bereitstellungen für verschiedene Umgebungen. Durch die Verwendung von Parametern, Variablen, Ausdrücken und Modulen ist Ihre Vorlage flexibel und wiederverwendbar. |
| [Erstellen von wiederverwendbaren Bicep-Vorlagen mithilfe von Parametern](/learn/modules/build-reusable-bicep-templates-parameters/) | In diesem Modul wird beschrieben, wie Sie Bicep-Parameter verwenden können, um während jeder Bereitstellung Informationen für Ihre Vorlage bereitzustellen. Sie werden mehr über Parameter-Decorators erfahren, mit denen Sie Ihre verschiedenen Parameter leichter verstehen und einsetzen können. Außerdem erhalten Sie Informationen zu den verschiedenen Möglichkeiten, wie Sie Parameterwerte bereitstellen und schützen können, wenn Sie mit sicheren Informationen arbeiten. |
| [Erstellen von flexiblen Bicep-Vorlagen mithilfe von Bedingungen und Schleifen](/learn/modules/build-flexible-bicep-templates-conditions-loops/) | Sie werden lernen, wie Sie Bedingungen nutzen können, um Ressourcen nur dann bereitzustellen, wenn bestimmte Einschränkungen gelten. Außerdem werden Sie lernen, wie Sie Schleifen nutzen können, um mehrere Ressourcen mit ähnlichen Eigenschaften bereitzustellen. |
| [Bereitstellen von untergeordneten und Erweiterungsressourcen mithilfe von Bicep](/learn/modules/child-extension-bicep-templates/) | In diesem Modul wird gezeigt, wie Sie verschiedene Azure-Ressourcen in Ihrem Bicep-Code bereitstellen. Erfahren Sie mehr über untergeordnete und Erweiterungsressourcen und wie sie in Bicep definiert und verwendet werden können. Verwenden Sie Bicep, um mit Ressourcen zu arbeiten, die Sie außerhalb einer Bicep-Vorlage oder eines Bicep-Moduls erstellt haben. |
| [Bereitstellen von Ressourcen für Abonnements, Verwaltungsgruppen und Mandanten mithilfe von Bicep](/learn/modules/deploy-resources-scopes-bicep/) | Stellen Sie Azure-Ressourcen im Abonnement-, Verwaltungsgruppen- und Mandantenbereich zur Verfügung. Sie erhalten mehr Informationen zu diesen Ressourcen und werden lernen, warum Sie sie einsetzen sollten und wie Sie Bicep-Code für die Bereitstellung der Ressourcen erstellen können. Außerdem werden Sie lernen, wie Sie einen einzelnen Satz von Bicep-Dateien erstellen, die Sie in nur einem Vorgang für mehrere Bereiche bereitstellen können. |
| [Erweitern von Vorlagen mithilfe von Bereitstellungsskripts](/learn/modules/extend-resource-manager-template-deployment-scripts/) | Sie werden lernen, wie Sie Ihrer Bicep-Datei oder Ihrer Azure Resource Manager-Vorlage (ARM-Vorlage) mithilfe von Bereitstellungsskripts benutzerdefinierte Schritte hinzufügen. |

## <a name="other-modules"></a>Sonstige Module

Zusätzlich zum vorherigen Pfad enthalten die folgenden Module Bicep-Inhalte.

| Learn-Modul | BESCHREIBUNG |
| ------------ | ----------- |
| [Verwalten von Änderungen an Ihrem Bicep-Code mithilfe von Git](/learn/modules/manage-changes-bicep-code-git/) | Erfahren Sie, wie Sie Git verwenden, um Ihren Bicep-Entwicklungsworkflow zu unterstützen, indem Sie die Änderungen nachverfolgen, die Sie während der Arbeit vornehmen. Sie erfahren, wie Sie Dateien committen, den Verlauf der geänderten Dateien anzeigen und Branches verwenden, um mehrere Versionen Ihres Codes gleichzeitig zu entwickeln. Außerdem erfahren Sie, wie Sie GitHub oder Azure Repos verwenden, um ein Repository zu veröffentlichen, damit Sie mit Teammitgliedern zusammenarbeiten können. |
| [Veröffentlichen von Bibliotheken mit wiederverwendbarem Infrastrukturcode mithilfe von Vorlagenspezifikationen](/learn/modules/arm-template-specs/) | Mit Vorlagenspezifikationen können Sie Ihre ARM-Vorlagen in Ihrer Organisation wiederverwenden und freigeben. Erfahren Sie, wie Sie Vorlagenspezifikationen erstellen und veröffentlichen und wie Sie sie bereitstellen. Außerdem erfahren Sie, wie Sie Vorlagenspezifikationen verwalten, einschließlich der Zugriffssteuerung und der sicheren Aktualisierung mithilfe von Versionen. |
| [Vorschau von Azure-Bereitstellungsänderungen mithilfe von Was-wäre-wenn-Vorgängen](/learn/modules/arm-template-whatif/) | In diesem Modul erfahren Sie, wie Sie eine Vorschau Ihrer Änderungen mit dem Was-wäre-wenn-Vorgang anzeigen können. Mit Was-wäre-wenn können Sie sicherstellen, dass Ihre Bicep-Datei nur die von Ihnen erwarteten Änderungen vornimmt. |
| [Strukturieren Ihres Bicep-Codes für die Zusammenarbeit](/learn/modules/structure-bicep-code-collaboration/) | Erstellen Sie Bicep-Dateien, die die gemeinsame Entwicklung unterstützen und bewährte Methoden befolgen. Planen Sie Ihre Parameter, um die Bereitstellung Ihrer Vorlagen zu vereinfachen. Verwenden Sie einen konsistenten Stil, eine klare Struktur und Kommentare, um das Verständnis, die Verwendung und das Ändern Ihres Bicep-Codes zu vereinfachen. |
| [Authentifizieren Ihrer Azure-Bereitstellungspipeline mit Dienstprinzipalen](/learn/modules/authenticate-azure-deployment-pipeline-service-principals/) | Dienstprinzipale ermöglichen Ihren Bereitstellungspipelines die sichere Authentifizierung bei Azure. In diesem Modul wird beschrieben, was Dienstprinzipale sind, wie sie funktionieren und wie sie erstellt werden. Darüber hinaus erfahren Sie, wie Sie dafür die Berechtigung für Ihre Azure-Ressourcen gewähren, damit Ihre Bicep-Dateien von Ihren Pipelines bereitgestellt werden können. |
| [Erstellen Ihrer ersten Bicep-Bereitstellungspipeline mithilfe von Azure Pipelines](/learn/modules/build-first-bicep-deployment-pipeline-using-azure-pipelines/) | Erstellen Sie eine einfache Bereitstellungspipeline für Bicep-Code. Verwenden Sie eine Dienstverbindung, um Ihre Pipeline sicher in Azure zu identifizieren. Konfigurieren Sie, wann die Pipeline ausgeführt wird, mithilfe von Triggern. |

## <a name="next-steps"></a>Nächste Schritte

* Eine kurze Einführung in Bicep finden Sie im [Bicep-Schnellstart](quickstart-create-bicep-use-visual-studio-code.md).
* Vorschläge zur Verbesserung Ihrer Bicep-Dateien finden Sie unter [Best Practices für Bicep](best-practices.md).

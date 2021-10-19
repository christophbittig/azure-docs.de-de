---
title: Was ist der Arbeitsbereich? – Azure Healthcare-APIs
description: In diesem Artikel wird eine Übersicht über den Azure Healthcare-APIs-Arbeitsbereich beschrieben.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.date: 07/12/2021
ms.author: ginle
ms.openlocfilehash: 6eb5fa5186b7f28954724f27186b972f7efb08b0
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121784917"
---
# <a name="what-is-healthcare-apis-preview-workspace"></a>Was ist ein Arbeitsbereich für Die Gesundheits-APIs (Vorschauversion)?

> [!IMPORTANT]
> Azure Healthcare-APIs sind derzeit als VORSCHAUversion verfügbar. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

Der Azure Healthcare-APIs-Arbeitsbereich ist ein logischer Container für alle Ihre Instanzen des Gesundheitswesens, z. B. Fast Healthcare Interoperability Resources-Dienste (FHIR®), Digital Imaging und Kommunikation in der Medizin (DICOM®) und IoT-Connectors (Internet der Dinge). Der Arbeitsbereich erstellt auch eine Konformitätsgrenze (HIPAA, HITRUST), innerhalb der geschützte Integritätsinformationen gespeichert werden können.

Sie können mehrere Datendienste innerhalb eines Arbeitsbereichs bereitstellen und funktionieren standardmäßig nahtlos miteinander. Mit dem Arbeitsbereich können Sie alle Instanzen Ihrer Gesundheits-APIs organisieren und bestimmte Konfigurationseinstellungen verwalten, die von allen zugrunde liegenden Datasets und Diensten gemeinsam genutzt werden, sofern zutreffend.

## <a name="workspace-provisioning-process"></a>Bereitstellungsprozess für Arbeitsbereiche
 
Ein oder mehrere Arbeitsbereiche können in einer Ressourcengruppe über die Azure-Portal oder mithilfe von Bereitstellungsskripts erstellt werden. Ein Arbeitsbereich für Die Gesundheits-APIs muss zuerst als übergeordnetes Element in der hierarchischen Dienststruktur erstellt werden, bevor eine oder mehrere untergeordnete Dienstinstanzen erstellt werden können.   
 
Ein Arbeitsbereich kann nur gelöscht werden, wenn alle untergeordneten Dienstinstanzen innerhalb des Arbeitsbereichs gelöscht wurden. Dieses Feature hilft dabei, versehentliches Löschen von Dienstinstanzen zu verhindern. Wenn jedoch eine Arbeitsbereichsressourcengruppe gelöscht wird, werden alle Arbeitsbereiche und untergeordneten Dienstinstanzen innerhalb der Arbeitsbereichsressourcengruppe gelöscht. 

## <a name="workspace-and-azure-region-selection"></a>Auswahl des Arbeitsbereichs und der Azure-Region 
 
Wenn Sie einen Arbeitsbereich erstellen, muss er für eine Azure-Region konfiguriert werden, die mit der Ressourcengruppe identisch oder anders sein kann. Die Region kann nach dem Erstellen des Arbeitsbereichs nicht mehr geändert werden. In jedem Arbeitsbereich müssen alle Dienste der Gesundheits-APIs (FHIR-Dienst, DICOM-Dienst und IoT Connector-Dienst) in der Region des Arbeitsbereichs erstellt werden und können nicht in einen anderen Arbeitsbereich verschoben werden. 

## <a name="workspace-and-azure-healthcare-apis-service-instances"></a>Arbeitsbereichs- und Azure Healthcare-APIs-Dienstinstanzen 

Nachdem der Arbeitsbereich mit den Azure Healthcare-APIs erstellt wurde, können Sie eine oder mehrere Dienstinstanzen aus der Azure-Portal. Sie können mehrere Dienstinstanzen desselben Typs oder verschiedener Typen in einem Arbeitsbereich erstellen. Innerhalb des Arbeitsbereichs können Sie freigegebene Konfigurationseinstellungen auf untergeordnete Dienstinstanzen anwenden, die im Abschnitt Arbeitsbereich und Konfigurationseinstellungen behandelt werden.

[![Azure-Ressourcengruppe ](media/azure-resource-group.png) ](media/azure-resource-group.png#lightbox)

Darüber hinaus können Arbeitsbereiche mithilfe von Azure Resource Manager erstellt werden. Dies ist ein Prozess, der häufig als Infrastructure-as-Code (IaC) bekannt ist. Mit dieser Option können Sie die ARM-Vorlagen anpassen und die Erstellung des Arbeitsbereichs und der Dienstinstanz in einem kombinierten Schritt abschließen. 

Sie können PowerShell, die CLI, Terraform-Skripts oder das .NET SDK verwenden, um den Dienst für Gesundheits-APIs bereitzustellen. Um eine Dienstinstanz im Arbeitsbereich  zu erstellen, wählen Sie Erstellen (FHIR-Dienst, DICOM-Dienst oder IoT-Connectors) aus, und geben Sie dann die Kontodetails für diese Dienstinstanz ein, die erstellt wird.


## <a name="fhir-service"></a>FHIR-Dienst

Der FHIR-Dienst umfasst FHIR-APIs und -Endpunkte in Azure für den Datenzugriff und die Speicherung im FHIR-Datenformat. Ein FHIR-Dienst verwaltet geschützte Integritätsinformationen (Protected Health Information, PHI) in einer sicheren und konformen Cloudumgebung. Durch die Bereitstellung eines FHIR-Diensts können Sie basierend auf dem von HL7 veröffentlichten [interoperablen FHIR-Datenstandard](https://www.hl7.org/fhir/index.html) daten aus mehreren Systemen in der Azure-Cloud zusammenbringen. Weitere Informationen finden Sie unter [Informationen zum FHIR-Dienst.](./fhir/overview.md)

## <a name="dicom-service"></a>DICOM-Dienst

Stellen Sie einen DICOM-Dienst zum Bereitstellen von medizinischen Bildverarbeitungsdaten aus einem beliebigen DICOMwebTM-fähigen System in die Cloud zur Verfügung. Weitere Informationen finden Sie unter [Übersicht über den DICOM-Dienst.](dicom/dicom-services-overview.md)

## <a name="iot-connector"></a>IoT-Konnektor

Mit dem IoT Connector-Dienst können Sie hochfrequenze IoT-Gerätedaten auf skalierbare, sichere und konforme Weise im FHIR-Dienst aufnehmen. Weitere Informationen finden Sie auf [der Dokumentationsseite für den IoT-Connector.](./iot/index.yml)
 
## <a name="workspace-configuration-settings"></a>Einstellungen für die Arbeitsbereichskonfiguration

Einige Features werden auf Arbeitsbereichsebene konfiguriert und gelten für alle untergeordneten Dienste innerhalb dieses Arbeitsbereichs.

### <a name="application-monitoring"></a>Anwendungsüberwachung

Mit Azure Monitor werden Verfügbarkeit und Leistung Ihrer Anwendungen und Dienste maximiert. Er bietet eine umfassende Lösung für das Sammeln, Analysieren und Behandeln von Telemetriedaten aus Ihren Cloud- und lokalen Umgebungen. Diese Informationen bieten Ihnen Einblicke in die Leistung Ihrer Anwendungen und ermöglichen Es Ihnen, Probleme proaktiv zu identifizieren und zu beheben, die sie und die Ressourcen betreffen, von denen sie abhängen. Weitere Informationen zu Azure Monitor finden Sie in [Azure Monitor Übersichtsdokumentation.](../azure-monitor/index.yml)

### <a name="role-based-access-control-rbac"></a>Rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC)

Die rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC) ist ein System, das eine präzise Zugriffsverwaltung für Azure-Ressourcen ermöglicht. Mithilfe von Azure RBAC können Sie Aufgaben in Ihrem Team verteilen und Benutzern nur den Zugriff gewähren, den sie zur Ausführung ihrer Aufgaben benötigen. Darüber hinaus können Sie damit verwalten, wer Zugriff auf Azure-Ressourcen hat, was sie mit diesen Ressourcen tun können und auf welche Bereiche sie Zugriff haben. Weitere Informationen finden Sie in der [Azure RBAC-Dokumentation.](../role-based-access-control/index.yml)


## <a name="next-steps"></a>Nächste Schritte

Befolgen Sie die fünfminütige Schnellstart-Anweisung zum Bereitstellen eines Arbeitsbereichs, um mit der Arbeit mit den Azure Healthcare-APIs zu beginnen.

>[!div class="nextstepaction"]
>[Bereitstellen eines Arbeitsbereichs im Azure-Portal](healthcare-apis-quickstart.md)
---
title: Bezeichnungen in Azure Purview
description: Beginnen Sie mit der Verwendung von Vertraulichkeitsbezeichnungen und Klassifizierungen, um Ihre Purview-Ressourcen zu verbessern.
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 0cc474ba9566737cb0117cc3a0f2bfb079cdf3d9
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/06/2021
ms.locfileid: "129616510"
---
# <a name="labeling-in-azure-purview"></a>Bezeichnungen in Azure Purview

> [!IMPORTANT]
> Azure Purview-Vertraulichkeitsbezeichnungen befinden sich derzeit in der VORSCHAU. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.
>

Um die Arbeit zu erledigen, arbeiten Personen in Ihrer Organisation mit anderen Personen innerhalb und außerhalb der Organisation zusammen. Daten bleiben nicht immer in Ihrer Cloud und wandern häufig über Geräte, Apps und Dienste hierhin und dorthin. Wenn Ihre Daten verschoben werden, sollen sie dabei weiterhin so sicher sein, dass dies den Geschäfts- und Compliancerichtlinien Ihrer Organisation entspricht.</br>

Durch das Anwenden von Vertraulichkeitsbezeichnungen auf Ihre Inhalte können Sie Ihre Daten schützen, indem Sie angeben, wie vertraulich bestimmte Daten in Ihrer Organisation sind. Außerdem werden die Daten selbst abstrahiert, sodass Sie Bezeichnungen zum Nachverfolgen des Datentyps verwenden, ohne vertrauliche Daten auf einer anderen Plattform verfügbar zu machen.</br>

Wenn Sie beispielsweise die Vertraulichkeitsbezeichnung „Streng vertraulich“ auf ein Dokument anwenden, das eine US-Sozialversicherungsnummer und Kreditkartennummern enthält, können Sie die Vertraulichkeitsstufe des Dokuments ermitteln, ohne die tatsächlichen Daten im Dokument zu kennen.

## <a name="benefits-of-labeling-in-azure-purview"></a>Vorteile der Bezeichnung in Azure Purview

Mit Azure Purview können Sie Vertraulichkeitsbezeichnungen auf Ressourcen anwenden, sodass Sie Ihre Daten klassifizieren und schützen können.

* **Bezeichnung wird mit den Daten verschoben:** Die in Purview verwendeten Vertraulichkeitsbezeichnungen werden derzeit in Microsoft 365, SharePoint, Teams, Power BI und SQL erkannt. Wenn eine Bezeichnung auf eine Ressource in Purview angewendet wird und Ihre Daten auf eine der anderen Plattformen wie Power BI oder Office verschoben wird, wird die Bezeichnung mit den Daten übertragen. Wenn eine Bezeichnung auf ein Word-Dokument angewendet und dann von Purview gescannt wird, wird die Bezeichnung entsprechend an Purview übertragen.
* **Übersicht über Ihre Daten:** Purview bietet mithilfe von vorgescannten Berichten Einblicke in Ihre Daten. Wenn Sie Daten in Purview scannen, werden die Berichte beispielsweise mit Informationen zu Ihren Ressourcen, dem Überprüfungsverlauf, den in Ihren Daten gefundenen Klassifizierungen, angewendeten Bezeichnungen und Glossarbegriffen aktualisiert.
* **Automatische Bezeichnung:** Bezeichnungen können basierend auf der Vertraulichkeit der Daten automatisch angewendet werden. Wenn eine Ressource auf vertrauliche Daten überprüft wird, werden Regeln für die automatische Bezeichnung verwendet, um zu entscheiden, welche Vertraulichkeitsbezeichnung angewendet werden soll. Sie können Regeln für die automatische Bezeichnung für jede Vertraulichkeitsbezeichnung erstellen und definieren, welcher Typ von Klassifizierung bzw. vertraulichen Informationen eine Bezeichnung ausmacht.
* **Anwenden von Bezeichnungen auf Dateien und Datenbankspalten:** Bezeichnungen können auf Dateien im Speicher (z. B. Azure Data Lake, Azure Files usw.) und auf schematisierte Daten (z. B. Spalten in Azure SQL DB, Cosmos DB usw.) angewendet werden.

Vertraulichkeitsbezeichnungen sind Tags, die Sie auf Ressourcen anwenden können, um Ihre Daten zu klassifizieren und zu schützen. Weitere Informationen zu Vertraulichkeitsbezeichnungen finden Sie [hier](/microsoft-365/compliance/create-sensitivity-labels.md).

## <a name="how-to-apply-labels-to-assets-in-azure-purview"></a>Anwenden von Bezeichnungen auf Ressourcen in Azure Purview

:::image type="content" source="media/create-sensitivity-label/apply-label-flow.png" alt-text="Anwenden von Bezeichnungen auf Ressourcen im Purview-Flow: Erstellen von Bezeichnungen, Registrieren einer Ressource, Überprüfen der Ressource, Ermitteln von Klassifizierungen und Anwenden von Bezeichnungen":::

Um Bezeichnungen auf Ihre Ressource in Azure Purview anwenden zu können, müssen Sie die folgenden Schritte ausführen:

1. [Erstellen Sie Vertraulichkeitsbezeichnungen, oder erweitern Sie vorhandene Vertraulichkeitsbezeichnungen auf Azure Purview](how-to-automatically-label-your-content.md) im Microsoft 365 Compliance Center. Das Erstellen von Vertraulichkeitsbezeichnungen umfasst Regeln für die automatische Bezeichnung, die Ihnen mitteilen, welche Bezeichnung basierend auf den in Ihren Daten gefundenen Klassifizierungen angewendet werden soll.
1. [Registrieren und scannen Sie Ihre Ressource](how-to-automatically-label-your-content.md#scan-your-data-to-apply-sensitivity-labels-automatically) in Azure Purview.
1. Azure Purview wendet Klassifizierungen an: Wenn Sie eine Überprüfung einer Ressource planen, scannt Azure Purview den Typ der Daten in Ihrer Ressource und wendet Klassifizierungen im Datenkatalog darauf an. Die Anwendung von Klassifizierungen wird automatisch von Azure Purview durchgeführt, sodass Sie keine Schritte ausführen müssen.
1. Azure Purview wendet Bezeichnungen an: Sobald Klassifizierungen für eine Ressource gefunden wurden, wendet Azure Purview in Abhängigkeit von den Regeln für die automatische Bezeichnung entsprechende Bezeichnungen auf die Ressourcen an. Die Anwendung von Klassifizierungen wird automatisch von Azure Purview durchgeführt, und Sie müssen keine Schritte ausführen, solange Sie in Schritt 1 Bezeichnungen mit Regeln für die automatische Bezeichnung erstellt haben.

> [!NOTE]
> Die Regeln für die automatische Bezeichnung sind Bedingungen, mit denen Sie angeben, wann eine bestimmte Bezeichnung angewendet werden soll. Wenn diese Bedingungen erfüllt sind, wird die Bezeichnung den Daten automatisch zugewiesen. Stellen Sie beim Erstellen der Bezeichnungen sicher, dass Sie sowohl für Dateien als auch Datenbankspalten Regeln für die automatische Bezeichnung definieren, um die Bezeichnungen automatisch mit jedem Scan anzuwenden.
>

## <a name="supported-data-sources"></a>Unterstützte Datenquellen

Vertraulichkeitsbezeichnungen werden in Azure Purview für die folgenden Datenquellen unterstützt:

|Datentyp  |Quellen  |
|---------|---------|
|Automatische Bezeichnung für Dateien     |    : Azure Blob Storage</br>- Azure Files</br>– Azure Data Lake Storage Gen 1 und Gen 2</br>– Amazon S3|
|Automatische Bezeichnung für Datenbankspalten     |  – SQL Server</br>– Azure SQL-Datenbank</br>– Verwaltete Azure SQL-Datenbank-Instanz</br>– Azure Synapse Analytics-Arbeitsbereiche</br>– Azure Cosmos DB (SQL-API)</br> – Azure Database for MySQL</br> – Azure Database for PostgreSQL</br> – Azure Data Explorer</br>  |
| | |

## <a name="labeling-for-sql-databases"></a>Bezeichnungen für SQL-Datenbanken

Zusätzlich zu Purview-Bezeichnungen für Datenbankspalten unterstützt Microsoft auch Bezeichnungen für die Spalten von SQL-Datenbanken, indem die SQL-Datenklassifizierung in [SQL Server Management Studio (SSMS)](/sql/ssms/sql-server-management-studio-ssms) verwendet wird. Während in Purview die globalen [Vertraulichkeitsbezeichnungen](/microsoft-365/compliance/sensitivity-labels) verwendet werden, werden in SSMS nur lokal definierte Bezeichnungen genutzt.

Bei den Bezeichnungen in Purview und in SSMS handelt es sich um separate Prozesse, die derzeit nicht miteinander interagieren. Aus diesem Grund werden **in SSMS angewendete Bezeichnungen in Purview nicht angezeigt (und umgekehrt)** . Für Bezeichnungen in SQL-Datenbanken empfehlen wir Azure Purview, da hierbei globale MIP-Bezeichnungen genutzt werden, die übergreifend für mehrere Plattformen angewendet werden können.

Weitere Informationen finden Sie unter [SQL-Datenermittlung und -klassifizierung](/sql/relational-databases/security/sql-data-discovery-and-classification). </br></br>

> [!div class="nextstepaction"]
> [Automatisches Bezeichnen von Inhalten](./how-to-automatically-label-your-content.md)

> [!div class="nextstepaction"]
> [Vertraulichkeitsbezeichnungs-Erkenntnisse](sensitivity-insights.md)

> [!div class="nextstepaction"]
> [Häufig gestellte Fragen zu Bezeichnungen](sensitivity-labels-frequently-asked-questions.yml)

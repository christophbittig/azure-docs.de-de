---
title: Purview-Sammlungs-Architektur und bewährte Methoden
description: In diesem Artikel finden sich Beispiele für die Architekturen von Azure Purview-Sammlungen und Beschreibungen bewährter Methoden.
author: zeinam
ms.author: zeinam
ms.service: purview
ms.subservice: purview-data-map
ms.topic: conceptual
ms.date: 09/15/2021
ms.openlocfilehash: 52eb75c054ec9b930898b86f5a58f5334656523a
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129207486"
---
# <a name="azure-purview-collections-architectures-and-best-practices"></a>Azure Purview-Sammlungs-Architekturen und bewährte Methoden  

Im Kern von Azure Purview ist die Data Map eine Platform as a Service (PaaS)-Komponente, die eine stets aktuelle Zuordnung von Ressourcen und ihren Metadaten in Ihrem Datenbestand pflegt. Um die Datenzuordnung zu aktualisieren, müssen Sie Ihre Datenquellen registrieren und scannen. In einer Organisation kann es mehrere tausend Datenquellen geben, die von zentralisierten oder dezentralisierten Teams verwaltet und gesteuert werden.  

[Sammlungen](./how-to-create-and-manage-collections.md) in Azure Purview unterstützen die organisationsbezogene Zuordnung von Metadaten. Mit Sammlungen können Sie Datenquellen, Scans und Ressourcen in einer Hierarchie verwalten und pflegen, anstatt in einer flachen Struktur. Auch können Sie mit Sammlungen ein benutzerdefiniertes hierarchisches Modell Ihrer Datenlandschaft erstellen, basierend darauf, wie Ihre Organisation mit Azure Purview Ihre Landschaft steuern möchte.

Eine Sammlung stellt auch eine Sicherheitsbegrenzung für Ihre Metadaten in der Data Map bereit. Der Zugriff auf Sammlungen, Datenquellen und Metadaten wird basierend auf der Hierarchie der Sammlungen in Azure Purview eingerichtet und gepflegt. Dabei wird das Modell der geringsten Berechtigung genutzt: 
- Benutzer haben nur in dem Umfang Zugriffsberechtigungen, der für die Ausführung ihrer Aufgaben erforderlich ist. 
- Benutzer haben keinen Zugriff auf vertrauliche Daten, die sie nicht benötigen.

## <a name="intended-audience"></a>Zielpublikum

- Datenarchitektur-Team 
- Daten-Governance- und Datenverwaltungs-Teams
- Datensicherheitsteam

## <a name="why-do-you-need-to-define-collections-and-an-authorization-model-for-your-azure-purview-account"></a>Warum müssen Sie Sammlungen und ein Autorisierungsmodell für Ihr Azure Purview-Konto definieren? 

Überlegen Sie, ob Sie Sammlungen in Azure Purview bereitstellen möchten, um die folgenden Anforderungen zu erfüllen: 

- Organisieren Sie Datenquellen, verteilen Sie Ressourcen, und führen Sie Scans durch – basierend auf Ihren Geschäftsanforderungen, der geografischen Verteilung von Daten und den Datenverwaltungsteams, Abteilungen oder Geschäftsfunktionen. 

- Delegieren Sie die Zuständigkeit für Datenquellen und Ressourcen an die richtigen Teams, indem Sie den entsprechenden Sammlungen Rollen zuweisen. 

- Durchsuchen und filtern Sie Ressourcen nach Sammlungen. 
 

## <a name="define-a-collection-hierarchy"></a>Definieren Sie eine Sammlungshierarchie.  

### <a name="design-considerations"></a>Überlegungen zum Entwurf  

- Jedes Purview-Konto wird mit einer standardmäßigen _Stammsammlung_ erstellt. Der Name der Stammsammlung ist mit dem Namen Ihres Azure Purview-Kontos identisch. Die Stammsammlung kann nicht entfernt werden. Um den Anzeigenamen der Stammsammlung zu ändern, können Sie den Anzeigenamen Ihres Purview-Kontos im Purview Management Center ändern.   

- Sammlungen können Datenquellen, Scans, Ressourcen und Rollenzuweisungen enthalten.

- Eine Auflistung kann so viele untergeordnete Auflistungen enthalten, wie benötigt werden. Aber jede Sammlung kann nur eine übergeordnete Sammlung haben. Sie können keine Sammlungen oberhalb der Stammsammlung bereitstellen.

- Datenquellen, Scans und Ressourcen können nur zu einer Sammlung gehören. 

- Eine Sammlungshierarchie in Azure Purview kann bis zu 300 Sammlungen unterstützen, die maximal acht Ebenen tief sein können. Dabei ist die Stammsammlung nicht eingeschlossen. 

- Standardmäßig können Sie Datenquellen nicht mehrmals in einem einzelnen Purview-Konto registrieren. Diese Architektur soll das Risiko vermeiden, dass einer einzelnen Datenquelle verschiedene Ebenen der Zugriffssteuerung zugewiesen werden. Wenn mehrere Teams die Metadaten einer einzelnen Datenquelle nutzen, können Sie die Datenquelle in einer übergeordneten Sammlung registrieren und verwalten. Anschließend können Sie entsprechende Scans in jeder untergeordneten Sammlung erstellen, damit relevante Ressourcen in jeder untergeordneten Sammlung angezeigt werden.

- Herkunftsverbindungen und Artefakte werden selbst dann der Stammsammlung angefügt, wenn die Datenquellen bei Sammlungen aus niedrigeren Ebenen registriert sind.

- Wenn Sie einen neuen Scan ausführen, wird der Scan standardmäßig in derselben Sammlung wie die Datenquelle bereitgestellt. Sie können optional eine andere untergeordnete Sammlung für die Ausführung des Scans auswählen. Dies hat zur Folge, dass die Ressourcen zur untergeordneten Sammlung gehören. 

- Derzeit ist das Verschieben von Datenquellen zwischen Sammlungen nicht zulässig. Um eine Datenquelle in eine andere Sammlung zu verschieben, müssen Sie alle Ressourcen löschen, die Datenquelle aus der ursprünglichen Sammlung entfernen und die Datenquelle in der Zielsammlung erneut registrieren.

- Das Verschieben von Ressourcen zwischen Sammlungen ist zulässig, wenn dem Benutzer die Datenkurator-Rolle für die Quell- und Zielsammlungen zugewiesen wird. 

- Derzeit sind bestimmte Vorgänge wie das Löschen, Verschieben und Umbenennen einer Sammlung nicht zulässig. 

- Datenquellen, Scans und Ressourcen müssen zu einer Sammlung gehören, wenn sie in der Azure Purview Data Map vorhanden sind.    

<!-- 
- Moving data sources across collections is allowed if the user is granted the Data Source Admin role for the source and destination collections. 

- Moving assets across collections is allowed if the user is granted the Data Curator role for the source and destination collections. 

- Certain operations, like delete, move, and rename of a collection, aren't allowed via the Azure Purview Studio graphical interface. You can use the API to perform such operations directly in your Azure Purview data map. 

- You can delete a collection if there are no assets or data sources associated with the collection. You can delete a collection that has a scan associated with it. 
-->

### <a name="design-recommendations"></a>Entwurfsempfehlungen 

- Lesen Sie den Artikel [Bewährten Methoden für das Azure Purview-Konto](./deployment-best-practices.md#determine-the-number-of-purview-instances). Bestimmen Sie, wie viele Purview-Konten in Ihrer Organisation benötigt werden, bevor Sie die Sammlungsstruktur planen.  

- Wir empfehlen, dass Sie Ihre Sammlungsarchitektur basierend auf den Sicherheitsanforderungen und der Datenverwaltungs- und Governance-Struktur Ihrer Organisation entwerfen. Informationen zu den empfohlenen [Sammlungsarchetypen](#collections-archetypes) finden Sie in diesem Artikel.

- Für zukünftige Skalierbarkeit empfehlen wir, unterhalb der Stammsammlung eine Sammlung der obersten Ebene für Ihre Organisation zu erstellen. Weisen Sie relevante Rollen in der Sammlung der obersten Ebene zu, anstatt in der Stammsammlung.  

- Berücksichtigen Sie bei allen Design-Entscheidungen die Sicherheits- und Zugriffsverwaltung, wenn Sie Sammlungen in Azure Purview erstellen. 

- Jede Sammlung verfügt über ein Attribut für den Namen und ein Attribut für den Anzeigenamen. Wenn Sie eine Sammlung mit Azure [Purview Studio](https://web.purview.azure.com/resource/) bereitstellen, weist das System der Sammlung automatisch einen zufälligen, sechsstelligen Namen zu, um Duplikate zu vermeiden. Vermeiden Sie für eine einfachere Nutzung die Verwendung doppelter Anzeigenamen in Ihren Sammlungen, insbesondere auf derselben Ebene.  

- Wann immer möglich, vermeiden Sie das Duplizieren ihrer Organisationsstruktur in einer tief geschachtelten Sammlungshierarchie. Wenn Sie das nicht vermeiden können, sollten Sie unbedingt verschiedene Namen für jede Sammlung in der Hierarchie verwenden, damit sich die Sammlungen leicht voneinander unterscheiden lassen.

- Automatisieren Sie die Bereitstellung von Sammlungen mithilfe der API, wenn Sie die Massenbereitstellung von Sammlungen und Rollenzuweisungen planen.

- Verwenden Sie einen dedizierten Dienstprinzipalnamen (Service Principal Name, SPN), um Vorgänge für Sammlungen und Rollenzuweisungen mithilfe der API durchzuführen. Mithilfe eines SPNs wird die Anzahl der Benutzer mit erhöhten Berechtigungen reduziert und die Richtlinien der geringstmöglichen Berechtigungen werden eingehalten.

## <a name="define-an-authorization-model"></a>Definieren eines Autorisierungsmodells

Azure Purview-Datenebenenrollen werden in Azure Purview verwaltet. Nachdem Sie ein Purview-Konto bereitgestellt haben, werden dem Ersteller des Purview-Kontos automatisch die folgenden Rollen in der Stammsammlung zugewiesen. Sie können [Purview Studio](https://web.purview.azure.com/resource/) oder eine programmgesteuerte Methode verwenden, um Rollen in Azure Purview direkt zuzuweisen und zu verwalten.

  - **Sammlungsadministratoren** können Purview-Sammlungen und deren Details bearbeiten und untergeordnete Sammlungen hinzufügen. Darüber hinaus können sie Benutzer in Sammlungen, in denen sie Administratoren sind, zu anderen Purview-Rollen hinzufügen.
  - **Datenquellenadministratoren** können Datenquellen und Datenüberprüfungen verwalten.
  - **Datenkuratoren** können Katalogdatenassets erstellen, lesen, ändern und löschen und Beziehungen zwischen Assets herstellen.
  - **Datenleseberechtigte** können auf Katalogdatenassets zugreifen, diese aber nicht ändern.

### <a name="design-considerations"></a>Überlegungen zum Entwurf  

- Die Azure Purview-Zugriffsverwaltung wurde in die Datenebene verschoben. Azure Resource Manager-Rollen werden nicht mehr verwendet. Daher sollten Sie Azure Purview nutzen, um Rollen zu zuweisen. 

- In Azure Purview können Sie Benutzern, Sicherheitsgruppen und Dienstprinzipalen (einschließlich verwalteter Identitäten) Rollen von Azure Active Directory (Azure AD) auf demselben Azure AD-Mandanten zuweisen, in dem das Purview-Konto bereitgestellt wird.
  
- Sie müssen Ihrem Azure AD-Mandanten zuerst Gastkonten als B2B-Benutzer hinzufügen, bevor Sie externen Benutzern Purview-Rollen zuweisen können. 

- Standardmäßig haben Sammlungsadministratoren keinen Zugriff zum Lesen oder Ändern von Ressourcen. Sie können jedoch ihre Zugriffsberechtigungen ausweiten und sich selbst zu mehr Rollen hinzufügen.

- Standardmäßig werden alle Rollenzuweisungen automatisch von allen untergeordneten Sammlungen übernommen. Sie können aber für alle Sammlungen außer der Stammsammlung das **Einschränken der geerbten Berechtigungen** aktivieren. Das **Einschränken der geerbten Berechtigungen** entfernt die geerbten Rollen aus allen übergeordneten Sammlungen, mit Ausnahme der Rolle „Sammlungsadministrator“. 

- Für die Azure Data Factory-Verbindung: Um eine Verbindung mit Azure Data Factory herzustellen, müssen Sie Sammlungsadministrator für die Stammsammlung sein.

- Wenn Sie eine Verbindung mit Azure Data Factory für die Herkunft herstellen müssen, erteilen Sie der verwalteten Identität der Data Factory die Datenkurator-Rolle auf der Purview-Stammsammlungsebene. Wenn Sie über die Dokumenterstellungs-Benutzeroberfläche eine Verbindung von Data Factory zu Purview herstellen, versucht Data Factory, diese Rollenzuweisungen automatisch hinzuzufügen. Wenn Sie für die Purview-Stammsammlung die Rolle „Sammlungsadministrator“ haben, wird dieser Vorgang funktionieren. 

### <a name="design-recommendations"></a>Entwurfsempfehlungen 

- Ziehen Sie die Implementierung eines [Notfallzugriffs](/azure/active-directory/users-groups-roles/directory-emergency-access) oder einer Strategie für den erzwungenen Zugriff für die Sammlungsadministrator-Rolle auf der Azure Purview-Stammsammlungsebene, um Sperrungen auf Purview-Kontoebene zu vermeiden. Dokumentieren Sie den Prozess für die Verwendung von Notfallkonten. 

    > [!NOTE]
    > In bestimmten Szenarien müssen Sie möglicherweise ein Notfallkonto verwenden, um sich bei Azure Purview anzumelden. Möglicherweise benötigen Sie ein solches Konto, um Zugriffsprobleme auf Organisationsebene zu beheben, wenn sich keine andere Person bei Purview anmelden kann oder wenn andere Administratoren aufgrund von Problemen bei der Unternehmensauthentifizierung bestimmte Vorgänge nicht ausführen können. Es wird dringend empfohlen, den bewährten Methoden von Microsoft für die Implementierung von [Konten für den Notfallzugriff](/azure/active-directory/users-groups-roles/directory-emergency-access) zu folgen und ausschließlich cloudbasierte Benutzer zu nutzen.
    >
    > Befolgen Sie die Anweisungen in [diesem Artikel](./concept-account-upgrade.md#what-happens-when-your-upgraded-account-doesnt-have-a-collection-admin), um den Zugriff auf Ihre Purview-Stammsammlung wiederherzustellen, wenn Ihr vorheriger Sammlungsadministrator nicht verfügbar ist.

- Minimieren Sie die Anzahl der Stammsammlungsadministratoren. Weisen Sie der Stammsammlung maximal drei Sammlungsadministratorbenutzer zu, einschließlich des SPN und Ihrer Notfallkonten. Weisen Sie Ihre Sammlungsadministratorrollen stattdessen der Sammlung der obersten Ebene oder untergeordneten Sammlungen zu.

- Weisen Sie Rollen Gruppen anstelle einzelner Benutzer zu, um den Verwaltungsaufwand und Fehler bei der Verwaltung einzelner Rollen zu reduzieren. 

- Weisen Sie den Dienstprinzipal zu Automatisierungszwecken in der Stammsammlung zu.

- Um die Sicherheit zu erhöhen, aktivieren Sie den bedingten Zugriff von Azure AD mit MFA mindestens für Sammlungsadministratoren, Datenquellenadministratoren und Datenkuratoren. Stellen Sie sicher, dass Notfallkonten von der Richtlinie für bedingten Zugriff ausgeschlossen sind.
 
## <a name="collections-archetypes"></a>Sammlungs-Archetypen

Sie können Ihre Azure Purview-Sammlung basierend auf zentralisierten, dezentralisierten oder hybriden Datenverwaltungs- und Governancemodellen bereitstellen. Treffen Sie diese Entscheidung basierend auf Ihren Unternehmens- und Sicherheitsanforderungen.

### <a name="example-1-single-region-organization"></a>Beispiel 1: Organisation in einer einzelnen Region 

Diese Struktur eignet sich für Organisationen, die: 
- hauptsächlich an einem einzelnen geografischen Standort angesiedelt sind 
- über ein zentralisiertes Datenverwaltungs- und Governanceteam verfügen, bei dem die nächste Ebene der Datenverwaltung in Abteilungen, Teams oder Projekte fällt  

Die Sammlungshierarchie besteht aus den folgenden Vertikalen: 

- Stammsammlung (Standard)
- Contoso (Sammlung der obersten Ebene)
- Abteilungen (eine delegierte Sammlung für jede Abteilung) 
- Teams oder Projekte (weitere Trennung basierend auf Projekten)

Jede Datenquelle wird registriert und in der entsprechenden Sammlung überprüft. Objekte werden also auch in derselben Sammlung angezeigt. 

Freigegebene Datenquellen auf Organisationsebene werden registriert und in der Hub-Shared-Sammlung überprüft. 

Die freigegebenen Datenquellen auf Abteilungsebene werden registriert und in den Abteilungssammlungen überprüft. 

:::image type="content" source="media/concept-best-practices/collections-example-1.png" alt-text="Screenshot: Erstes Beispiel für Azure Purview-Sammlungen"lightbox="media/concept-best-practices/collections-example-1.png":::

### <a name="example-2-multiregion-organization"></a>Beispiel 2: Organisation in mehreren Regionen

Dieses Szenario eignet sich für Organisationen: 
- die in mehreren Regionen vertreten sind 
- deren Datengovernanceteam zentralisiert oder dezentralisiert in jeder Region ist
- deren Datenverwaltungsteams an jedem geografischen Standort verteilt sind 

Die Sammlungshierarchie besteht aus den folgenden Vertikalen: 

- Stammsammlung (Standard)
- FourthCoffee (Sammlung der obersten Ebene)
- Geografische Standorte (Sammlungen auf mittlerer Ebene, basierend auf geografischen Standorten, an denen sich Datenquellen und Datenbesitzer befinden)
- Abteilungen (eine delegierte Sammlung für jede Abteilung) 
- Teams oder Projekte (weitere Trennung basierend auf Teams oder Projekten)

In diesem Szenario verfügt jede Region über eine eigene untergeordnete Sammlung unter der Sammlung der obersten Ebene im Purview-Konto. Datenquellen werden in den entsprechenden untergeordneten Sammlungen an ihren eigenen geografischen Standorten registriert und überprüft. Objekte werden also auch in der Hierarchie der untergeordneten Sammlung für die Region angezeigt. 

Wenn Sie über zentralisierte Datenverwaltungs- und Governanceteams verfügen, können Sie ihnen Zugriff aus der Sammlung der obersten Ebene gewähren. Wenn Sie dies tun, erhalten sie einen Überblick über den gesamten Datenbestand in der Data Map. Optional kann das zentralisierte Team alle freigegebenen Datenquellen registrieren und überprüfen.

Regionbasierte Datenverwaltungs- und Governanceteams können Zugriff von ihren entsprechenden Sammlungen auf einer niedrigeren Ebene erhalten.

Die freigegebenen Datenquellen auf Abteilungsebene werden registriert und in den Abteilungssammlungen überprüft. 

:::image type="content" source="media/concept-best-practices/collections-example-2.png" alt-text="Screenshot: Zweites Beispiel für Azure Purview-Sammlungen"lightbox="media/concept-best-practices/collections-example-2.png":::

### <a name="example-3-multiregion-data-transformation"></a>Beispiel 3: Mehrere Regionen, Datentransformation

Dieses Szenario kann nützlich sein, wenn Sie die Verwaltung des Metadatenzugriffs basierend auf geografischen Standorten und Datentransformationszuständen verteilen möchten. Datenwissenschaftler und Datentechniker, die Daten transformieren können, um sie aussagekräftiger zu machen, können Rohdatenzonen und optimierte Datenzonen verwalten. Sie können die Daten dann in Erstellungs- oder kuratierte Zonen verschieben.  

Die Sammlungshierarchie besteht aus den folgenden Vertikalen: 

- Stammsammlung (Standard)
- Fabrikam (Sammlung auf oberster Ebene)
- Geografische Standorte (Sammlungen auf mittlerer Ebene, basierend auf geografischen Standorten, an denen sich Datenquellen und Datenbesitzer befinden)
- Datentransformationsphasen (Rohdaten, optimierte Daten, Erstellung/kuratiert) 

Datenwissenschaftler und Datentechniker können in ihren entsprechenden Zonen die Datenkurator-Rolle erhalten, damit sie Metadaten zusammenstellen können. Zugriff als Datenleseberechtigter auf die kuratierte Zone kann ganzen Datenpersonas sowie Geschäftsbenutzern gewährt werden. 

:::image type="content" source="media/concept-best-practices/collections-example-3.png" alt-text="Screenshot: Drittes Beispiel für Azure Purview-Sammlungen"lightbox="media/concept-best-practices/collections-example-3.png":::

### <a name="example-4-multiregion-business-functions"></a>Beispiel 4: Mehrere Regionen, Geschäftsfunktionen 

Diese Option kann von Organisationen verwendet werden, die Metadaten und die Zugriffsverwaltung basierend auf Geschäftsfunktionen organisieren müssen.

Die Sammlungshierarchie besteht aus den folgenden Vertikalen: 

- Stammsammlung (Standard)
- AdventureWorks (Sammlung auf oberster Ebene)
- Geografische Standorte (Sammlungen auf mittlerer Ebene, basierend auf geografischen Standorten, an denen sich Datenquellen und Datenbesitzer befinden)
- Wichtige Geschäftsfunktionen oder Clients (weitere Trennung basierend auf Funktionen oder Clients)

Jede Region verfügt über eine eigene untergeordnete Sammlung unter der Sammlung der obersten Ebene im Purview-Konto. Datenquellen werden in den entsprechenden untergeordneten Sammlungen an ihren eigenen geografischen Standorten registriert und überprüft. Objekte werden also auch in der Hierarchie der untergeordneten Sammlung für die Region angezeigt. 

Wenn Sie über zentralisierte Datenverwaltungs- und Governanceteams verfügen, können Sie ihnen Zugriff aus der Sammlung der obersten Ebene gewähren. Wenn Sie dies tun, erhalten sie einen Überblick über den gesamten Datenbestand in der Data Map. Optional kann das zentralisierte Team alle freigegebenen Datenquellen registrieren und überprüfen.

Regionbasierte Datenverwaltungs- und Governanceteams können Zugriff von ihren entsprechenden Sammlungen auf einer niedrigeren Ebene erhalten.
Jede Geschäftseinheit verfügt über eine eigene untergeordnete Sammlung.

:::image type="content" source="media/concept-best-practices/collections-example-4.png" alt-text="Screenshot: Viertes Beispiel für Azure Purview-Sammlungen"lightbox="media/concept-best-practices/collections-example-4.png":::

## <a name="access-management-options"></a>Optionen für die Zugriffsverwaltung

Wenn Sie die Datendemokratisierung in einer gesamten Organisation implementieren möchten, weisen Sie Datenverwaltungs-, Governance- und Geschäftsbenutzern die Datenleseberechtigter-Rolle auf oberster Ebene zu. Weisen Sie den entsprechenden Datenverwaltungs- und Governanceteams Datenquellenadministrator- und Datenkurator-Rollen auf untergeordnete Sammlungsebenen zu.

Wenn Sie den Zugriff auf die Suche und Ermittlung von Metadaten in Ihrer Organisation einschränken müssen, weisen Sie die Datenleseberechtigter- und Datenkurator-Rollen auf der spezifischen Sammlungsebene zu. Sie können z. B. Mitarbeiter in den USA einschränken, sodass sie Daten nur auf der Ebene der US-Sammlung und nicht in der lateinamerikanischen Sammlung lesen können. 

Sie können eine Kombination dieser beiden Szenarien in Ihrer Purview-Data Map anwenden, wenn eine vollständige Datendemokratisierung mit wenigen Ausnahmen für einige Sammlungen erforderlich ist. Sie können Purview-Rollen in der Sammlung der obersten Ebene zuweisen und die Vererbung auf die spezifischen untergeordneten Sammlungen beschränken.

Weisen Sie die Sammlungsadministrator-Rolle dem zentralisierten Datensicherheits- und -verwaltungsteam in der Sammlung der obersten Ebene zu. Delegieren Sie die weitere Sammlungsverwaltung von Sammlungen auf niedrigerer Ebene an die entsprechenden Teams.

## <a name="next-steps"></a>Nächste Schritte
-  [Erstellen einer Sammlung und Zuweisen von Berechtigungen in Purview](./quickstart-create-collection.md)
-  [Erstellen und Verwalten von Sammlungen in Azure Purview](./how-to-create-and-manage-collections.md)
-  [Zugriffssteuerung in Azure Purview](./catalog-permissions.md)

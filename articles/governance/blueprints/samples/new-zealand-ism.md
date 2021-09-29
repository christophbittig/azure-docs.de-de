---
title: 'Blaupausenbeispiel: Durch New Zealand ISM eingeschränkt'
description: Übersicht über das Blaupausenbeispiel „Durch New Zealand ISM eingeschränkt“ Dieses Blaupausenbeispiel unterstützt Kunden bei der Bewertung spezifischer Kontrollen.
ms.date: 09/08/2021
ms.topic: sample
ms.openlocfilehash: f64d14bc7fd2a3dd133f1f3fd4da6d5de39f9f01
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128659719"
---
# <a name="new-zealand-ism-restricted-blueprint-sample"></a>Blaupausenbeispiel: Durch New Zealand ISM eingeschränkt

Das Blaupausenbeispiel „Durch New Zealand ISM eingeschränkt“ bietet mithilfe von [Azure Policy](../../policy/overview.md) Schutzmaßnahmen für Governance, die Ihnen beim Bewerten spezifischer NZISM-Kontrollen ([New Zealand Information Security Manual](https://www.nzism.gcsb.govt.nz/)) helfen. Diese Blaupause hilft Kunden bei der Bereitstellung eines Kernsatzes von Richtlinien für jede in Azure bereitgestellte Architektur, in der Kontrollen für „Durch New Zealand ISM eingeschränkt“ implementiert werden müssen.

## <a name="control-mapping"></a>Steuerungszuordnung

In der [Azure Policy-Steuerungszuordnung](../../policy/samples/new-zealand-ism.md) finden Sie Details zu den in dieser Blaupause enthaltenen Richtliniendefinitionen. Darüber hinaus erfahren Sie, wie diese Richtlinien mit den **Kontrollen** im New Zealand Information Security Manual zusammenhängen. Bei der Zuweisung zu einer Architektur werden Ressourcen von Azure Policy auf Nichtkonformität mit den zugewiesenen Richtliniendefinitionen überprüft. Weitere Informationen finden Sie unter [Was ist Azure Policy?](../../policy/overview.md).

## <a name="deploy"></a>Bereitstellen

Führen Sie zum Bereitstellen des Blaupausenbeispiels „Durch New Zealand ISM eingeschränkt“ von Azure Blueprints die folgenden Schritte aus:

> [!div class="checklist"]
> - Erstellen einer neuen Blaupause mithilfe des Beispiels
> - Markieren Ihrer Kopie des Beispiels als **Veröffentlicht**
> - Zuweisen Ihrer Kopie der Blaupause zu einem vorhandenen Abonnement

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free) erstellen, bevor Sie beginnen.

### <a name="create-blueprint-from-sample"></a>Erstellen einer Blaupause mithilfe des Beispiels

Implementieren Sie zuerst das Blaupausenbeispiel, indem Sie mithilfe des Beispiels eine neue Blaupause in Ihrer Umgebung erstellen.

1. Wählen Sie **Alle Dienste** im linken Bereich aus. Suchen Sie nach **Blaupausen**, und wählen Sie die Option aus.

1. Klicken Sie links auf der Seite **Erste Schritte** unter _Blaupause erstellen_ auf die Schaltfläche **Erstellen**.

1. Suchen Sie unter _Weitere Beispiele_ nach dem Blaupausenbeispiel **Durch New Zealand ISM eingeschränkt**, und wählen Sie **Dieses Beispiel verwenden** aus.

1. Geben Sie die _Grundlagen_ des Blaupausenbeispiels ein:

   - **Name der Blaupause**: Geben Sie einen Namen für Ihre Kopie des Blaupausenbeispiels „Durch New Zealand ISM eingeschränkt“ ein.
   - **Definitionsspeicherort**: Klicken Sie auf die Schaltfläche mit den Auslassungspunkten, und wählen Sie die Verwaltungsgruppe aus, in der Sie Ihre Kopie des Beispiels speichern möchten.

1. Wählen Sie oben auf der Seite die Registerkarte _Artefakte_ oder unten auf der Seite die Option **Weiter: Artefakte** aus.

1. Überprüfen Sie die Liste der Artefakte, die im Blaupausenbeispiel enthalten sind. Viele der Artefakte enthalten Parameter, die Sie später definieren. Wählen Sie **Entwurf speichern** aus, nachdem Sie das Blaupausenbeispiel überprüft haben.

### <a name="publish-the-sample-copy"></a>Veröffentlichen der Kopie des Beispiels

Ihre Kopie des Blaupausenbeispiels wurde jetzt in Ihrer Umgebung erstellt. Sie wird im Modus **Entwurf** erstellt und muss **veröffentlicht** werden, bevor sie zugewiesen und bereitgestellt werden kann. Die Kopie des Blaupausenbeispiels kann an Ihre Umgebung und Anforderungen angepasst werden. Durch diese Änderungen ist sie aber unter Umständen nicht mehr mit den Kontrollen von „Durch New Zealand ISM eingeschränkt“ konform.

1. Wählen Sie **Alle Dienste** im linken Bereich aus. Suchen Sie nach **Blaupausen**, und wählen Sie die Option aus.

1. Wählen Sie links die Seite **Blaupausendefinitionen** aus. Verwenden Sie die Filter, um Ihre Kopie des Blaupausenbeispiels zu suchen, und wählen Sie es aus.

1. Wählen Sie oben auf der Seite die Option **Blaupause veröffentlichen** aus. Geben Sie auf der neuen Seite rechts für Ihre Kopie des Blaupausenbeispiels eine **Version** an. Diese Eigenschaft ist hilfreich, wenn Sie später Änderungen vornehmen. Geben Sie **Änderungshinweise** ein, z. B. „Erste mit dem Blaupausenbeispiel ‚Durch New Zealand ISM eingeschränkt‘ veröffentlichte Version“. Wählen Sie dann unten auf der Seite **Veröffentlichen** aus.

### <a name="assign-the-sample-copy"></a>Zuweisen der Kopie des Beispiels

Nachdem die Kopie des Blaupausenbeispiels erfolgreich **veröffentlicht** wurde, kann sie einem Abonnement innerhalb der Verwaltungsgruppe, in der sie gespeichert wurde, zugewiesen werden. In diesem Schritt werden Parameter angegeben, damit jede Bereitstellung der Kopie des Blaupausenbeispiels eindeutig ist.

1. Wählen Sie **Alle Dienste** im linken Bereich aus. Suchen Sie nach **Blaupausen**, und wählen Sie die Option aus.

1. Wählen Sie links die Seite **Blaupausendefinitionen** aus. Verwenden Sie die Filter, um Ihre Kopie des Blaupausenbeispiels zu suchen, und wählen Sie es aus.

1. Wählen Sie oben auf der Seite mit der Blaupausendefinition die Option **Blaupause zuweisen** aus.

1. Geben Sie die Parameterwerte für die Blaupausenzuweisung an:

   - Grundlagen

     - **Abonnements**: Wählen Sie mindestens eines der Abonnements in der Verwaltungsgruppe aus, in der Sie die Kopie des Blaupausenbeispiels gespeichert haben. Wenn Sie mehrere Abonnements auswählen, wird für jedes Abonnement eine Zuweisung mit den eingegebenen Parametern erstellt.
     - **Zuweisungsname:** Der Name wird basierend auf dem Namen der Blaupause vorab ausgefüllt.
       Sie können ihn nach Bedarf ändern oder unverändert übernehmen.
     - **Standort**: Wählen Sie eine Region aus, in der die verwaltete Identität erstellt werden soll. Azure Blueprint verwendet diese verwaltete Identität zum Bereitstellen aller Artefakte in der zugewiesenen Blaupause. Weitere Informationen finden Sie unter [Was sind verwaltete Identitäten für Azure-Ressourcen?](../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Version der Blaupausendefinition:** Wählen Sie eine **veröffentlichte** Version Ihrer Kopie des Blaupausenbeispiels aus.

   - Zuweisung sperren

     Wählen Sie die Blaupausensperreinstellung für die Umgebung aus. Weitere Informationen finden Sie unter [Grundlegendes zur Ressourcensperre in Azure Blueprint](../concepts/resource-locking.md).

   - Verwaltete Identität

     Behalten Sie die Standardeinstellung _Vom System zugewiesen_ für die verwaltete Identität bei.

   - Artefaktparameter

     Die in diesem Abschnitt definierten Parameter gelten für das Artefakt, unter dem sie definiert werden. Diese Parameter sind [dynamische Parameter](../concepts/parameters.md#dynamic-parameters), da sie während der Zuweisung der Blaupause definiert werden. Eine vollständige Liste der Artefaktparameter und die zugehörigen Beschreibungen finden Sie in der [Tabelle der Artefaktparameter](#artifact-parameters-table).

1. Nachdem Sie alle Parameter eingegeben haben, wählen Sie unten auf der Seite die Option **Zuweisen** aus. Die Blaupausenzuweisung wird erstellt, und die Artefaktbereitstellung wird gestartet. Die Bereitstellung dauert ungefähr eine Stunde. Um den Status der Bereitstellung zu überprüfen, öffnen Sie die Blaupausenzuweisung.

> [!WARNING]
> Der Azure Blueprints-Dienst und die integrierten Blaupausenbeispiele sind **kostenlos**. Azure-Ressourcen werden [nach Produkt abgerechnet](https://azure.microsoft.com/pricing/). Verwenden Sie den [Preisrechner](https://azure.microsoft.com/pricing/calculator/) zum Schätzen der Kosten für die Ausführung der Ressourcen, die mit diesem Blaupausenbeispiel bereitgestellt werden.

### <a name="artifact-parameters-table"></a>Tabelle der Artefaktparameter

Die folgende Tabelle enthält eine Aufstellung der Parameter des Blaupausenartefakts:

|Artefaktname|Artefakttyp|Parametername|BESCHREIBUNG|
|-|-|-|-|
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|Liste der Benutzer, die in der Windows-VM-Administratorengruppe enthalten sein müssen|Eine durch Semikolon getrennte Liste von Benutzern, die in der Gruppe der lokalen Administratoren enthalten sein sollen, z. B. Administrator; myUser1; myUser2|
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|Liste der Benutzer, die aus der Windows-VM-Administratorgruppe ausgeschlossen werden müssen|Eine durch Semikolon getrennte Liste von Benutzern, die nicht in der Gruppe der lokalen Administratoren enthalten sein sollen, z. B. Administrator; myUser1; myUser2|
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|Liste der Benutzer, die als einzige in der Windows-VM-Administratorengruppe enthalten sein dürfen|Eine durch Semikolon getrennte Liste aller erwarteten Mitglieder der lokalen Administratorengruppe. Beispiel: Administrator; meinBenutzer1; meinBenutzer2|
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|Log Analytics-Arbeitsbereichs-ID für VM-Agent-Berichterstellung|ID (GUID) des Log Analytics-Arbeitsbereichs, in dem VM-Agents Bericht erstatten sollen|
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|Auswirkung für Richtlinie: Web Application Firewall (WAF) muss für Azure Front Door Service aktiviert sein|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|Auswirkung für Richtlinie: Für SQL Server müssen die Einstellungen der Sicherheitsrisikobewertung eine E-Mail-Adresse für den Empfang von Überprüfungsberichten enthalten|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|Auswirkung für Richtlinie: Auf VMs mit Internetausrichtung müssen Empfehlungen zur adaptiven Netzwerkhärtung angewendet werden|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|Auswirkung für die Richtlinie: Ihrem Abonnement sollte mehr als ein Besitzer zugewiesen sein.|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|Auswirkung für die Richtlinie: Die Datenträgerverschlüsselung sollte auf virtuelle Computer angewendet werden.|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|Auswirkung für die Richtlinie: Remotedebuggen sollte für Funktions-Apps deaktiviert sein.|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|Auswirkung für Richtlinie: Web Application Firewall (WAF) muss den angegebenen Modus für Application Gateway verwenden|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|WAF-Modusanforderung für Application Gateway|Für den Application Gateway-Dienst muss der Verhinderungs- oder Erkennungsmodus aktiviert sein.|
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|Auswirkung für die Richtlinie: Transparent Data Encryption für SQL-Datenbanken sollte aktiviert werden.|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|Auswirkung für Richtlinie: Für SQL Managed Instance muss eine Sicherheitsrisikobewertung aktiviert sein|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|Optional: Liste benutzerdefinierter VM-Images mit unterstütztem Windows-Betriebssystem, die dem Bereich zusätzlich zu den Images im Katalog für die folgende Richtlinie hinzugefügt werden: „Bereitstellen: Aktivierung des Dependency-Agents auf Windows-VMs konfigurieren“|Weitere Informationen zur Gastkonfiguration finden Sie unter [https://aka.ms/gcpol](../../policy/concepts/guest-configuration.md).|
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|Auswirkung für die Richtlinie: Ein Azure Active Directory-Administrator sollte für SQL-Server-Instanzen bereitgestellt werden|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|Auswirkung für Richtlinie: Für Azure Cache for Redis dürfen nur sichere Verbindungen aktiviert sein|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|Auswirkung für die Richtlinie: Die Endpoint Protection-Lösung sollte für VM-Skalierungsgruppen installiert sein.|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|Mit Arc verbundene Server beim Auswerten der Richtlinie einschließen: Windows-Computer überwachen, auf denen angegebene Mitglieder in der Gruppe „Administratoren“ fehlen|Durch Auswahl von TRUE stimmen Sie einer monatlichen Abrechnung pro Computer mit Arc-Verbindung zu.|
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|Optional: Liste benutzerdefinierter VM-Images mit unterstütztem Windows-Betriebssystem, die dem Bereich zusätzlich zu den Images im Katalog für die folgende Richtlinie hinzugefügt werden: „[Vorschau]: Für die aufgelisteten VM-Images muss der Log Analytics-Agent aktiviert werden“|Weitere Informationen zur Gastkonfiguration finden Sie unter [https://aka.ms/gcpol](../../policy/concepts/guest-configuration.md).|
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|Optional: Liste benutzerdefinierter VM-Images mit unterstütztem Linux-Betriebssystem, die dem Bereich zusätzlich zu den Images im Katalog für die folgende Richtlinie hinzugefügt werden: „[Vorschau]: Für die aufgelisteten VM-Images muss der Log Analytics-Agent aktiviert werden“|Weitere Informationen zur Gastkonfiguration finden Sie unter [https://aka.ms/gcpol](../../policy/concepts/guest-configuration.md).|
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|Auswirkung für Richtlinie: Netzwerkzugriff auf Speicherkonten einschränken|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|Optional: Liste benutzerdefinierter VM-Images mit unterstütztem Windows-Betriebssystem, die dem Bereich zusätzlich zu den Images im Katalog für die folgende Richtlinie hinzugefügt werden: „Bereitstellen: Aktivierung des Dependency-Agents in Windows-VM-Skalierungsgruppen konfigurieren“|Weitere Informationen zur Gastkonfiguration finden Sie unter [https://aka.ms/gcpol](../../policy/concepts/guest-configuration.md).|
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|Auswirkung für die Richtlinie: Sicherheitsrisiken in der Sicherheitskonfiguration von VM-Skalierungsgruppen sollten beseitigt werden.|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|Mit Arc verbundene Server beim Auswerten der Richtlinie einschließen: Windows-Computer mit zusätzlichen Konten in der Gruppe „Administratoren“ überwachen|Durch Auswahl von TRUE stimmen Sie einer monatlichen Abrechnung pro Computer mit Arc-Verbindung zu.|
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|Auswirkung für die Richtlinie: Sichere Übertragung in Speicherkonten sollte aktiviert werden.|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|Auswirkung für Richtlinie: Web Application Firewall (WAF) muss den angegebenen Modus für Azure Front Door Service verwenden|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|WAF-Modusanforderung für Azure Front Door-Dienst|Für den Azure Front Door-Dienst muss der Verhinderungs- oder Erkennungsmodus aktiviert sein.|
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|Auswirkung für Richtlinie: Adaptive Anwendungssteuerung zum Definieren sicherer Anwendungen muss auf Computern aktiviert sein|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|Auswirkung für die Richtlinie: Maximal 3 Besitzer sollten für Ihr Abonnement festgelegt sein.|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|Auswirkung für Richtlinie: [Vorschau]: Der öffentliche Zugriff auf Speicherkonten muss untersagt sein|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|Auswirkung für Richtlinie: Auf VMs muss eine Lösung zur Sicherheitsrisikobewertung installiert werden|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|Auswirkung für Richtlinie: Web Application Firewall (WAF) muss für Application Gateway aktiviert sein|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|Auswirkung für die Richtlinie: CORS sollte nicht jeder Ressource Zugriff auf Ihre Webanwendungen erteilen.|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|Mit Arc verbundene Server bei der Richtlinienauswertung einbeziehen: Windows-Webserver überwachen, die keine sicheren Kommunikationsprotokolle verwenden|Durch Auswahl von TRUE stimmen Sie einer monatlichen Abrechnung pro Computer mit Arc-Verbindung zu.|
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|TLS-Mindestversion für Windows-Webserver|Windows-Webserver mit niedrigeren TLS-Versionen werden als nicht konform bewertet.|
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|Optional: Liste benutzerdefinierter VM-Images mit unterstütztem Linux-Betriebssystem, die dem Bereich zusätzlich zu den Images im Katalog für die folgende Richtlinie hinzugefügt werden: „Für die aufgelisteten VM-Images muss der Log Analytics-Agent in VM-Skalierungsgruppen aktiviert werden“|Weitere Informationen zur Gastkonfiguration finden Sie unter [https://aka.ms/gcpol](../../policy/concepts/guest-configuration.md).|
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|Optional: Liste benutzerdefinierter VM-Images mit unterstütztem Windows-Betriebssystem, die dem Bereich zusätzlich zu den Images im Katalog für die folgende Richtlinie hinzugefügt werden: „Für die aufgelisteten VM-Images muss der Log Analytics-Agent in VM-Skalierungsgruppen aktiviert werden“|Weitere Informationen zur Gastkonfiguration finden Sie unter [https://aka.ms/gcpol](../../policy/concepts/guest-configuration.md).|
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|Auswirkung für die Richtlinie: Externe Konten mit Schreibberechtigungen sollten aus Ihrem Abonnement entfernt werden.|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|Mit Arc verbundene Server beim Auswerten der Richtlinie einschließen: Windows-Computer überwachen, auf denen angegebene Mitglieder in der Gruppe „Administratoren“ vorhanden sind|Durch Auswahl von TRUE stimmen Sie einer monatlichen Abrechnung pro Computer mit Arc-Verbindung zu.|
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|Auswirkung für die Richtlinie: Veraltete Konten sollten aus Ihrem Abonnement entfernt werden.|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|Auswirkung für die Richtlinie: Zugriff auf Funktions-App nur über HTTPS gestatten|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|Auswirkung für Richtlinie: Azure-Abonnements benötigen ein Protokollprofil für das Aktivitätsprotokoll|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|Liste von Ressourcentypen, für die Diagnoseprotokolle aktiviert werden sollen||
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|Auswirkung für die Richtlinie: Systemupdates sollten auf Ihren Computern installiert sein.|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|Auswirkung für die Richtlinie: In Ihrer API-App sollte die neueste TLS-Version verwendet werden.|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|Auswirkung für die Richtlinie: MFA sollte für Konten mit Schreibrechten für Ihr Abonnement aktiviert werden|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|Auswirkung für die Richtlinie: Die Microsoft IaaSAntimalware-Erweiterung muss auf Windows-Servern bereitgestellt werden|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|Auswirkung für die Richtlinie: Zugriff auf Webanwendung nur über HTTPS gestatten|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|Auswirkung für Richtlinie: Azure DDoS Protection Standard muss aktiviert sein|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|Auswirkung für die Richtlinie: MFA sollte für Konten mit Besitzerberechtigungen in Ihrem Abonnement aktiviert sein.|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|Auswirkung für die Richtlinie: Advanced Data Security muss für Ihre SQL-Server aktiviert werden.|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|Auswirkung für Richtlinie: Für SQL Managed Instance muss Advanced Data Security aktiviert sein|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|Auswirkung für die Richtlinie: Fehlenden Endpoint Protection-Schutz in Azure Security Center überwachen|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|Auswirkung für Richtlinie: Das Aktivitätsprotokoll muss mindestens ein Jahr lang aufbewahrt werden|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|Auswirkung für Richtlinie: Verwaltungsports virtueller Computer müssen mit der Just-In-Time-Netzwerkzugriffssteuerung geschützt werden|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|Auswirkung für die Richtlinie: Service Fabric-Cluster sollten nur Azure Active Directory für die Clientauthentifizierung verwenden.|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|Auswirkung für die Richtlinie: Auf API-Apps sollte nur über HTTPS zugegriffen werden können|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|Auswirkung für Richtlinie: Windows-Computer überwachen, auf denen Windows Defender Exploit Guard nicht aktiviert ist|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|Mit Arc verbundene Server bei der Richtlinienauswertung einbeziehen: Windows-Computer überwachen, auf denen Windows Defender Exploit Guard nicht aktiviert ist|Durch Auswahl von TRUE stimmen Sie einer monatlichen Abrechnung pro Computer mit Arc-Verbindung zu.|
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|Konformitätszustand für Windows-Computer, auf denen Microsoft Defender Exploit Guard nicht verfügbar ist|Windows Defender Exploit Guard ist erst ab Windows 10/Windows Server mit Update 1709 verfügbar. Wenn dieser Wert auf „Nicht konform“ festgelegt wird, werden Computer mit älteren Versionen, auf denen Windows Defender Exploit Guard nicht verfügbar ist (z. B. Windows Server 2012 R2), als nicht konform angezeigt. Wenn dieser Wert auf „Konform“ festgelegt wird, werden diese Computer als konform angezeigt.|
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|Auswirkung für die Richtlinie: Systemupdates für VM-Skalierungsgruppen sollten installiert werden.|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|Auswirkung für die Richtlinie: Remotedebuggen sollte für Webanwendungen deaktiviert sein.|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|Auswirkung für die Richtlinie: Sicherheitsrisiken in der Sicherheitskonfiguration für Ihre Computer sollten beseitigt werden.|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|Auswirkung für die Richtlinie: MFA sollte für Ihre Abonnementkonten mit Leseberechtigungen aktiviert sein|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|Auswirkung für die Richtlinie: Sicherheitsrisiken in Containersicherheitskonfigurationen sollten behoben werden.|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|Auswirkung für die Richtlinie: Remotedebuggen sollte für API-Apps deaktiviert sein.|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|Auswirkung für Richtlinie: Linux-Computer überwachen, die Remoteverbindungen über Konten ohne Kennwörter zulassen|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|Mit Arc verbundene Server beim Auswerten der Richtlinie einschließen: Linux-Computer überwachen, die Remoteverbindungen von Konten ohne Kennwörter zulassen|Durch Auswahl von TRUE stimmen Sie einer monatlichen Abrechnung pro Computer mit Arc-Verbindung zu.|
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|Auswirkung für die Richtlinie: Veraltete Konten mit Besitzerberechtigungen sollten aus Ihrem Abonnement entfernt werden.|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|Auswirkung für die Richtlinie: Für Ihre SQL Server-Instanzen muss eine Sicherheitsrisikobewertung aktiviert sein.|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|Auswirkung für die Richtlinie: In Ihrer Web-App sollte die neueste TLS-Version verwendet werden.|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|Auswirkung für Richtlinie: Windows-Computer müssen die Anforderungen für „Sicherheitseinstellungen: Kontorichtlinien“ erfüllen|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|Kennwortverlauf für lokale Windows-VM-Konten erzwingen|Hiermit werden Grenzwerte für die Wiederverwendung von Kennwörtern angegeben: Legen Sie fest, wie oft ein neues Kennwort für ein Benutzerkonto erstellt werden muss, bevor das Kennwort erneut verwendet werden kann.|
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|Mit Arc verbundene Server beim Auswerten der Richtlinie einschließen: Windows-Computer müssen die Anforderungen für „Sicherheitseinstellungen“ > „Kontorichtlinien“ erfüllen|Durch Auswahl von TRUE stimmen Sie einer monatlichen Abrechnung pro Computer mit Arc-Verbindung zu.|
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|Maximales Kennwortalter für lokale Windows-VM-Konten|Hiermit wird die Höchstanzahl von Tagen angegeben, die verstreichen kann, bevor das Kennwort für ein Benutzerkonto geändert werden muss. Der Wert wird in Form von zwei ganzen Zahlen angegeben, die durch ein Komma getrennt werden. Sie definieren einen Bereich, der beide Zahlen einschließt.|
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|Kennwortmindestalter für lokale Windows-VM-Konten|Hiermit wird die Mindestanzahl von Tagen angegeben, die verstreichen muss, bevor das Kennwort für ein Benutzerkonto geändert werden kann.|
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|Kennwortmindestlänge für lokale Windows-VM-Konten|Hiermit wird die Mindestanzahl von Zeichen angegeben, die ein Benutzerkontokennwort enthalten muss.|
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|Kennwort muss die Komplexitätsanforderungen für lokale Windows-VM-Konten erfüllen|Hiermit wird angegeben, ob ein Benutzerkontokennwort komplex sein muss. Ein komplexes Kennwort darf keinen Teil des Kontonamens oder des vollständigen Namens des Benutzers enthalten, muss mindestens 6 Zeichen lang sein und aus einer Kombination aus Großbuchstaben, Kleinbuchstaben, Ziffern und nicht alphabetischen Zeichen bestehen.|
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|Auswirkung für Richtlinie: VMs mit Internetzugang sollten mithilfe von Netzwerksicherheitsgruppen geschützt werden|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|Auswirkung für Richtlinie: Linux-Computer überwachen, die Konten ohne Kennwörter verwenden|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|Mit Arc verbundene Server beim Auswerten der Richtlinie einschließen: Linux-Computer mit Konten ohne Kennwörter überwachen|Durch Auswahl von TRUE stimmen Sie einer monatlichen Abrechnung pro Computer mit Arc-Verbindung zu.|
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|Auswirkung für die Richtlinie: Externe Konten mit Besitzerberechtigungen sollten aus Ihrem Abonnement entfernt werden.|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|Auswirkung für die Richtlinie: In Ihrer Funktions-App sollte die neueste TLS-Version verwendet werden.|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|Auswirkung für Richtlinie: [Vorschau]: Gesamten Internetdatenverkehr über Ihre bereitgestellte Azure Firewall-Instanz leiten|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|
|Durch New Zealand ISM eingeschränkt|Richtlinienzuweisung|Auswirkung für die Richtlinie: Sicherheitsrisiken in SQL-Datenbanken sollten beseitigt werden.|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md).|

## <a name="next-steps"></a>Nächste Schritte

Weitere Artikel zu Blaupausen und ihrer Nutzung:

- Erfahren Sie mehr über den [Lebenszyklus von Blaupausen](../concepts/lifecycle.md).
- Machen Sie sich mit der Verwendung [statischer und dynamischer Parameter](../concepts/parameters.md) vertraut.
- Erfahren Sie, wie Sie die [Abfolge von Blaupausen](../concepts/sequencing-order.md) anpassen können.
- Erfahren Sie, wie Sie [Ressourcen in Blaupausen sperren](../concepts/resource-locking.md) können.
- Lernen Sie, wie Sie [vorhandene Zuweisungen aktualisieren](../how-to/update-existing-assignments.md).

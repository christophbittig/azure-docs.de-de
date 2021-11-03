---
title: Zertifikatverwaltung in einem Service Fabric-Cluster
description: Erfahren Sie mehr zum Verwalten von Zertifikaten in einem Service Fabric-Cluster, der mit X.509-Zertifikaten geschützt wird.
ms.topic: conceptual
ms.date: 04/10/2020
ms.custom: sfrev, devx-track-azurepowershell
ms.openlocfilehash: 768fe83a0ce4bc0507f21b72e1c917e60c7ec971
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131075123"
---
# <a name="certificate-management-in-service-fabric-clusters"></a>Zertifikatverwaltung in Service Fabric-Clustern

Dieser Artikel befasst sich mit den Verwaltungsaspekten von Zertifikaten, die zum Schutz der Kommunikation in Azure Service Fabric-Clustern verwendet werden, und ergänzt die Einführung in die [Sicherheit von Service Fabric-Clustern](service-fabric-cluster-security.md) sowie die Erläuterung zur [auf X.509-Zertifikaten basierenden Authentifizierung in Service Fabric](cluster-security-certificates.md). Wir gehen davon aus, dass Leser dieses Artikels mit grundlegenden Sicherheitskonzepten und auch mit den Steuerungsmöglichkeiten vertraut sind, die Service Fabric zur Verfügung stellt, um die Sicherheit eines Clusters zu konfigurieren.  

In diesem Artikel behandelte Themen:

* Was genau bedeutet „Zertifikatverwaltung“?
* An der Zertifikatverwaltung beteiligte Rollen und Entitäten
* Der Weg eines Zertifikats
* Ausführliche Erörterung eines Beispiels
* Problembehandlung und häufig gestellte Fragen (FAQ)

Doch zunächst ein Vorbehalt: Dieser Artikel versucht, die theoretische Seite an praktische Beispiele zu koppeln, die spezifische Angaben zu Diensten, Technologien usw. erfordern. Da ein beträchtlicher Teil der Zielgruppe Microsoft-intern ist, verweisen wir auf Dienste, Technologien und Produkte, die spezifisch für Microsoft Azure sind. Wenn die Microsoft-spezifischen Angaben in Ihrem Fall nicht zutreffen, können Sie im Abschnitt „Kommentare“ um eine Klarstellung oder Anleitung bitten.

## <a name="defining-certificate-management"></a>Definieren der Zertifikatverwaltung
Wie wir im [Begleitartikel](cluster-security-certificates.md) erfahren haben, ist ein Zertifikat ein kryptografisches Objekt, das im Wesentlichen ein asymmetrisches Schlüsselpaar an Attribute bindet, die die von ihm dargestellte Entität beschreiben. Es ist jedoch auch ein kurzlebiges Objekt, da seine Gültigkeitsdauer begrenzt und anfällig für Gefährdungen ist. Eine versehentliche Preisgabe oder ein erfolgreiches Ausnutzen eines Exploits kann ein Zertifikat unter Sicherheitsaspekten unbrauchbar machen. Dies impliziert die Notwendigkeit, Zertifikate zu ändern, entweder regelmäßig oder als Reaktion auf einen Sicherheitsvorfall. Ein weiterer Aspekt der Verwaltung (und ein ganz eigenständiges Thema) ist der Schutz von privaten Schlüsseln von Zertifikaten bzw. von Geheimnissen, die die Identitäten der an Bezug und Bereitstellung von Zertifikaten beteiligten Entitäten schützen. Wir bezeichnen die Prozesse und Verfahren zum Bezug von Zertifikaten und zu ihrem sicheren (und geschützten) Transport dorthin, wo sie benötigt werden, als „Zertifikatverwaltung“. Einige der Verwaltungsvorgänge, wie z. B. Registrierung, Festlegung von Richtlinien und Autorisierungskontrollen, werden in diesem Artikel nicht behandelt. Andere wiederum, wie z. B. Bereitstellung, Verlängerung, Neuvergabe oder Widerruf, stehen nur am Rande mit der Service Fabric in Verbindung. Dennoch werden wir uns hier bis zu einem gewissen Grad mit ihnen befassen, da das Verständnis dieser Vorgänge dazu beitragen kann, den eigenen Cluster ordnungsgemäß zu schützen. 

Ziel ist es, die Zertifikatverwaltung so weit wie möglich zu automatisieren, um eine unterbrechungsfreie Verfügbarkeit des Clusters zu gewährleisten und Sicherheitsgarantien unter Berücksichtigung der Tatsache zu bieten, dass der Prozess ohne Benutzereingriffe erfolgt. Dieses Ziel ist derzeit in Azure Service Fabric-Clustern erreichbar. Im weiteren Verlauf des Artikels wird zunächst die Zertifikatverwaltung detailliert erörtert. Später wird der Schwerpunkt auf das Aktivieren eines automatischen Rollovers gelegt.

Die folgenden Themen werden behandelt:
  - Annahmen in Bezug auf die Trennung der Zuweisungen zwischen Besitzer und Plattform im Zusammenhang der Verwaltung von Zertifikaten
  - Der lange Weg von Zertifikaten von der Ausstellung bis zur Nutzung
  - Rotation von Zertifikaten: warum, wie und wann
  - Was kann möglicherweise schief gehen?

Aspekte wie die Absicherung/Verwaltung von Domänennamen, die Registrierung in Zertifikaten oder die Einrichtung von Autorisierungskontrollen zum Erzwingen der Zertifikatsausstellung werden in diesem Artikel nicht behandelt. Wenden Sie sich an die Registrierungsstelle Ihres bevorzugten PKI-Infrastrukturdiensts (Public Key-Infrastruktur). Interne Microsoft-Consumer: Wenden Sie sich an Azure Security.

## <a name="roles-and-entities-involved-in-certificate-management"></a>An der Zertifikatverwaltung beteiligte Rollen und Entitäten
Der Sicherheitsansatz in einem Service Fabric-Cluster ist ein Fall von „Deklarierung durch Clusterbesitzer, Erzwingung durch Service Fabric-Laufzeit“. Damit meinen wir, dass praktisch keine der Zertifikate, Schlüssel oder anderen Anmeldeinformationen von Identitäten, die am Betrieb eines Clusters beteiligt sind, vom Dienst selbst stammen, denn sie werden alle vom Besitzer des Clusters deklariert. Darüber hinaus ist der Clusterbesitzer auch dafür verantwortlich, die Zertifikate im Cluster bereitzustellen, sie bei Bedarf zu verlängern und die Sicherheit der Zertifikate jederzeit zu gewährleisten. Genauer gesagt muss der Clusterbesitzer sicherstellen, dass:
  - Zertifikate, die im Abschnitt NodeType des Clustermanifests deklariert sind, gemäß den [Präsentationsregeln](cluster-security-certificates.md#presentation-rules) auf jedem Knoten dieses Typs gefunden werden können.
  - oben deklarierte Zertifikate einschließlich ihrer entsprechenden privaten Schlüssel installiert werden.
  - in den Präsentationsregeln deklarierte Zertifikate die [Validierungsregeln](cluster-security-certificates.md#validation-rules) einhalten müssen. 

Service Fabric ist seinerseits für Folgendes verantwortlich:
  - Ermitteln/Auffinden von Zertifikaten, die den Deklarationen in der Clusterdefinition entsprechen  
  - Gewähren von Zugriff nach Bedarf auf die entsprechenden privaten Schlüssel für von Service Fabric kontrollierte Entitäten
  - Validieren von Zertifikaten in strikter Übereinstimmung mit bewährten Sicherheitsverfahren und der Clusterdefinition
  - Auslösen von Warnungen bei bevorstehendem Ablauf von Zertifikaten oder bei Nichtdurchführung der grundlegenden Schritte zur Validierung von Zertifikaten
  - Validieren (bis zu einem gewissen Grad), dass die zertifikatbezogenen Aspekte der Clusterdefinition von der zugrunde liegenden Konfiguration der Hosts erfüllt werden 

Daraus folgt, dass die Last der Zertifikatverwaltung (als aktive Vorgänge) allein dem Clusterbesitzer zufällt. In den folgenden Abschnitten werfen wir einen genaueren Blick auf die einzelnen Verwaltungsvorgänge sowie die verfügbaren Mechanismen und ihre Auswirkungen auf den Cluster.

## <a name="the-journey-of-a-certificate"></a>Der Weg eines Zertifikats
Lassen Sie uns schnell noch einmal den Weg eines Zertifikats von der Ausstellung bis zur Nutzung im Kontext eines Service Fabric-Clusters verfolgen:

  1. Ein Domänenbesitzer registriert bei der Registrierungsstelle einer PKI eine Domäne oder einen Antragsteller, die bzw. den er Zertifikaten zuordnen möchte. Die Zertifikate stellen wiederum Nachweise des Besitzes an der genannten Domäne oder dem Antragsteller dar.
  2. Der Domänenbesitzer benennt in der Registrierungsstelle auch die Identitäten der autorisierten anfordernden Personen. Dies sind Entitäten, die berechtigt sind, die Registrierung von Zertifikaten bei der angegebenen Domäne oder dem angegebenen Antragsteller anzufordern. In Microsoft Azure ist Azure Active Directory der standardmäßige Identitätsanbieter. Autorisierte anfordernde Personen werden durch ihre entsprechende AAD-Identität (oder über Sicherheitsgruppen) bestimmt.
  3. Eine autorisierte anfordernde Person registriert sich dann bei einem Zertifikat über einen Dienst zur Verwaltung von Geheimnissen. In Microsoft Azure heißt dieser Dienst Azure Key Vault, der Geheimnisse/Zertifikate sicher speichert und deren Abruf durch autorisierte Entitäten ermöglicht. Azure Key Vault dient auch zum Verlängern bzw. Neuvergeben des Zertifikats gemäß Konfiguration in der zugehörigen Zertifikatrichtlinie. (Azure Key Vault nutzt Azure Active Directory als Identitätsanbieter.)
  4. Ein autorisierter Abrufer, den wir als „Bereitstellungs-Agent“ bezeichnen, ruft das Zertifikat samt seinem privaten Schlüssel aus dem Tresor ab und installiert es auf den Computern, die den Cluster hosten.
  5. Der Service Fabric-Dienst (der auf jedem Knoten mit erhöhten Rechten ausgeführt wird) gewährt berechtigten Service Fabric-Entitäten Zugriff auf das Zertifikat. Diese werden von lokalen Gruppen bestimmt und in ServiceFabricAdministrators (Service Fabric-Administratoren) und ServiceFabricAllowedUsers (Zulässige Service Fabric-Benutzer) aufgeteilt.
  6. Die Service Fabric-Laufzeit greift auf das Zertifikat zu und nutzt es, um einen Verbund einzurichten oder um sich bei eingehenden Anforderungen von autorisierten Clients zu authentifizieren.
  7. Der Bereitstellungs-Agent überwacht das Tresorzertifikat und löst den Bereitstellungsflow aus, wenn er eine Verlängerung feststellt. Anschließend aktualisiert der Clusterbesitzer bei Bedarf die Clusterdefinition, um die Absicht zum Rollover des Zertifikats anzugeben.
  8. Der Bereitstellungs-Agent oder Clusterbesitzer ist auch für die Bereinigung/Löschung nicht verwendeter Zertifikate verantwortlich.
  
Für unsere Zwecke sind die ersten beiden Schritte in der obigen Abfolge weitgehend ohne Bezug zueinander. Die einzige Verbindung besteht darin, dass der allgemeine Name des Antragstellers des Zertifikats der in der Clusterdefinition deklarierte DNS-Name ist.

Diese Schritte sind nachstehend dargestellt. Beachten Sie die Unterschiede bei der Bereitstellung von Zertifikaten, die per Fingerabdruck bzw. allgemeinem Namen deklariert werden.

*Abb. 1* Ausstellungs- und Bereitstellungsflow von per Fingerabdruck deklarierten Zertifikaten.
![Bereitstellen von per Fingerabdruck deklarierten Zertifikaten][Image1]

*Abb. 2* Ausstellungs- und Bereitstellungsflow von per allgemeinem Namen des Antragstellers deklarierten Zertifikaten.
![Bereitstellen von per allgemeinem Namen des Antragstellers deklarierten Zertifikaten][Image2]

### <a name="certificate-enrollment"></a>Zertifikatregistrierung
Dieses Thema wird in der Key Vault-[Dokumentation](../key-vault/certificates/create-certificate.md) ausführlich behandelt. Wir fügen hier aus Gründen der Übersichtlichkeit und zur leichteren Bezugnahme eine Inhaltsangabe ein. Um mit Azure als Kontext fortzufahren und Azure Key Vault als Dienst zur Verwaltung von Geheimnissen zu nutzen, muss eine autorisierte, ein Zertifikat anfordernde Person mindestens die vom Tresorbesitzer erteilten Berechtigungen zur Zertifikatverwaltung für den Tresor haben. Die anfordernde Person registriert sich dann wie folgt in einem Zertifikat:
    - Sie erstellt eine Zertifikatrichtlinie in Azure Key Vault, die die Domäne/den Antragsteller des Zertifikats, den gewünschten Aussteller, Schlüsseltyp und -länge, die beabsichtigte Schlüsselverwendung und mehr angibt. Weitere Informationen finden Sie unter [Zertifikate in Azure Key Vault](../key-vault/certificates/certificate-scenarios.md). 
    - Sie erstellt ein Zertifikat im gleichen Tresor mit der oben angegebenen Richtlinie. Dies wiederum generiert ein Schlüsselpaar als Tresorobjekte und eine Zertifikatsignierungsanforderung, die mit dem privaten Schlüssel signiert wird und dann zum Signieren an den benannten Aussteller weitergeleitet wird.
    - Sobald der Aussteller (die Zertifizierungsstelle) mit dem signierten Zertifikat antwortet, wird das Ergebnis im Tresor zusammengeführt, woraufhin das Zertifikat für die folgenden Vorgänge zur Verfügung steht:
      - Unter {vaultUri}/certificates/{name}: das Zertifikat einschließlich öffentlichem Schlüssel und Metadaten
      - Unter {vaultUri}/keys/{name}: der für kryptografische Vorgänge verfügbare private Schlüssel des Zertifikats (umbrechen/Umbruch aufheben, signieren/überprüfen)
      - Unter {vaultUri}/secrets/{name}: das Zertifikat einschließlich des privaten Schlüssels, herunterladbar als ungeschützte PFX- oder PEM-Datei  
    Zur Erinnerung: Ein Tresorzertifikat ist eigentlich eine chronologische Reihe von Zertifikatsinstanzen mit einer gemeinsamen Richtlinie. Zertifikatversionen werden entsprechend der Attribute für Gültigkeitsdauer und Verlängerung der Richtlinie erstellt. Es wird nachdrücklich empfohlen, dass Tresorzertifikate keine Antragsteller oder Domänen/DNS-Namen gemeinsam haben. Es kann in einem Cluster problematisch sein, Zertifikatinstanzen aus verschiedenen Tresorzertifikaten mit identischen Antragstellern, aber deutlich unterschiedlichen anderen Attributen wie Aussteller, Schlüsselverwendungen usw. bereitzustellen.

An dieser Stelle befindet sich ein Zertifikat im Tresor, das zur Nutzung bereit steht. Weiter zur:

### <a name="certificate-provisioning"></a>Zertifikatbereitstellung
Wir haben einen „Bereitstellungs-Agent“ erwähnt, bei dem es sich um die Entität handelt, die das Zertifikat einschließlich seines privaten Schlüssels aus dem Tresor abruft und auf jedem der Hosts des Clusters installiert. (Denken Sie daran, dass Service Fabric keine Zertifikate bereitstellt.) In unserem Kontext wird der Cluster in einer Sammlung von Azure-VMs und/oder VM-Skalierungsgruppen gehostet. In Azure kann die Bereitstellung eines Zertifikats aus einem Tresor für eine VM/VMSS (VM-Skalierungsgruppe) mit den folgenden Mechanismen erreicht werden, wobei wie weiter oben angenommen wird, dass dem Bereitstellungs-Agent zuvor vom Tresorbesitzer Abrufberechtigungen für den Tresor erteilt wurden: 
  - Ad-hoc: Ein Bediener ruft das Zertifikat aus dem Tresor (im Format PFX/PKCS #12 oder PEM) ab und installiert es auf jedem Knoten.
  - Als Geheimnis einer VM-Skalierungsgruppe während der Bereitstellung: Der Compute-Dienst ruft unter Verwendung seiner Erstanbieteridentität im Namen des Bedieners das Zertifikat aus einem für die Vorlagenbereitstellung aktivierten Tresor ab und installiert es auf jedem Knoten der VM-Skalierungsgruppe ([auf diese Weise](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.yml)). Beachten Sie, dass dies nur die Bereitstellung von Geheimnissen mit Versionsangabe erlaubt.
  - Mithilfe der [Key Vault-VM-Erweiterung](../virtual-machines/extensions/key-vault-windows.md). Dies ermöglicht die Bereitstellung von Zertifikaten unter Verwendung versionsloser Deklarationen bei regelmäßiger Aktualisierung der überwachten Zertifikate. In diesem Fall wird erwartet, dass die VM/VMSS über eine [verwaltete Identität](../virtual-machines/security-policy.md#managed-identities-for-azure-resources) verfügt, d. h. eine Identität, der Zugriff auf die Tresore mit den überwachten Zertifikaten gewährt wurde.

Der Ad-hoc-Mechanismus wird aus mehreren Gründen, die von der Sicherheit bis zur Verfügbarkeit reichen, nicht empfohlen und soll hier nicht weiter erörtert werden. Weitere Einzelheiten finden Sie unter [Zertifikate in VM-Skalierungsgruppen](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.yml).

Die VMSS-/Compute-basierte Bereitstellung bietet Vorteile in Bezug auf Sicherheit und Verfügbarkeit, bringt aber auch Einschränkungen mit sich. Sie erfordert von vornherein die Deklaration von Zertifikaten als Geheimnisse mit Versionsangabe, wodurch sie nur für Cluster geeignet ist, die mit per Fingerabdruck deklarierten Zertifikaten abgesichert sind. Im Gegensatz dazu wird bei der auf der Key Vault-VM-Erweiterung basierenden Bereitstellung immer die neueste Version jedes überwachten Zertifikats installiert. Dadurch eignet sie sich nur für Cluster, die mit Zertifikaten abgesichert sind, die mit dem allgemeinen Namen des Antragstellers deklariert wurden. Zur Hervorhebung: Verwenden Sie keinen Bereitstellungsmechanismus mit automatischer Aktualisierung (wie z. B. die Key Vault-VM-Erweiterung) für Zertifikate, die nach Instanz (d. h. per Fingerabdruck) deklariert wurden. Das Risiko des Verlusts der Verfügbarkeit ist beträchtlich.

Es gibt möglicherweise noch weitere Bereitstellungsmechanismen. Die oben genannten werden derzeit für Azure Service Fabric-Cluster akzeptiert.

### <a name="certificate-consumption-and-monitoring"></a>Nutzung und Überwachung von Zertifikaten
Wie bereits erwähnt, ist die Service Fabric-Laufzeit für das Auffinden und Nutzen der in der Clusterdefinition deklarierten Zertifikate verantwortlich. Im Artikel zur [zertifikatbasierten Authentifizierung](cluster-security-certificates.md) wurde ausführlich erläutert, wie Service Fabric die Präsentations- bzw. Validierungsregeln implementiert, auf die hier nicht noch einmal eingegangen werden soll. Wir werden uns mit dem Zugriff und der Berechtigungserteilung sowie mit der Überwachung befassen.

Rufen Sie sich in Erinnerung, dass Zertifikate in Service Fabric für eine Vielzahl von Zwecken verwendet werden – von der gegenseitigen Authentifizierung auf Verbundebene bis zur TLS-Authentifizierung der Verwaltungsendpunkte. Dies erfordert, dass verschiedene Komponenten oder Systemdienste Zugriff auf den privaten Schlüssel des Zertifikats haben. Die Service Fabric-Laufzeit durchsucht regelmäßig den Zertifikatspeicher nach Übereinstimmungen mit jeder der bekannten Präsentationsregeln. Für jedes der übereinstimmenden Zertifikate ist der entsprechende private Schlüssel vorhanden. Seine DACL (Discretionary Access Control List, besitzerverwaltete Zugriffssteuerungsliste) wird so aktualisiert, dass sie Berechtigungen – in der Regel „Lesen“ und „Ausführen“ – enthält, die der jeweiligen Identität gewährt werden, die diese benötigt. (Dieser Prozess wird informell als „ACLing“ [Hinzufügen zur ACL] bezeichnet.) Der Prozess läuft mit einer Taktfrequenz von 1 Minute und betrifft auch Zertifikate für „Anwendungen“, z. B. solche, die zur Verschlüsselung von Einstellungen oder als Endpunktzertifikate zum Einsatz kommen. „ACLing“ richtet sich nach den Präsentationsregeln. Daher ist es wichtig zu bedenken, dass auf per Fingerabdruck deklarierte Zertifikate, die ohne die anschließende Aktualisierung der Clusterkonfiguration automatisch aktualisiert werden, nicht zugegriffen werden kann.    

### <a name="certificate-rotation"></a>Rotation für Zertifikate
Randbemerkung: IETF [RFC 3647](https://tools.ietf.org/html/rfc3647) definiert [Verlängerung](https://tools.ietf.org/html/rfc3647#section-4.4.6) formell als die Ausstellung eines Zertifikats mit den gleichen Attributen wie das zu ersetzende Zertifikat. Aussteller, der öffentliche Schlüssel und Informationen zum Antragsteller bleiben erhalten. Die [Neuvergabe](https://tools.ietf.org/html/rfc3647#section-4.4.7) ist die Ausstellung eines Zertifikats mit einem neuen Schlüsselpaar ohne Einschränkungen der Möglichkeit, den Aussteller zu ändern. Da die Unterscheidung wichtig sein kann (denken Sie an Zertifikate, die per allgemeinem Namen des Antragstellers mit Anheftung an den Aussteller deklariert werden), entscheiden wir uns für den neutralen Begriff „Rotation“, um beide Szenarien abzudecken. (Denken Sie daran, dass sich der Begriff „Verlängerung“ bei informeller Verwendung in der Tat auf eine Neuvergabe bezieht). 

Wir haben bereits festgestellt, dass Azure Key Vault die automatische Rotation von Zertifikaten unterstützt. Die zugehörige Zertifikatrichtlinie legt den Zeitpunkt fest, an dem das Zertifikat im Tresor rotiert wird, entweder anhand von Tagen vor Ablauf oder als Prozentsatz der Gesamtgültigkeitsdauer. Der Bereitstellungs-Agent muss nach diesem Zeitpunkt und vor dem Ablauf des nun vorherigen Zertifikats aufgerufen werden, um dieses neue Zertifikat an alle Knoten im Cluster zu verteilen. Service Fabric hilft dabei, indem Integritätswarnungen ausgelöst werden, wenn das Ablaufdatum eines Zertifikats (das derzeit im Cluster verwendet wird) vor einem vorgegebenen Intervall eintritt. Ein automatischer Bereitstellungs-Agent (d. h. die Key Vault-VM-Erweiterung), der so konfiguriert ist, dass er das Tresorzertifikat überwacht, fragt den Tresor in regelmäßigen Abständen ab, erkennt die Rotation, ruft das neue Zertifikat ab und installiert es. Wenn die Bereitstellung über das VM/VMSS-Feature „Geheimnisse“ erfolgt, muss ein autorisierter Bediener die VM/VMSS mit dem Key Vault-URI mit Versionsangabe aktualisieren, der dem neuen Zertifikat entspricht.

In beiden Fällen wird das rotierte Zertifikat nun allen Knoten zur Verfügung gestellt. Außerdem haben wir den Mechanismus beschrieben, den Service Fabric zur Erkennung von Rotationen verwendet. Lassen Sie uns untersuchen, was als Nächstes geschieht, wenn die Rotation auf ein Clusterzertifikat angewendet wird, das anhand des allgemeinen Namens des Antragstellers deklariert wurde.
  - Bei neuen Verbindungen innerhalb des Clusters sowie mit dem Cluster findet und wählt die Service Fabric-Laufzeit das passende Zertifikat, das zuletzt ausgestellt wurde (größter Wert der Eigenschaft „NotBefore“). Beachten Sie, dass dies eine Änderung gegenüber früheren Versionen der Service Fabric-Laufzeit ist.
  - Bestehende Verbindungen bleiben bestehen bzw. können natürlich ablaufen oder anderweitig beendet werden. Ein interner Handler wird benachrichtigt, dass es eine neue Übereinstimmung gibt.

> [!NOTE] 
> Vor Version 7.2.445 (7.2 CU4) hat Service Fabric das Zertifikat mit dem spätesten Ablaufdatum ausgewählt (das Zertifikat mit dem spätesten Wert der Eigenschaft „NotAfter“).

Daraus ergeben sich die folgenden wichtigen Beobachtungen:
  - Das Verlängerungszertifikat wird ggf. ignoriert, wenn sein Ablaufdatum vor dem des derzeit verwendeten Zertifikats liegt.
  - Die Verfügbarkeit des Clusters oder der gehosteten Anwendungen hat Vorrang vor der Anweisung, das Zertifikat zu rotieren. Der Cluster wird schließlich auf das neue Zertifikat umgestellt, jedoch ohne zeitliche Garantien. Daraus folgt:
  - Es mag für einen Beobachter nicht sofort erkennbar sein, dass das rotierte Zertifikat seinen Vorgänger vollständig ersetzt hat. Die einzige Möglichkeit, dies sicherzustellen, besteht (bei Clusterzertifikaten) darin, die Hostcomputer neu zu starten. Beachten Sie, dass es nicht ausreicht, die Service Fabric-Knoten neu zu starten, da Komponenten im Kernelmodus, die Leaseverbindungen in einem Cluster bilden, davon nicht betroffen sind. Beachten Sie auch, dass ein Neustart der VM/VMSS zu einem vorübergehenden Verlust von Verfügbarkeit führen kann. (Bei Anwendungszertifikaten genügt es, nur die jeweiligen Anwendungsinstanzen neu zu starten.)
  - Die Einführung eines neu vergebenen Zertifikats, das die Validierungsregeln nicht erfüllt, kann den Cluster praktisch unbrauchbar machen. Das häufigste Beispiel hierfür ist der Fall eines unerwarteten Ausstellers. Die Clusterzertifikate werden per allgemeinem Namen des Antragstellers mit Anheftung an den Aussteller deklariert, aber das rotierte Zertifikat wurde von einem neuen bzw. nicht deklarierten Aussteller ausgestellt.     

### <a name="certificate-cleanup"></a>Zertifikatbereinigung
Gegenwärtig gibt es in Azure keine Vorkehrungen für das explizite Entfernen von Zertifikaten. Es ist oft eine nicht ganz einfache Aufgabe festzustellen, ob ein bestimmtes Zertifikat zu einem bestimmten Zeitpunkt verwendet wird oder nicht. Dies ist bei Anwendungszertifikaten schwieriger als bei Clusterzertifikaten. Da Service Fabric selbst nicht der Bereitstellungs-Agent ist, wird ein vom Benutzer deklariertes Zertifikat unter keinen Umständen gelöscht. Für die Standardbereitstellungsmechanismen gilt Folgendes:
  - Als VM/VMSS-Geheimnisse deklarierte Zertifikate werden bereitgestellt, solange auf sie in der VM/VMSS-Definition verwiesen wird und sie aus dem Tresor abgerufen werden können (das Löschen eines Tresorgeheimnisses/Zertifikats schlägt bei nachfolgenden VM/VMSS-Bereitstellungen fehl. Gleichermaßen führt die Deaktivierung einer Geheimnisversion im Tresor zu fehlgeschlagenen VM/VMSS-Bereitstellungen, die auf diese Geheimnisversion verweisen.)
  - Vorherige Versionen von Zertifikaten, die über die Key Vault-VM-Erweiterung bereitgestellt wurden, können ggf. auf einem VM/VMSS-Knoten vorhanden sein oder auch nicht. Der Agent ruft nur die aktuelle Version ab und installiert sie, entfernt aber keine Zertifikate. Beim Reimaging eines Knotens (das normalerweise monatlich erfolgt) wird der Zertifikatspeicher auf den Inhalt des Betriebssystemimages zurückgesetzt, sodass frühere Versionen implizit entfernt werden. Berücksichtigen Sie jedoch, dass das Aufskalieren einer VM-Skalierungsgruppe dazu führt, dass nur die aktuelle Version der überwachten Zertifikate installiert wird. Gehen Sie nicht von Homogenität der Knoten in Bezug auf die installierten Zertifikate aus.   

## <a name="simplifying-management---an-autorollover-example"></a>Vereinfachen der Verwaltung: Beispiel für automatisches Rollover
Wir haben Mechanismen und Einschränkungen beschrieben, komplizierte Regeln und Definitionen dargelegt und düstere Vorhersagen von Ausfällen gemacht. Es ist wohl an der Zeit zu zeigen, wie eine automatische Zertifikatverwaltung eingerichtet werden kann, um all diese Bedenken auszuräumen. Dies erfolgt im Kontext eines Azure Service Fabric-Clusters, der in einer PaaSv2-VM-Skalierungsgruppe ausgeführt wird, und zwar wie folgt unter Verwendung von Azure Key Vault für die Verwaltung von Geheimnissen und von verwalteten Identitäten:
  - Die Validierung von Zertifikaten wird von der Anheftung an den Fingerabdruck auf die Anheftung an Antragsteller und Aussteller umgestellt. Jedes Zertifikat mit einem bestimmten Antragsteller von einem bestimmten Aussteller ist gleichermaßen vertrauenswürdig.
    - Zertifikate werden im einem vertrauenswürdigen Speicher (Key Vault) registriert, daraus abgerufen und von einem Agent, in diesem Fall von der Key Vault-VM-Erweiterung, aktualisiert.
    - Die Bereitstellung von Zertifikaten wird von basierend auf Bereitstellungszeit und Version (wie durch ComputeRP) auf nach der Bereitstellung und unter Verwendung von Key Vault-URIs ohne Version umgestellt.
    - Der Zugriff auf Key Vault wird über vom Benutzer zugewiesene, verwaltete Identitäten gewährt. Die Benutzerzugriffsidentität wird erstellt und der bei der Bereitstellung festgelegten VM-Skalierungsgruppe zugewiesen.
    - Nach der Bereitstellung fragt der Agent (die Key Vault VM-Erweiterung) überwachte Zertifikate auf jedem Knoten der VM-Skalierungsgruppe ab und aktualisiert sie. Die Zertifikatrotation ist somit vollständig automatisiert, da Service Fabric automatisch das am weitesten entfernte gültige Zertifikat auswählt.

Die Sequenz ist vollständig skriptfähig bzw. automatisierbar und ermöglicht eine anfängliche Bereitstellung eines Clusters ohne Benutzereingriff, der für automatisches Rollover von Zertifikaten konfiguriert ist. Es folgen detaillierte Schritte. Wir verwenden eine Kombination aus PowerShell-Cmdlets und Fragmenten von JSON-Vorlagen. Die gleiche Funktionalität ist mit allen unterstützten Mitteln zur Interaktion mit Azure zu erreichen.

> [!NOTE]
> Bei diesem Beispiel wird davon ausgegangen, dass sich bereits ein Zertifikat im Tresor befindet. Das Registrieren und Verlängern eines von Key Vault verwalteten Zertifikats erfordert die zuvor in diesem Artikel beschriebenen erforderlichen manuellen Schritte. Verwenden Sie in Produktionsumgebungen von Key Vault verwaltete Zertifikate. Ein Beispielskript speziell für eine Microsoft-interne PKI ist nachstehend angegeben.

> [!NOTE]
> Automatisches Rollover von Zertifikaten ist nur für von einer Zertifizierungsstelle ausgestellte Zertifikate sinnvoll. Der Einsatz selbstsignierter Zertifikate, einschließlich derer, die bei der Bereitstellung eines Service Fabric-Clusters im Azure-Portal generiert werden, ist unsinnig, aber dennoch für lokale/vom Entwickler gehostete Bereitstellungen möglich, indem der Fingerabdruck des Ausstellers als identisch mit dem des Blattzertifikats deklariert wird.

### <a name="starting-point"></a>Startpunkt
Der Kürze halber gehen wir von folgendem Ausgangszustand aus:
  - Der Service Fabric-Cluster ist vorhanden und wird mit einem von einer Zertifizierungsstelle ausgestellten und per Fingerabdruck deklarierten Zertifikat abgesichert.
  - Das Zertifikat ist in einem Tresor gespeichert und wird als VM-Skalierungsgruppengeheimnis bereitgestellt.
  - Dasselbe Zertifikat wird verwendet, um den Cluster in auf dem allgemeinen Namen basierende Zertifikatdeklarationen umzuwandeln, und kann so nach Antragssteller und Aussteller validiert werden. Wenn dies nicht der Fall ist, beziehen Sie das von der Zertifizierungsstelle ausgestellte Zertifikat, das für diesen Zweck vorgesehen ist, und fügen Sie es, wie [hier](service-fabric-cluster-security-update-certs-azure.md) erklärt, per Fingerabdruck zur Clusterdefinition hinzu.

Es folgt ein JSON-Auszug aus einer Vorlage, die einem solchen Zustand entspricht. Beachten Sie, dass viele erforderliche Einstellungen ausgelassen werden und nur die zertifikatbezogenen Aspekte veranschaulicht werden:
```json
  "resources": [
    {   ## VMSS definition
      "apiVersion": "[variables('vmssApiVersion')]",
      "type": "Microsoft.Compute/virtualMachineScaleSets",
      "name": "[variables('vmNodeTypeName')]",
      "location": "[variables('computeLocation')]",
      "properties": {
        "virtualMachineProfile": {
          "extensionProfile": {
            "extensions": [
            {
                "name": "[concat('ServiceFabricNodeVmExt','_vmNodeTypeName')]",
                "properties": {
                  "type": "ServiceFabricNode",
                  "autoUpgradeMinorVersion": true,
                  "publisher": "Microsoft.Azure.ServiceFabric",
                  "settings": {
                    "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                    "nodeTypeRef": "[variables('vmNodeTypeName')]",
                    "dataPath": "D:\\SvcFab",
                    "durabilityLevel": "Bronze",
                    "certificate": {
                        "thumbprint": "[parameters('primaryClusterCertificateTP')]",
                        "x509StoreName": "[parameters('certificateStoreValue')]"
                    }
                  },
                  "typeHandlerVersion": "1.1"
                }
            },}},
          "osProfile": {
            "adminPassword": "[parameters('adminPassword')]",
            "adminUsername": "[parameters('adminUsername')]",
            "secrets": [
            {
                "sourceVault": {
                    "id": "[resourceId('Microsoft.KeyVault/vaults', parameters('keyVaultName'))]"
                },
                "vaultCertificates": [
                {
                    "certificateStore": "[parameters('certificateStoreValue')]",
                    "certificateUrl": "[parameters('clusterCertificateUrlValue')]"
                },
            ]}]
        },
    },
    {   ## cluster definition
        "apiVersion": "[variables('sfrpApiVersion')]",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "certificate": {
            "thumbprint": "[parameters('primaryClusterCertificateTP')]",
            "x509StoreName": "[parameters('certificateStoreValue')]"
        },
    }
  ]
```   

Im Wesentlichen besagt das Vorstehende, dass das Zertifikat mit Fingerabdruck ```json [parameters('primaryClusterCertificateTP')] ```, das unter dem Key Vault-URI ```json [parameters('clusterCertificateUrlValue')] ``` gefunden wird, per Fingerabdruck als das einzige Zertifikat des Clusters deklariert wird. Als Nächstes richten wir die zusätzlichen Ressourcen ein, die erforderlich sind, um das automatische Rollover des Zertifikats sicherzustellen.

### <a name="setting-up-prerequisite-resources"></a>Einrichten vorausgesetzter Ressourcen
Wie bereits erwähnt, wird ein Zertifikat, das als Geheimnis einer VM-Skalierungsgruppe bereitgestellt wird, vom Ressourcenanbieterdienst Microsoft.Compute unter Verwendung der Identität der Erstpartei und im Auftrag des Bereitstellungsbedieners aus dem Tresor abgerufen. Beim automatischen Rollover ändert sich das. Wir gehen dazu über, eine verwaltete Identität zu verwenden, die der VM-Skalierungsgruppe zugeordnet ist und der Berechtigungen für die Geheimnisse des Tresors gewährt wurden.

Alle nachfolgenden Auszüge sollten gleichzeitig bereitgestellt werden. Sie sind einzeln aufgelistet, damit sie einzeln analysiert und erläutert werden können.

Definieren Sie zunächst eine vom Benutzer zugewiesene Identität (Standardwerte sind als Beispiele enthalten). Aktuelle Informationen finden Sie in der [offiziellen Dokumentation](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md#create-a-user-assigned-managed-identity):
```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "userAssignedIdentityName": {
      "type": "string",
      "defaultValue": "sftstuaicus",
      "metadata": {
        "description": "User-assigned managed identity name"
      }
    },
  },
  "variables": {
      "vmssApiVersion": "2018-06-01",
      "sfrpApiVersion": "2018-02-01",
      "miApiVersion": "2018-11-30",
      "kvApiVersion": "2018-02-14",
      "userAssignedIdentityResourceId": "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('userAssignedIdentityName'))]"
  },    
  "resources": [
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "name": "[parameters('userAssignedIdentityName')]",
      "apiVersion": "[variables('miApiVersion')]",
      "location": "[resourceGroup().location]"
    },
  ]}
```

Gewähren Sie anschließend dieser Identität Zugriff auf die Geheimnisse im Tresor. Aktuelle Informationen finden Sie in der [offiziellen Dokumentation](/rest/api/keyvault/vaults/updateaccesspolicy):
```json
  "resources":
  [{
      "type": "Microsoft.KeyVault/vaults/accessPolicies",
      "name": "[concat(parameters('keyVaultName'), '/add')]",
      "apiVersion": "[variables('kvApiVersion')]",
      "properties": {
        "accessPolicies": [
          {
            "tenantId": "[reference(variables('userAssignedIdentityResourceId'), variables('miApiVersion')).tenantId]",
            "objectId": "[reference(variables('userAssignedIdentityResourceId'), variables('miApiVersion')).principalId]",
            "dependsOn": [
              "[variables('userAssignedIdentityResourceId')]"
            ],
            "permissions": {
              "secrets": [
                "get",
                "list"
              ]}}]}}]
```

Es folgen die nächsten Schritte:
  - Weisen Sie die vom Benutzer zugewiesene Identität der VM-Skalierungsgruppe zu.
  - Deklarieren Sie die Abhängigkeit der VM-Skalierungsgruppe von der Erstellung der verwalteten Identität und vom Ergebnis der Gewährung des Zugriffs auf den Tresor.
  - Deklarieren Sie die Key Vault-VM-Erweiterung, und fordern Sie, dass sie beim Start überwachte Zertifikate abruft ([offizielle Dokumentation](../virtual-machines/extensions/key-vault-windows.md)).
  - Aktualisieren Sie die Definition der Service Fabric-VM-Erweiterung so, dass sie von der Key Vault-VM-Erweiterung abhängt und das Clusterzertifikat in einen allgemeinen Namen konvertiert wird. (Wir nehmen diese Änderungen in einem einzigen Schritt vor, da sie in den Geltungsbereich derselben Ressource fallen.)

```json
  "parameters": {
    "kvvmextPollingInterval": {
      "type": "string",
      "defaultValue": "3600",
      "metadata": {
        "description": "kv vm extension polling interval in seconds"
      }
    },
    "kvvmextLocalStoreName": {
      "type": "string",
      "defaultValue": "MY",
      "metadata": {
        "description": "kv vm extension local store name"
      }
    },
    "kvvmextLocalStoreLocation": {
      "type": "string",
      "defaultValue": "LocalMachine",
      "metadata": {
        "description": "kv vm extension local store location"
      }
    },
    "kvvmextObservedCertificates": {
      "type": "array",
      "defaultValue": [
                "https://sftestcus.vault.azure.net/secrets/sftstcncluster",
                "https://sftestcus.vault.azure.net/secrets/sftstcnserver"
            ],
      "metadata": {
        "description": "kv vm extension observed certificates versionless uri"
      }
    },
    "certificateCommonName": {
      "type": "string",
      "defaultValue": "cus.cluster.sftstcn.system.servicefabric.azure-int",
      "metadata": {
        "description": "Certificate Common name"
      }
    },
  },
  "resources": [
  {
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "name": "[variables('vmNodeTypeName')]",
    "location": "[variables('computeLocation')]",
    "dependsOn": [
      "[variables('userAssignedIdentityResourceId')]",
      "[concat('Microsoft.KeyVault/vaults/', concat(parameters('keyVaultName'), '/accessPolicies/add'))]"
    ],
    "identity": {
      "type": "UserAssigned",
      "userAssignedIdentities": {
        "[variables('userAssignedIdentityResourceId')]": {}
      }
    },
    "virtualMachineProfile": {
      "extensionProfile": {
        "extensions": [
        {
          "name": "KVVMExtension",
          "properties": {
            "publisher": "Microsoft.Azure.KeyVault",
            "type": "KeyVaultForWindows",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "secretsManagementSettings": {
                    "pollingIntervalInS": "[parameters('kvvmextPollingInterval')]",
                    "linkOnRenewal": false,
                    "observedCertificates": "[parameters('kvvmextObservedCertificates')]",
                    "requireInitialSync": true
                }
            }
          }
        },
        {
        "name": "[concat('ServiceFabricNodeVmExt','_vmNodeTypeName')]",
        "properties": {
          "type": "ServiceFabricNode",
          "provisionAfterExtensions" : [ "KVVMExtension" ],
          "publisher": "Microsoft.Azure.ServiceFabric",
          "settings": {
            "certificate": {
                "commonNames": [
                    "[parameters('certificateCommonName')]"
                ],
                "x509StoreName": "[parameters('certificateStoreValue')]"
            }
            },
            "typeHandlerVersion": "1.0"
          }
        },
  ] } ## extension profile
  },  ## VM profile
  "osProfile": {
    "adminPassword": "[parameters('adminPassword')]",
    "adminUsername": "[parameters('adminUsername')]",
  } 
  }
  ]
```
Beachten Sie, obwohl oben nicht explizit erwähnt, dass wir die URL des Tresorzertifikats aus dem Abschnitt OsProfile der VM-Skalierungsgruppe entfernt haben.
Der letzte Schritt ist die Aktualisierung der Clusterdefinition dergestalt, dass die Zertifikatdeklaration von Fingerabdruck in allgemeinen Namen geändert wird. Hier heften wir auch die Fingerabdrücke des Ausstellers an:

```json
  "parameters": {
    "certificateCommonName": {
      "type": "string",
      "defaultValue": "cus.cluster.sftstcn.system.servicefabric.azure-int",
      "metadata": {
        "description": "Certificate Common name"
      }
    },
    "certificateIssuerThumbprint": {
      "type": "string",
      "defaultValue": "1b45ec255e0668375043ed5fe78a09ff1655844d,d7fe717b5ff3593764f4d90654d86e8362ec26c8,3ac7c3cac8de0dd392c02789c8be97474f456960,96ea05926e2e42cc207e358668be2c316857fb5e",
      "metadata": {
        "description": "Certificate issuer thumbprints separated by comma"
      }
    },
  },
  "resources": [
    {
      "apiVersion": "[variables('sfrpApiVersion')]",
      "type": "Microsoft.ServiceFabric/clusters",
      "name": "[parameters('clusterName')]",
      "location": "[parameters('clusterLocation')]",
      "properties": {
        "certificateCommonNames": {
          "commonNames": [{
              "certificateCommonName": "[parameters('certificateCommonName')]",
              "certificateIssuerThumbprint": "[parameters('certificateIssuerThumbprint')]"
          }],
          "x509StoreName": "[parameters('certificateStoreValue')]"
        },
  ]
```

An diesem Punkt können Sie die oben genannten Aktualisierungen im Rahmen einer einzigen Bereitstellung ausführen. Der Ressourcenanbieterdienst für Service Fabric teilt seinerseits die Clusteraktualisierung in mehrere Schritte auf, wie im Abschnitt zum [Konvertieren von Clusterzertifikaten von Fingerabdruck in allgemeinen Namen](cluster-security-certificates.md#converting-a-cluster-from-thumbprint--to-common-name-based-certificate-declarations) beschrieben.

### <a name="analysis-and-observations"></a>Analyse und Beobachtungen
Dieser Abschnitt ist ein Überblick über die oben beschriebenen Schritte und lenkt die Aufmerksamkeit auf wichtige Aspekte.

#### <a name="certificate-provisioning-explained"></a>Erläuterung der Zertifikatbereitstellung
Die Key Vault-VM-Erweiterung wird als Bereitstellungs-Agent mit einer vorgegebenen Frequenz kontinuierlich ausgeführt. Falls ein überwachtes Zertifikat nicht abgerufen wird, wird mit dem nächsten in der Reihe fortgefahren. Dann folgt ein Wechsel in den Ruhezustand bis zum nächsten Zyklus. Die Service Fabric-VM-Erweiterung benötigt als Bootstrap-Agent des Clusters die deklarierten Zertifikate, bevor der Cluster gebildet werden kann. Dies wiederum bedeutet, dass die Service Fabric-VM-Erweiterung erst nach dem erfolgreichen Abruf der Clusterzertifikate, hier durch die Klausel ```json "provisionAfterExtensions" : [ "KVVMExtension" ]"``` und die Einstellung ```json "requireInitialSync": true``` der Key Vault-VM-Erweiterung bezeichnet, ausgeführt werden kann. Dies ist ein Hinweis an die Key Vault-VM-Erweiterung darauf, dass sie bei der ersten Ausführung (nach Bereitstellung oder Neustart) ihre überwachten Zertifikate durchlaufen muss, bis alle erfolgreich heruntergeladen wurden. Wenn dieser Parameter auf FALSE festgelegt wird und die Clusterzertifikate nicht abgerufen werden können, schlägt die Clusterbereitstellung fehl. Umgekehrt würde die Anforderung einer anfänglichen Synchronisierung mit einer falschen/ungültigen Liste überwachter Zertifikate zu einem Fehler bei der Key Vault-VM-Erweiterung und damit wiederum zu einem Fehler bei der Bereitstellung des Clusters führen.  

#### <a name="certificate-linking-explained"></a>Erläuterung der Zertifikatverknüpfung
Möglicherweise ist Ihnen das Flag linkOnRenewal der Key Vault-VM-Erweiterung und die Tatsache aufgefallen, dass es auf FALSE festgelegt ist. Wir befassen uns hier eingehend mit dem von diesem Flag gesteuerten Verhalten und seinen Auswirkungen auf den Betrieb eines Clusters. Beachten Sie, dass dieses Verhalten für Windows spezifisch ist.

Laut zugehöriger [Definition](../virtual-machines/extensions/key-vault-windows.md#extension-schema) gilt Folgendes:

```json
"linkOnRenewal": <Only Windows. This feature enables auto-rotation of SSL certificates, without necessitating a re-deployment or binding.  e.g.: false>,
```

Die zur Herstellung einer TLS-Verbindung verwendeten Zertifikate werden in der Regel [als Handle](/windows/win32/api/sspi/nf-sspi-acquirecredentialshandlea) über den Security Support Provider von Schannel bezogen, d. h. der Client greift nicht direkt auf den privaten Schlüssel des Zertifikats selbst zu. Schannel unterstützt die Umleitung (Verknüpfung) von Anmeldeinformationen in Form einer Zertifikaterweiterung ([CERT_RENEWAL_PROP_ID](/windows/win32/api/wincrypt/nf-wincrypt-certsetcertificatecontextproperty#cert_renewal_prop_id)). Wenn diese Eigenschaft festgelegt ist, stellt ihr Wert den Fingerabdruck des Zertifikats für „Verlängerung“ dar, sodass Schannel stattdessen versucht, das verknüpfte Zertifikat zu laden. Tatsächlich durchläuft es diese verknüpfte (und hoffentlich azyklische) Liste, bis es am Ende zum „endgültigen“ Zertifikat gelangt, und zwar zu einem ohne Verlängerungskennzeichen. Bei vernünftiger Nutzung ist dieses Feature eine große Erleichterung gegen den Verlust von Verfügbarkeit aufgrund z. B. abgelaufener Zertifikate. In anderen Fällen kann sie die Ursache für Ausfälle sein, die schwer zu diagnostizieren und abzustellen sind. Schannel führt das Durchlaufen von Zertifikaten bezüglich ihrer Verlängerungseigenschaften bedingungslos aus, und zwar unabhängig von Antragsteller, Aussteller oder anderen spezifischen Attributen, die an der Validierung des resultierenden Zertifikats durch den Client beteiligt sind. Es ist in der Tat möglich, dass das resultierende Zertifikat keinen zugehörigen privaten Schlüssel hat oder dass der Schlüssel nicht der ACL seines potenziellen Consumers hinzugefügt wurde. 
 
Wenn die Verknüpfung aktiviert ist, versucht die Key Vault-VM-Erweiterung beim Abrufen eines überwachten Zertifikats aus dem Tresor, übereinstimmende, vorhandene Zertifikate zu finden, um sie über die Erweiterungseigenschaft zur Verlängerung zu verknüpfen. Der Abgleich basiert (ausschließlich) auf dem alternativer Antragstellernamen (Subject Alternative Name, SAN) und funktioniert wie unten dargestellt.
Angenommen, es gibt die beiden folgenden Zertifikate: A: CN = “Alice's accessories”, SAN = {“alice.universalexports.com”}, renewal = ‘’ B: CN = “Bob's bits”, SAN = {“bob.universalexports.com”, “bob.universalexports.net”}, renewal = ‘’
 
Angenommen, das Zertifikat C wird von der Key Vault-VM-Erweiterung abgerufen: CN = “Mallory's malware”, SAN = {“alice.universalexports.com”, “bob.universalexports.com”, “mallory.universalexports.com”}
 
Die SAN-Liste von A ist vollständig in der von C enthalten. Daher gilt: A.renewal = C.thumbprint. Die SAN-Liste von B hat eine gemeinsame Schnittmenge mit der von C, ist aber nicht vollständig in ihr enthalten, weshalb B.renewal leer bleibt.
 
Jeder Versuch, AcquireCredentialsHandle (Schannel) in diesem Zustand für das Zertifikat A aufzurufen, führt letztlich dazu, dass C an die Gegenseite gesendet wird. Bei Service Fabric verwendet das [Transportsubsystem](service-fabric-architecture.md#transport-subsystem) eines Clusters Schannel zur gegenseitigen Authentifizierung. Daher wirkt sich das zuvor beschriebene Verhalten direkt auf die grundlegende Kommunikation des Clusters aus. Wenn wir das obige Beispiel fortsetzen und annehmen, dass A das Clusterzertifikat ist, hängt der weitere Verlauf von Folgendem ab:
  - Wenn der private Schlüssel von C nicht der ACL des Kontos hinzugefügt wurde, mit dem Service Fabric ausgeführt wird, kommt es zu Fehlern beim Beziehen des privaten Schlüssels (SEC_E_UNKNOWN_CREDENTIALS o. ä).
  - Wenn auf den privaten Schlüssel von C zugegriffen werden kann, kommt es zu Autorisierungsfehlern, die von den anderen Knoten zurückgegeben werden (CertificateNotMatched, nicht autorisiert usw.). 
 
In beiden Fällen schlägt der Transport fehl und der Cluster kann ausfallen. Die Symptome schwanken. Erschwerend kommt hinzu, dass die Verknüpfung von der Reihenfolge der Verlängerung abhängt. Diese wird durch die Reihenfolge in der Liste der überwachten Zertifikate der Key Vault-VM-Erweiterung, den Verlängerungszeitplan im Tresor oder sogar durch vorübergehende Fehler vorgegeben, die die Reihenfolge des Abrufs verändern würden.

Um derartige Vorfälle zu vermeiden, empfehlen wir Folgendes:
  - Mischen Sie nicht die SANs verschiedener Tresorzertifikate. Die einzelnen Tresorzertifikate sollten einen bestimmten Zweck erfüllen, und ihr Antragsteller und SAN sollten diesen spezifisch widerspiegeln.
  - Fügen Sie den allgemeinen Namen des Antragstellers der SAN-Liste hinzu (wörtlich als „`CN=<subject common name>`“).  
  - Wenn Sie unsicher sind, deaktivieren Sie die Verknüpfung bei Verlängerung für Zertifikate, die mit der Key Vault-VM-Erweiterung bereitgestellt werden. 

#### <a name="why-use-a-user-assigned-managed-identity-what-are-the-implications-of-using-it"></a>Warum sollte eine vom Benutzer zugewiesene, verwaltete Identität verwendet werden? Welche Auswirkungen hat diese Verwendung?
Wie aus den obigen JSON-Ausschnitten hervorgeht, ist eine bestimmte Abfolge der Vorgänge/Aktualisierungen erforderlich, um den Erfolg der Konvertierung zu gewährleisten und die Verfügbarkeit des Clusters aufrechtzuerhalten. Insbesondere deklariert und verwendet die Ressource der VM-Skalierungsgruppe ihre Identität, um Geheimnisse (aus der Sicht des Benutzers) in einer einzigen Aktualisierung abzurufen. Die Service Fabric-VM-Erweiterung (die das Bootstrapping des Clusters durchführt) hängt vom Abschluss der Key Vault-VM-Erweiterung ab, die vom erfolgreichen Abruf der überwachten Zertifikate abhängt. Die Key Vault-VM-Erweiterung verwendet die Identität einer VM-Skalierungsgruppe für den Zugriff auf den Tresor, was bedeutet, dass die Zugriffsrichtlinie für den Tresor bereits vor der Bereitstellung der VM-Skalierungsgruppe aktualisiert worden sein muss. 

Um über die Erstellung einer verwalteten Identität zu veranlassen oder sie einer anderen Ressource zuzuweisen, muss der Zuständige für die Bereitstellung über die erforderliche Rolle (ManagedIdentityOperator) im Abonnement oder in der Ressourcengruppe verfügen, und zwar zusätzlich zu den Rollen, die für die Verwaltung der anderen in der Vorlage referenzierten Ressourcen erforderlich sind. 

Beachten Sie vom Standpunkt der Sicherheit aus, dass die VM bzw. VM-Skalierungsgruppe im Hinblick auf ihre Azure-Identität als Sicherheitsbegrenzung betrachtet wird. Das bedeutet, dass jede Anwendung, die in der VM gehostet wird, im Prinzip ein Zugriffstoken erhalten könnte, das die VM repräsentiert. Zugriffstoken für verwaltete Identitäten werden vom nicht authentifizierten IMDS-Endpunkt bezogen. Wenn Sie die VM als eine gemeinsam genutzte oder mehrinstanzenfähige Umgebung betrachten, ist diese Methode zum Abrufen von Clusterzertifikaten vielleicht nicht geeignet. Es ist jedoch der einzige Bereitstellungsmechanismus, der für das automatische Rollover von Zertifikaten in Frage kommt.

## <a name="troubleshooting-and-frequently-asked-questions"></a>Problembehandlung und häufig gestellte Fragen

*F:* Wie ist eine programmgesteuerte Registrierung bei einem von Key Vault verwalteten Zertifikat möglich?
*A:* Ermitteln Sie den Namen des Ausstellers anhand der Key Vault-Dokumentation, und ersetzen Sie ihn dann im nachstehenden Skript.  
```PowerShell
  $issuerName=<depends on your PKI of choice>
    $clusterVault="sftestcus"
    $clusterCertVaultName="sftstcncluster"
    $clusterCertCN="cus.cluster.sftstcn.system.servicefabric.azure-int"
    Set-AzKeyVaultCertificateIssuer -VaultName $clusterVault -Name $issuerName -IssuerProvider $issuerName
    $distinguishedName="CN=" + $clusterCertCN
    $policy = New-AzKeyVaultCertificatePolicy `
        -IssuerName $issuerName `
        -SubjectName $distinguishedName `
        -SecretContentType 'application/x-pkcs12' `
        -Ekus "1.3.6.1.5.5.7.3.1", "1.3.6.1.5.5.7.3.2" `
        -ValidityInMonths 4 `
        -KeyType 'RSA' `
        -RenewAtPercentageLifetime 75        
    Add-AzKeyVaultCertificate -VaultName $clusterVault -Name $clusterCertVaultName -CertificatePolicy $policy
    
    # poll the result of the enrollment
    Get-AzKeyVaultCertificateOperation -VaultName $clusterVault -Name $clusterCertVaultName 
```

*F:* Was passiert, wenn ein Zertifikat von einem nicht deklarierten/nicht näher bezeichneten Aussteller ausgestellt wird? Wo kann ich die vollständige Liste der aktiven Aussteller einer bestimmten PKI abrufen?
*A:* Wenn in der Zertifikatdeklaration Fingerabdrücke des Ausstellers angegeben sind und der direkte Aussteller des Zertifikats nicht in der Liste der angehefteten Aussteller aufgeführt ist, wird das Zertifikat als ungültig betrachtet, und zwar unabhängig davon, ob sein Stammzertifikat vom Client als vertrauenswürdig eingestuft wird oder nicht. Daher ist es entscheidend sicherzustellen, dass die Liste der Aussteller auf dem neuesten Stand ist. Die Einführung eines neuen Ausstellers ist ein seltenes Ereignis und sollte umfassend bekannt gegeben werden, bevor mit der Ausstellung von Zertifikaten begonnen wird. 

Im Allgemeinen veröffentlicht und pflegt eine PKI eine Erklärung zum Zertifizierungsbetrieb in Übereinstimmung mit IETF [RFC 7382](https://tools.ietf.org/html/rfc7382). Neben anderen Informationen enthält sie alle aktiven Aussteller. Der programmgesteuerte Abruf dieser Liste kann sich je nach PKI unterscheiden.   

Konsultieren Sie für Microsoft-interne PKIs die interne Dokumentation zu den Endpunkten/SDKs, die zum Abrufen der autorisierten Aussteller verwendet werden. Es ist Aufgabe des Clusterbesitzers, diese Liste regelmäßig zu überprüfen und sicherzustellen, dass ihre Clusterdefinition *alle* erwarteten Aussteller enthält.

*F:* Werden mehrere PKIs unterstützt? 
*A:* Ja. Sie dürfen im Clustermanifest nicht mehrere CN-Einträge mit demselben Wert deklarieren, können aber Aussteller aus mehreren PKIs auflisten, die demselben CN entsprechen. Es wird nicht empfohlen, dies zu tun, und die Verfahren zur Transparenz von Zertifikaten können das Ausstellen solcher Zertifikate verhindern. Als Instrument zur Migration von einer PKI zu einer anderen ist dies jedoch eine akzeptable Lösung.

*F:* Was geschieht, wenn das aktuelle Clusterzertifikat nicht von einer Zertifizierungsstelle ausgestellt wurde oder nicht den beabsichtigten Antragsteller hat? 
*A:* Beziehen Sie ein Zertifikat mit dem beabsichtigten Antragsteller, und fügen Sie es per Fingerabdruck als sekundär in die Definition des Clusters ein. Nach erfolgreichem Abschluss der Aktualisierung veranlassen Sie eine weitere Aktualisierung der Clusterkonfiguration, um die Zertifikatdeklaration in allgemeinen Namen zu konvertieren. 

[Image1]:./media/security-cluster-certificate-mgmt/certificate-journey-thumbprint.png
[Image2]:./media/security-cluster-certificate-mgmt/certificate-journey-common-name.png

---
title: Erstellen von Plänen für ein VM-Angebot im Azure Marketplace
description: Erfahren Sie, wie Sie Pläne für ein VM-Angebot im Azure Marketplace erstellen.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 10/13/2021
ms.openlocfilehash: 0a7ca14a797ef2680527cd6992ff9a7a1d6ea115
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131084898"
---
# <a name="create-plans-for-a-virtual-machine-offer"></a>Erstellen von Plänen für ein VM-Angebot

Auf der Seite **Planübersicht** (Auswahl im Partner Center im linken Navigationsmenü) können Sie eine Vielzahl von Planoptionen innerhalb desselben Angebots bereitstellen. Ein Angebot erfordert mindestens einen Plan (zuvor als SKU bezeichnet), der sich hinsichtlich der Monetarisierungszielgruppe, Azure-Region, Features oder VM-Images unterscheiden kann.

Sie können bis zu 100 Pläne für jedes Angebot erstellen, von denen bis zu 45 privat sein können. Erfahren Sie unter [Private Angebote im kommerziellen Microsoft-Marketplace](private-offers.md) mehr über private Pläne.

Wählen Sie nach dem Erstellen Ihrer Pläne die Registerkarte **Planübersicht** aus, um folgende Informationen anzuzeigen:

- Plannamen
- Lizenzmodelle
- Zielgruppe (öffentlich oder privat)
- Aktueller Veröffentlichungsstatus
- Verfügbare Aktionen

Die in diesem Bereich verfügbaren Aktionen unterscheiden sich abhängig vom aktuellen Status Ihres Plans.

- Wenn es sich bei dem Plan um einen Entwurf handelt, wählen Sie **Entwurf löschen** aus.
- Wenn der Planstatus live veröffentlicht wird, wählen Sie **Verkauf des Plans einstellen** oder **Private Zielgruppe synchronisieren** aus.

## <a name="create-a-new-plan"></a>Erstellen eines neuen Plans

Wählen Sie oben **+ Neuen Plan erstellen** aus.

Geben Sie im Dialogfeld **Neuer Plan** für jeden Plan im Angebot eine eindeutige **Plan-ID** ein. Diese ID ist für Kunden in der Webadresse des Produkts sichtbar. Verwenden Sie nur Kleinbuchstaben und Zahlen, Bindestriche oder Unterstriche bei maximal 50 Zeichen.

> [!NOTE]
> Die Plan-ID kann nicht mehr geändert werden, nachdem Sie **Erstellen** ausgewählt haben.

Geben Sie einen **Plannamen** ein. Kunden sehen diesen Namen, wenn sie sich für einen der Pläne in Ihrem Angebot interessieren. Erstellen Sie einen eindeutigen Namen, der die Unterschiede der einzelnen Pläne deutlich macht. Beispielsweise könnten Sie **Windows Server** mit den Plänen *Nutzungsbasierte Bezahlung*, *BYOL*, *Erweitert* und *Enterprise* verwenden.

Klicken Sie auf **Erstellen**. Dadurch wird die Seite **Plansetup** geöffnet.

## <a name="plan-setup"></a>Plansetup

Legen Sie die allgemeine Konfiguration für den Plantyp fest, geben Sie an, ob die technische Konfiguration von einem anderen Plan wiederverwendet wird, und nennen Sie die Azure-Regionen, in denen der Plan verfügbar sein soll. Ihre hier getroffene Auswahl legt fest, welche Felder in anderen Bereichen desselben Plans angezeigt werden.

### <a name="azure-regions"></a>Azure-Regionen

Ihr Plan muss in mindestens einer Azure-Region verfügbar gemacht werden.

Wählen Sie **Azure Global** aus, um Ihren Plan für Kunden in allen globalen Azure-Regionen mit Integration des kommerziellen Marketplace verfügbar zu machen. Weitere Informationen finden Sie unter [Geografische Verfügbarkeit und Unterstützung von Währungen](marketplace-geo-availability-currencies.md).

Wählen Sie **Azure Government** aus, um Ihren Plan in der [Azure Government](../azure-government/documentation-government-welcome.md)-Region verfügbar zu machen. Diese Region bietet kontrolliertem Zugriff für Kunden von US-Behörden auf Bundes-, Bundesstaats-, Gemeinde- oder Stammesgebietsebene sowie für Partner, die diese beliefern dürfen. Als Herausgeber sind Sie für alle Kompatibilitätskontrollen, Sicherheitsmaßnahmen und bewährten Methoden verantwortlich. Azure Government verwendet physisch isolierte Rechenzentren und Netzwerke (die sich ausschließlich in den USA befinden).

Testen und validieren Sie Ihren Plan vor dem Veröffentlichen für [Azure Government](../azure-government/documentation-government-manage-marketplace-partners.md) in der Umgebung, da bestimmte Endpunkte möglicherweise abweichen. Fordern Sie für das Einrichten und Testen Ihres Plans auf der Seite [Microsoft Azure Government-Testversion](https://azure.microsoft.com/global-infrastructure/government/request/) ein Testkonto an.

> [!NOTE]
> Nachdem Ihr Plan veröffentlicht und in einer bestimmten Azure-Region verfügbar gemacht wurde, können Sie diese Region nicht mehr entfernen.

### <a name="azure-government-certifications"></a>Azure Government-Zertifizierungen

Diese Option ist nur sichtbar, wenn Sie im vorherigen Abschnitt **Azure Government** als Azure-Region ausgewählt haben.

Azure Government-Dienste verarbeiten Daten, die bestimmten behördlichen Vorschriften und Anforderungen unterliegen. Beispielsweise FedRAMP, NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 und CJIS. Um bei Ihren Zertifizierungen ein Bewusstsein für diese Programme zu schaffen, können Sie bis zu 100 Links bereitstellen, in denen sie beschrieben werden. Diese können entweder direkt mit ihrem Listing im Programm verknüpft sein oder zu Beschreibungen der Konformität mit diesen Programmen auf Ihren eigenen Websites weiterleiten. Diese Links sind nur für Azure Government-Kunden sichtbar.

Wählen Sie **Entwurf speichern** aus, bevor Sie mit der nächsten Registerkarte im linken Navigationsmenü (**Planlisten**) fortfahren.

## <a name="plan-listing"></a>Planlisting

Konfigurieren Sie die Auflistungsdetails des Plans. In diesem Bereich werden bestimmte Informationen angezeigt, die sich von anderen Plänen im selben Angebot unterscheiden können.

### <a name="plan-name"></a>Planname

In dieses Feld wird automatisch der Name eingetragen, den Sie Ihrem Plan beim Erstellen gegeben haben. Dieser Name wird im Azure Marketplace als Titel dieses Plans angezeigt. Er ist auf 100 Zeichen beschränkt.

### <a name="plan-summary"></a>Planzusammenfassung

Geben Sie eine kurze Zusammenfassung Ihres Plans (nicht des Angebots) an. Diese Zusammenfassung darf maximal 100 Zeichen enthalten.

### <a name="plan-description"></a>Planbeschreibung

Beschreiben Sie, was Ihren Softwareplan von anderen unterscheidet und worin die Unterschiede zwischen den Plänen in Ihrem Angebot bestehen. Beschreiben Sie nur den Plan, nicht das Angebot. Die Planbeschreibung kann bis zu 2.000 Zeichen enthalten.

Wählen Sie **Entwurf speichern** aus, bevor Sie mit der nächsten Registerkarte im linken Navigationsmenü (**Preise und Verfügbarkeit**) fortfahren.

## <a name="pricing-and-availability"></a>Preise und Verfügbarkeit

In diesem Bereich konfigurieren Sie Folgendes:

- Märkte, in denen dieser Plan verfügbar ist Jeder Plan muss in mindestens einem [Markt](marketplace-geo-availability-currencies.md) verfügbar sein.
- Preis pro Stunde
- Ob der Plan für alle Benutzer oder nur für bestimmte Kunden (eine private Zielgruppe) angezeigt werden soll

### <a name="markets"></a>Märkte

Jeder Plan muss in mindestens einem Markt verfügbar sein. Die meisten Märkte werden standardmäßig ausgewählt. Um die Liste zu bearbeiten, wählen Sie **Märkte bearbeiten** aus, und aktivieren oder deaktivieren Sie die Kontrollkästchen für die einzelnen Marktstandorte, an denen dieser Plan für den Kauf verfügbar sein soll (oder nicht). Benutzer in diesen ausgewählten Märkten können das Angebot weiterhin in allen Azure-Regionen bereitstellen, die im Abschnitt [Plansetup](#plan-setup) ausgewählt wurden.

Wählen Sie **Nur von „Von Microsoft bezahlte Steuern“ auswählen** aus, um nur Länder/Regionen auszuwählen, in denen Microsoft für Sie Verkaufs- und Verbrauchssteuer überweist. Die Veröffentlichung in China ist auf *kostenlose* oder *BYOL*-Pläne (Bring Your Own License) beschränkt.

Wenn Sie bereits Preise für Ihren Plan in der Währung US-Dollar (USD) festgelegt haben und einen anderen Marktstandort hinzufügen, wird der Preis für den neuen Markt entsprechend dem aktuellen Wechselkurs berechnet. Überprüfen Sie vor dem Veröffentlichen immer den Preis für jeden Markt. Überprüfen Sie Ihre Preise, indem Sie **Export prices (xlsx)** (Preise exportieren (XLSX)) auswählen, nachdem Sie die Änderungen gespeichert haben.

Wenn Sie einen Markt entfernen, können Kunden in diesem Markt, die aktive Bereitstellungen nutzen, keine neuen Bereitstellungen mehr erstellen und ihre vorhandenen Bereitstellungen nicht mehr hochskalieren. Vorhandene Bereitstellungen sind nicht betroffen.

Wählen Sie **Save** (Speichern) aus, um den Vorgang fortzusetzen.

### <a name="pricing"></a>Preise

Wählen Sie **Nutzungsbasierter monatlicher Fakturierungsplan** als **Lizenzmodell** aus, um die Preise für diesen Plan zu konfigurieren, oder **BYOL (Bring Your Own License)** , damit Kunden diesen Plan mit Ihrer vorhandenen Lizenz verwenden können.

Verwenden Sie für einen nutzungsbasierten monatlichen Fakturierungsplan eine der drei folgenden Preisoptionen:

- **Pro Kern**: Geben Sie die Preise pro Kern in USD an. Microsoft berechnet die Preise pro Kerngröße und rechnet sie anhand des aktuellen Wechselkurses in lokale Währungen um.
- **Pro Kerngröße**: Geben Sie die Preise pro Kerngröße in US-Dollar (USD) an. Microsoft berechnet die Preise und rechnet sie anhand des aktuellen Wechselkurses in lokale Währungen um.
- **Pro Markt und Kerngröße**: Geben Sie die Preise für die einzelnen Kerngrößen für alle Märkte an. Sie können die Preise aus einem Arbeitsblatt importieren.

Geben Sie einen **Preis pro Kern** ein, und wählen Sie dann **Preis pro Kerngröße** aus, um eine Tabelle mit Preis-/Stundenberechnungen anzuzeigen.

> [!NOTE]
> Speichern Sie Preisänderungen, damit die Preisdaten exportiert werden können. Nachdem ein Preis für einen Markt in Ihrem Plan veröffentlicht wurde, kann er nicht mehr geändert werden. Um vor der Veröffentlichung dieser Preise sicherzustellen, dass sie richtig sind, exportieren Sie das Preisarbeitsblatt, und überprüfen Sie die Preise für jeden Markt.

### <a name="free-trial"></a>Kostenlose Testversion

Sie können Ihren Kunden für einen Monat, drei oder sechs Monate eine **kostenlose Testversion** anbieten.

### <a name="plan-visibility"></a>Sichtbarkeit des Plans

Sie können jeden Plan so entwerfen, dass er für alle Benutzer oder nur für eine vorab ausgewählte Zielgruppe sichtbar ist. Weisen Sie die Mitgliedschaften in dieser eingeschränkten Zielgruppe mithilfe von Azure-Mandanten-IDs, Azure-Abonnement-IDs oder beiden ID-Typen zu.

**Öffentlich**: Ihr Plan kann von allen Benutzern eingesehen werden.

**Privat**: Machen Sie Ihren Plan nur für eine vorab ausgewählte Zielgruppe sichtbar. Nachdem er als privater Plan veröffentlicht wurde, können Sie die Zielgruppe aktualisieren oder ihn in „öffentlich“ ändern. Nachdem Sie einen Plan öffentlich gemacht haben, muss er auch öffentlich bleiben. Er kann nicht wieder in einen privaten Plan geändert werden.

Weisen Sie anhand von *Azure-Mandanten-IDs*, *Azure-Abonnement-IDs* oder beiden ID-Typen die Zielgruppe zu, die auf diesen privaten Plan zugreifen darf. Optional können Sie eine **Beschreibung** der einzelnen zugewiesenen Azure-Mandanten-IDs oder Azure-Abonnement-IDs einschließen. Fügen Sie bis zu zehn Abonnement-IDs und Mandanten-IDs manuell hinzu, oder importieren Sie eine CSV-Tabelle, falls mehr als zehn IDs erforderlich sind.

> [!NOTE]
> Eine private oder eingeschränkte Zielgruppe unterscheidet sich von der im Bereich **Vorschau** definierten Vorschauzielgruppe. Eine Vorschauzielgruppe kann auf das Angebot zugreifen, *bevor* es im Azure Marketplace live veröffentlicht wird. Auch wenn die Auswahl der privaten Zielgruppe nur für einen bestimmten Plan gilt, kann die private Zielgruppe alle Pläne (private und nicht private Pläne) zu Validierungszwecken anzeigen.

Private Angebote werden bei Azure-Abonnements, die über einen Handelspartner des Cloud Solution Provider-Programms (CSP) eingerichtet wurden, nicht unterstützt.

### <a name="hide-plan"></a>Plan ausblenden

Wenn Ihre VM bei Verweisen über eine andere Lösungsvorlage oder verwaltete Anwendung nur indirekt bereitgestellt werden soll, aktivieren Sie dieses Kontrollkästchen, um Ihre VM zu veröffentlichen, aber für Kunden bei einer direkten Suche auszublenden.

Jeder Azure-Kunde kann das Angebot entweder mithilfe von PowerShell oder über die CLI bereitstellen.  Wenn Sie dieses Angebot für eine begrenzte Anzahl von Kunden verfügbar machen möchten, legen Sie den Plan auf **Privat** fest. 

Ausgeblendete Pläne generieren keine Vorschaulinks. Sie können Sie jedoch testen, indem Sie [die folgenden Schritte](azure-vm-create-faq.yml#how-do-i-test-a-hidden-preview-image-)ausführen. 

Wählen Sie **Entwurf speichern** aus, bevor Sie mit der nächsten Registerkarte im linken Planmenü (**Technische Konfiguration**) fortfahren.

## <a name="technical-configuration"></a>Technische Konfiguration

Geben Sie die Images und andere technische Eigenschaften an, die diesem Plan zugeordnet sind.

### <a name="reuse-technical-configuration"></a>Wiederverwenden der technischen Konfiguration

Mit dieser Option können Sie die gleichen technischen Konfigurationseinstellungen planübergreifend innerhalb desselben Angebots verwenden und daher dieselben Images nutzen. Wenn Sie die Option „Technische Konfiguration wiederverwenden“ aktivieren, erbt Ihr Plan die gleichen technischen Konfigurationseinstellungen wie der ausgewählte Basisplan.  Wenn Sie den Basisplan ändern, werden die Änderungen auf dem Plan widergespiegelt, der die Konfiguration wiederverwendet.

Einige häufige Gründe für die erneute Verwendung der technischen Konfigurationseinstellungen aus einem anderen Plan sind die folgenden:

1. Die gleichen Images sind sowohl für die *nutzungsbasierte Bezahlung* als auch für *BYOL* verfügbar.
2. Die gleiche technische Konfiguration aus einem öffentlichen Plan wird für einen privaten Plan mit einem anderen Preis wiederverwendet. 
3. Das Verhalten Ihrer Lösung basiert auf dem Plan, den ein*e Benutzer*in auswählt. Beispielsweise ist die Software identisch, aber die Features variieren je nach Plan.

Nutzen Sie [Azure Instance Metadata Service](../virtual-machines/windows/instance-metadata-service.md) (IMDS), um zu ermitteln, in welchem Plan Ihre Lösung bereitgestellt wird, um die Lizenz zu überprüfen oder geeignete Features zu aktivieren.

Wenn Sie später verschiedene Änderungen zwischen Ihren Plänen veröffentlichen möchten, können Sie sie trennen. Trennen Sie den Plan, indem Sie die technische Konfiguration wiederverwendbar machen, indem Sie diese Option mit Ihrem Plan deaktivieren. Nach der Trennung hat Ihr Plan die gleichen technischen Konfigurationseinstellungen an der Stelle ihrer letzten Einstellung, und Ihre Pläne können in der Konfiguration abweichen. Ein Plan, der in der Vergangenheit unabhängig veröffentlicht wurde, kann später keine technische Konfiguration wiederverwenden. 

### <a name="operating-system"></a>Betriebssystem

Wählen Sie die Betriebssystemfamilie **Windows** oder **Linux** aus.

Wählen Sie das Windows-**Release** oder den Linux-**Anbieter** aus.

Nehmen Sie für das Betriebssystem die Eingabe **Anzeigename für Betriebssystem** vor. Dieser Name ist für Kunden sichtbar.

### <a name="recommended-vm-sizes"></a>Empfohlene VM-Größen

Wählen Sie den Link zur Auswahl von bis zu sechs empfohlenen VM-Größen für die Anzeige im Azure Marketplace aus.

### <a name="open-ports"></a>Öffnen von Ports

Fügen Sie geöffnete öffentliche oder private Ports auf einem bereitgestellten virtuellen Computer hinzu.

### <a name="properties"></a>Eigenschaften

Nachfolgend sehen Sie eine Liste der Eigenschaften, die für Ihre VM ausgewählt werden können.

- **Sicherung unterstützt**: Aktivieren Sie diese Eigenschaft, wenn Ihre Images die Azure-VM-Sicherung unterstützen. Weitere Informationen finden Sie unter [Azure-VM-Sicherung](../backup/backup-azure-vms-introduction.md).

- **Unterstützt den beschleunigten Netzwerkbetrieb**: Aktivieren Sie diese Eigenschaft, wenn die VM-Images für diesen Plan E/A-Virtualisierung mit Einzelstamm (SR-IOV) für einen virtuellen Computer unterstützen, was für geringe Latenz und einen hohen Durchsatz für die Netzwerkschnittstelle sorgt. Erfahren Sie mehr über den [beschleunigten Netzwerkbetrieb](https://go.microsoft.com/fwlink/?linkid=2124513).

- **Unterstützt die cloud-init-Konfiguration**: Aktivieren Sie diese Eigenschaft, wenn die Images in diesem Plan nach der Bereitstellung ausgeführte cloud-init-Skripts unterstützen. Weitere Informationen finden Sie unter [cloud-init-Konfiguration](../virtual-machines/linux/using-cloud-init.md).

- **Unterstützt Hotpatch**: Windows Server-Azure-Editionen bieten Unterstützung für Hotpatches. Weitere Informationen finden Sie unter [Hotpatch](../automanage/automanage-hotpatch.md).

- **Unterstützt Erweiterungen**: Aktivieren Sie diese Eigenschaft, wenn die Images in diesem Plan Erweiterungen unterstützen. Erweiterungen sind kleine Anwendungen, die Konfiguration und Automatisierung für virtuelle Azure-Computer nach der Bereitstellung bereitstellen. Erfahren Sie mehr über [Azure-VM-Erweiterungen](./azure-vm-create-certification-faq.yml#vm-extensions).

- **Ist ein virtuelles Netzwerkgerät**: Aktivieren Sie diese Eigenschaft, wenn es sich bei diesem Produkt um ein virtuelles Netzwerkgerät handelt. Ein virtuelles Netzwerkgerät ist ein Produkt, das eine oder mehrere Netzwerkfunktionen ausführt und z. B. als Lastenausgleich, VPN-Gateway, Firewall oder Anwendungsgateway genutzt wird. Erfahren Sie mehr über [virtuelle Netzwerkgeräte](https://go.microsoft.com/fwlink/?linkid=2155373).

- **Remotedesktop oder SSH deaktiviert**: Aktivieren Sie diese Eigenschaft, wenn mit diesen Images bereitgestellte VMs Kunden keinen Zugriff über Remotedesktop oder SSH gestatten. Erfahren Sie mehr über die [gesperrte VM-Images](./azure-vm-create-certification-faq.yml#locked-down-or-ssh-disabled-offer).

- **Erfordert eine benutzerdefinierte ARM-Vorlage für die Bereitstellung**: Aktivieren Sie diese Eigenschaft, wenn die Images in diesem Plan nur mithilfe einer benutzerdefinierten ARM-Vorlage bereitgestellt werden können. Weitere Informationen finden Sie im [Abschnitt „Benutzerdefinierte Vorlagen“ des Artikels „Problembehandlung bei der Zertifizierung von virtuellen Computern“](./azure-vm-create-certification-faq.yml#custom-templates).

### <a name="generations"></a>Generationen

Beim Erstellen einer VM wird die verwendete virtuelle Hardware definiert. Basierend auf den Anforderungen Ihres Kunden können Sie VMs der Generation 1, der Generation 2 oder beider Generationen veröffentlichen.

1. Wenn Sie ein neues Angebot erstellen, wählen Sie einen **Generationstyp** aus, und geben Sie die gewünschten Details ein:

    :::image type="content" source="./media/create-vm/azure-vm-generations-image-details-1.png" alt-text="Ansicht des Abschnitts mit Generationsdetail in Partner Center":::

2. Wenn Sie dem Plan eine weitere Generation hinzufügen möchten, wählen Sie **Generation hinzufügen** aus...

    :::image type="content" source="./media/create-vm/azure-vm-generations-add.png" alt-text="Ansicht des Links „Generation hinzufügen“":::

    ...und geben Sie die gewünschten Details ein:

    :::image type="content" source="./media/create-vm/azure-vm-generations-image-details-3.png" alt-text="Ansicht des Fensters mit Details zur Generation":::

<!--    The **Generation ID** you choose will be visible to customers in places such as product URLs and ARM templates (if applicable). Use only lowercase, alphanumeric characters, dashes, or underscores; it cannot be modified once published.
-->
3. Wenn Sie eine vorhandene VM, die bereits mit Generation 1 veröffentlicht wurde, aktualisieren möchten, bearbeiten Sie die Details auf der Seite **Technische Konfiguration**.

Weitere Informationen zu den Unterschieden zwischen den Funktionen von Generation 1 und Generation 2 finden Sie unter [Unterstützung für VMs der Generation 2 in Azure](../virtual-machines/generation-2.md).

> [!NOTE]
> Für eine veröffentlichte Generation ist mindestens eine Imageversion erforderlich, damit sie für Kunden verfügbar bleibt. Wenn Sie den gesamten Plan (einschließlich aller Generationen und Images) entfernen möchten, wählen Sie auf der Seite **Planübersicht** die Option **Plan als veraltet kennzeichnen** aus. Informationen hierzu finden Sie im ersten Abschnitt dieses Artikels.

### <a name="vm-images"></a>VM-Images

Geben Sie eine Datenträgerversion und den SAS-URI (Shared Access Signature) für die VM-Images an. Fügen Sie bis zu 16 Datenträger für jedes VM-Image hinzu. Geben Sie nur eine neue Imageversion pro Plan in einer bestimmten Übermittlung an. Nachdem ein Image veröffentlicht wurde, können Sie es nicht mehr bearbeiten, Sie können es jedoch löschen. Das Löschen einer Version verhindert, dass neue und vorhandene Benutzer eine neue Instanz der gelöschten Version bereitstellen.

Diese beiden erforderlichen Felder werden in der obigen Abbildung angezeigt:

- **Datenträgerversion**: Die Version des Images, das Sie bereitstellen.
- **Link zu Betriebssystem-VHD:** Das im Katalog mit freigegebenen Azure-Images gespeicherte Image. Erfahren Sie, wie Sie Ihr Image in einem [Katalog mit freigegebenen Images](azure-vm-create-using-approved-base.md#capture-image) erfassen.

Datenträger (wählen Sie **Datenträger hinzufügen (max. 16)** aus) sind ebenfalls VHD-SAS-URIs, die in den zugehörigen Azure Storage-Konten gespeichert sind. Fügen Sie einem Plan pro Übermittlung nur ein Image hinzu.

Fügen Sie unabhängig vom verwendeten Betriebssystem immer nur die für die Lösung erforderliche Mindestanzahl von Datenträgern für Daten hinzu. Bei der Bereitstellung können Kunden Datenträger, die Teil eines Images sind, nicht entfernen. Sie haben aber immer die Möglichkeit, während oder nach der Bereitstellung Datenträger hinzuzufügen.

> [!NOTE]
> Wenn Sie Ihre Images mit einer SAS bereitstellen und über Datenträger verfügen, müssen Sie diese ebenfalls als SAS-URI bereitstellen. Wenn Sie freigegebene Images verwenden, werden diese als Teil Ihres Images in Shared Image Gallery erfasst. Nachdem Ihr Angebot in Azure Marketplace veröffentlicht wurde, können Sie das Image aus Ihrem Azure-Speicher oder Ihrem Katalog mit freigegebenen Images löschen.

Wählen Sie **Entwurf speichern** und dann oben links **Planübersicht** aus, um den soeben erstellten Plan anzuzeigen.

Nach der Veröffentlichung des VM-Images können Sie das Image aus Ihrem Azure-Speicher löschen.

## <a name="next-steps"></a>Nächste Schritte

- [Verkaufen über CSPs](azure-vm-create-resell-csp.md)
---
title: Bereitstellen von SAP S/4HANA oder BW/4HANA auf einem virtuellen Azure-Computer | Microsoft-Dokumentation
description: Bereitstellen von SAP S/4HANA oder BW/4HANA auf einem virtuellen Azure-Computer
services: virtual-machines-linux
documentationcenter: ''
author: hobru
manager: timlt
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 44bbd2b6-a376-4b5c-b824-e76917117fa9
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/26/2021
ms.author: hobruche
ms.openlocfilehash: 0fff7d57e8a7dfa312b16accd15ef5990cdedf15
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131853622"
---
# <a name="sap-cloud-appliance-library"></a>SAP Cloud Appliance Library

Mit der [SAP Cloud Appliance Library](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) können Sie schnell eine Demoumgebung mit einem vollständig vorkonfigurierten SAP-System erstellen. Mit wenigen Klicks können Sie Ihr SAP-System einrichten und ausführen. Die folgenden Links zeigen verschiedene Lösungen, die Sie schnell in Azure bereitstellen können. Klicken Sie einfach auf den Link „Instanz erstellen“. 

Sie müssen sich mit Ihrer S-User- oder P-User-Benutzerkennung authentifizieren. Sie können eine kostenlose P-User-Benutzerkennung über die [SAP-Community](https://community.sap.com/) erstellen.  Weitere Informationen finden Sie weiter unten.

| Lösung | Link |
| -------------- | :--------- | 
| **SAP S/4HANA 2020 FPS02, Fully-Activated Appliance** 27. April 2021 | [Create Instance (Instanz erstellen)](https://cal.sap.com/registration?sguid=d48af08b-e2c6-4409-82f8-e42d5610e918&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) |
|Diese Appliance enthält SAP S/4HANA 2020 (FPS02) mit vorab aktivierten SAP Best Practices für SAP S/4HANA-Kernfunktionen sowie weitere Szenarios für Service, Master Data Governance (MDG), Transportation Mgmt. (TM), Portfolio Mgmt. (PPM), Human Capital Management (HCM), Analytics, Migration Cockpit und mehr. Der Benutzerzugriff erfolgt über SAP Fiori, SAP GUI, SAP HANA Studio, Windows-Remotedesktop oder das Back-End-Betriebssystem für vollständigen Administratorzugriff. |  [Details]( https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/solutions/d48af08b-e2c6-4409-82f8-e42d5610e918) |
| **SAP S/4HANA 2020 FPS01, Fully-Activated Appliance** 20. April 2021 | [Create Instance (Instanz erstellen)](https://cal.sap.com/registration?sguid=a0b63a18-0fd3-4d88-bbb9-4f02c13dc343&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) |
|Diese Appliance enthält SAP S/4HANA 2020 (FPS01) mit vorab aktivierten SAP Best Practices für SAP S/4HANA-Kernfunktionen sowie weitere Szenarios für Service, Master Data Governance (MDG), Transportation Mgmt. (TM), Portfolio Mgmt. (PPM), Human Capital Management (HCM), Analytics, Migration Cockpit und mehr. Der Benutzerzugriff erfolgt über SAP Fiori, SAP GUI, SAP HANA Studio, Windows-Remotedesktop oder das Back-End-Betriebssystem für vollständigen Administratorzugriff. |  [Details](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/solutions/a0b63a18-0fd3-4d88-bbb9-4f02c13dc343) | 
| **SAP S/4HANA 2020 FPS02** 10. Juni 2021 | [Create Instance (Instanz erstellen)](https://cal.sap.com/registration?sguid=c7cff775-cbf7-4cd1-a907-6eeca95a0946&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) |
| Diese Lösung ist eine S/4HANA-Standardsysteminstallation, die einen Remotedesktop für einfachen Front-End-Zugriff umfasst. Sie enthält eine vorkonfigurierte und aktivierte SAP S/4HANA Fiori-Benutzeroberfläche im Client 100, bei der die erforderlichen Komponenten entsprechend dem SAP-Hinweis „3045635 Rapid Activation for SAP Fiori in SAP S/4HANA 2020 FPS02“ aktiviert sind. Siehe Link für weitere Informationen. | [Details](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/solutions/c7cff775-cbf7-4cd1-a907-6eeca95a0946) | 
| **IDES EHP8 FOR SAP ERP 6.0 für SAP ASE, Juni 2021** 10. Juni 2021  | [Create Instance (Instanz erstellen)](https://cal.sap.com/registration?sguid=ed55a454-0b10-47c5-8644-475ecb8988a0&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) |
|IDES-Systeme sind Kopien der SAP-internen Demosysteme und werden als Playground zum Anpassen und Testen verwendet. Dieses IDES-System kann speziell als Quellsystem in den Datenmigrationsszenarien der SAP S/4HANA Fully-Activated Appliance (2020 FPS01 und höher) verwendet werden. Darüber hinaus enthält es standardbasierte Geschäftsszenarien, die auf vordefinierten Master- und Transaktionsdaten basieren. |  [Details](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/solutions/ed55a454-0b10-47c5-8644-475ecb8988a0) |
| **SAP BW/4HANA 2.0 SP07 einschließlich BW/4HANA Content 2.0 SP06** 24. Februar 2020 | [Create Instance (Instanz erstellen)](https://cal.sap.com/registration?sguid=0f2f20f4-d012-4f76-81af-6ff15063db66&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) |
|Diese Lösung bietet Ihnen einen Einblick in SAP BW/4HANA. SAP BW/4HANA ist die Data Warehouse-Lösung der nächsten Generation, die für HANA optimiert wurde. Neben den grundlegenden BW/4HANA-Optionen bietet die Lösung eine Reihe von HANA-optimierten BW/4HANA-Inhalten und Hybridszenarios der nächsten Generation mit der SAP Data Warehouse Cloud. Da das System vorkonfiguriert ist, können Sie direkt mit der Implementierung Ihrer Szenarios beginnen.| [Details](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/solutions/0f2f20f4-d012-4f76-81af-6ff15063db66) | 
| **SAP Business One 10.0 PL02, Version für SAP HANA** 4. August 2020  | [Create Instance (Instanz erstellen)](https://cal.sap.com/registration?sguid=371edc8c-56c6-4d21-acb4-2d734722c712&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) |
|Sap Business One, das von über 70.000 kleinen und mittelgroßen Unternehmen in mehr als 170 Ländern Vertrauen genießt, ist eine flexible, kostengünstige und skalierbare ERP-Lösung auf Basis von SAP HANA. Die Lösung ist mit einer 31-Tage-Testlizenz vorkonfiguriert und verfügt über eine Demodatenbank Ihrer Wahl, die vorinstalliert ist. Informationen zum Umfang der Lösung und zum einfachen Hinzufügen neuer Demodatenbanken finden Sie im Leitfaden zu den ersten Schritte. |  [Details](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/solutions/371edc8c-56c6-4d21-acb4-2d734722c712) |
| **Information Detector für SAP Data Custodian v2106** 30. August 2021  | [Create Instance (Instanz erstellen)](https://cal.sap.com/registration?sguid=db44680c-8a2a-405d-8963-838db38fa7dd&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) |
|Der Information Detector für SAP Data Custodian kann verwendet werden, um die Datenbeschriftung von Cloudressourcen zu automatisieren. Informationsdetektoren durchsuchen Ihre Infrastrukturressourcen und bestimmen, ob sie bestimmte Arten von Informationen enthalten. |  [Details](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/solutions/db44680c-8a2a-405d-8963-838db38fa7dd) |
| **SAP Yard Logistics 2009 für SAP S/4HANA** 28. Juli 2021 | [Create Instance (Instanz erstellen)](https://cal.sap.com/registration?sguid=9cdf4f13-73a5-4743-a213-82e0d1a68742&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) |
|Gestalten Sie Ihre Lieferkettenlogistik effizienter und profitabler mit der SAP Yard Logistics-Anwendung. Maximieren Sie mit einer Reihe von Visualisierungs- und Berichterstellungstools den Einblick in alle Yard-Prozesse, und zeigen Sie eine Vorschau der geplanten Workloads an, um die Ressourcennutzung zu optimieren und die Planung, Ausführung und Abrechnung mit einem einzigen System zu unterstützen.|  [Details](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/solutions/9cdf4f13-73a5-4743-a213-82e0d1a68742) | 
| **SAP S/4HANA 2020 FPS02, Fully-Activated Appliance** 27. April 2021 | [Create Instance (Instanz erstellen)](https://cal.sap.com/registration?sguid=d48af08b-e2c6-4409-82f8-e42d5610e918&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) | 
|Diese Lösung ist eine S/4HANA-Standardsysteminstallation, die einen Remotedesktop für einfachen Front-End-Zugriff umfasst. Sie enthält eine vorkonfigurierte und aktivierte SAP S/4HANA Fiori-Benutzeroberfläche im Client 100, bei der die erforderlichen Komponenten entsprechend dem SAP-Hinweis „3045635 Rapid Activation for SAP Fiori in SAP S/4HANA 2020 FPS02“ aktiviert sind. Siehe Link für weitere Informationen.| [Details](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/solutions/d48af08b-e2c6-4409-82f8-e42d5610e918) | 
| **SAP Focused Run 3.0 FP01, nicht konfiguriert** 21. Juli 2021 | [Create Instance (Instanz erstellen)](https://cal.sap.com/registration?sguid=82bdb96e-3578-41aa-a3e1-a6d9a8335ae1&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) |
|SAP Focused Run ist speziell für Unternehmen konzipiert, die eine Lösung für die Überwachung, Warnungserstellung und Analyse von komplexen Systemen und Anwendungen benötigen. Die leistungsstarke Lösung unterstützt Dienstanbieter, die alle Kunden in einer zentralen, skalierbaren, sicheren und automatisierten Umgebung hosten möchten. Sie richtet sich auch an Kunden mit spezifischen Anforderungen in Bezug auf Systemverwaltung, Benutzerüberwachung, Integrationsüberwachung sowie Konfigurations- und Sicherheitsanalyse.|  [Details](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/solutions/82bdb96e-3578-41aa-a3e1-a6d9a8335ae1) | 
| **SAP S/4HANA 2020 FPS01 Utilities Testversion** 21. Juli 2021 | [Create Instance (Instanz erstellen)](https://cal.sap.com/registration?sguid=68785eeb-a228-4aa8-8273-b4c30775590c&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) |
|Mit dieser Lösung können Sie Ihr eigenes SAP S/4HANA 2020 Utilities-System erstellen und praktische Erfahrungen sammeln, wobei Sie vollständigen Administratorzugriff erhalten. Verschiedene Einführungen helfen Ihnen, sich mit der optimierten Verarbeitung von Messungsdaten, den optimierten Abrechnungsprozess über rollenbasierte FIORI-Benutzeroberflächen und das branchenspezifische Kundenserviceverhalten im Hinblick auf die Kundenbindung vertraut zu machen.|  [Details](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/solutions/68785eeb-a228-4aa8-8273-b4c30775590c)| 
| **SAP Product Lifecycle Costing 4.0 SP3 Hotfix 2** 1. August 2021 | [Create Instance (Instanz erstellen)](https://cal.sap.com/registration?sguid=f2bf191a-7efc-48a2-b8ac-51756eb225bc&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) |
|SAP Product Lifecycle Costing ist eine Lösung zum Berechnen von Kosten und anderen Kennzahlen für neue Produkte oder produktbezogene Angebote in einer frühen Phase des Produktlebenszyklus, um Kostenfaktoren schnell zu identifizieren und Alternativen einfach zu simulieren und zu vergleichen.|  [Details](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/solutions/f2bf191a-7efc-48a2-b8ac-51756eb225bc)|
| **SAP ABAP Platform 1909, Developer Edition** 21. Juni 2021  | [Create Instance (Instanz erstellen)](https://cal.sap.com/registration?sguid=7bd4548f-a95b-4ee9-910a-08c74b4f6c37&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) |
|SAP ABAP Platform für SAP HANA bietet Ihnen Zugriff auf SAP ABAP Platform 1909 Developer Edition für SAP HANA. Beachten Sie, dass diese Lösung mit vielen zusätzlichen Elementen vorkonfiguriert ist, darunter SAP ABAP RESTful Anwendungsprogrammiermodell, SAP Fiori-Launchpad, SAP gCTS, SAP ABAP Test Cockpit und vorkonfigurierte Front-End-/Back-End-Verbindungen usw. Sie umfasst auch die gesamte Standard-ABAP-Infrastruktur: Transaktionsverwaltung, Datenbankvorgänge/Persistenz, Änderungs- und Transportsystem, SAP Gateway, Interoperabilität mit ABAP Development Toolkit und SAP WebIDE und vieles mehr. |  [Details](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/solutions/7bd4548f-a95b-4ee9-910a-08c74b4f6c37) |
| **1: SAP ERP-Quellsystem (openSAP)** 17. September 2021 | [Create Instance (Instanz erstellen)](https://cal.sap.com/registration?sguid=1a3556c0-0ee1-4a4c-8a5a-db08173df293&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) |
|Lösung 1 für die Durchführung einer Systemkonvertierung aus SAP ERP in SAP S/4HANA-Anfangsstatus. Die Lösung wurde getestet und für die Konvertierung von SAP EHP6 für SAP ERP 6.0 SPS13 in SAP S/4HANA 2020 FPS00 vorbereitet. |  [Details]( https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/solutions/1a3556c0-0ee1-4a4c-8a5a-db08173df293) |
| **2: SAP ERP-Quellsystem nach den Vorbereitungsschritten vor dem Ausführen des Softwareupdate-Managers (openSAP)** 17. Oktober 2021 | [Create Instance (Instanz erstellen)](https://cal.sap.com/registration?sguid=5eb92a4d-a704-48b8-b060-0647c63b667c&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) |
|Lösung 2 für die Durchführung einer Systemkonvertierung aus SAP ERP in SAP S/4HANA nach Vorbereitungsschritten vor dem Ausführen des Softwareupdate-Managers. Die Lösung wurde getestet und für die Konvertierung aus SAP EHP6 für SAP ERP 6.0 SPS13 in SAP S/4HANA 2020 FPS00 vorbereitet. |  [Details]( https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/solutions/5eb92a4d-a704-48b8-b060-0647c63b667c) |
| **3. SAP S/4HANA-Zielsystem nach der technischen Konvertierung vor zusätzlicher Konfiguration** 22. September 2021  | [Create Instance (Instanz erstellen)](https://cal.sap.com/registration?sguid=4336a3fb-2fc9-4a93-9500-c65101ffc9d7&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) |
|Lösung 3 nach einer technischen Systemkonvertierung aus SAP ERP in SAP S/4HANA vor zusätzlicher Konfiguration. Die Lösung wurde getestet und als konvertiert aus SAP EHP6 für SAP ERP 6.0 SPS13 in SAP S/4HANA 2020 FPS00 vorbereitet. |  [Details](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/solutions/4336a3fb-2fc9-4a93-9500-c65101ffc9d7) |
| **4: SAP S/4HANA-Zielsystem mit zusätzlicher Konfiguration (openSAP)** 17. Oktober 2021 | [Create Instance (Instanz erstellen)](https://cal.sap.com/registration?sguid=f48f2b77-389f-488b-be2b-1c14a86b2e69&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) |
|Lösung 4 nach einer technischen Systemkonvertierung aus SAP ERP in SAP S/4HANA einschließlich zusätzlicher Konfiguration. Die Lösung wurde getestet und als konvertiert aus SAP EHP6 für SAP ERP 6.0 SPS13 in SAP S/4HANA 2020 FPS00 vorbereitet. |  [Details]( https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/solutions/f48f2b77-389f-488b-be2b-1c14a86b2e69) |
 




---







## <a name="setup-and-get-started-with-sap-cloud-appliance-library"></a>Einrichten und erste Schritte mit der SAP Cloud Appliance Library

> [!NOTE]
> Weitere Informationen zur SAP Cloud Appliance Library finden Sie auf der Website [SAP Cloud Appliance Library](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8). SAP bietet auch einen Blog zur [SAP Cloud Appliance Library 3.0](http://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience).

> [!NOTE]
> Ab dem 29. Mai 2017 können Sie das Azure Resource Manager-Bereitstellungsmodell zusätzlich zum weniger bevorzugten klassischen Bereitstellungsmodell für die Bereitstellung der SAP CAL nutzen. Wir empfehlen allerdings, das neue Resource Manager-Bereitstellungsmodell zu verwenden und das klassische Bereitstellungsmodell außer Acht zu lassen.

## <a name="create-an-account-in-the-sap-cal"></a>Erstellen eines Kontos in der SAP CAL
1. Um sich erstmals bei der SAP CAL anzumelden, verwenden Sie Ihren SAP S-Benutzer oder einen anderen bei SAP registrierten Benutzer. Anschließend definieren Sie ein SAP CAL-Konto, das von der SAP CAL zum Bereitstellen von Appliances in Azure verwendet wird. Bei der Definition des Kontos müssen Sie die folgenden Schritte ausführen:

    a. Wählen Sie das Bereitstellungsmodell in Azure aus (Resource Manager oder klassisch).

    b. Geben Sie Ihr Azure-Abonnement ein. Ein SAP CAL-Konto kann nur einem Abonnement zugewiesen werden. Wenn Sie mehrere Abonnements benötigen, müssen Sie ein weiteres SAP CAL-Konto erstellen.

    c. Erteilen Sie der SAP CAL die Berechtigung zur Bereitstellung in Ihrem Azure-Abonnement.

    > [!NOTE]
    > Die nächsten Schritte zeigen, wie Sie ein SAP CAL-Konto für Resource Manager-Bereitstellungen erstellen. Wenn Sie bereits über ein SAP CAL-Konto verfügen, das mit dem klassischen Bereitstellungsmodell verknüpft ist, *müssen* Sie folgende Schritte zum Erstellen eines neuen SAP CAL-Kontos ausführen. Das neue SAP CAL-Konto muss im Resource Manager-Modell bereitgestellt werden.

2. Erstellen Sie ein neues SAP CAL-Konto. Die Seite **Konten** zeigt drei Optionen für Azure: 

    a. **Microsoft Azure (klassisch)** ist das klassische Bereitstellungsmodell, das nicht mehr empfohlen wird.

    b. **Microsoft Azure** ist das neue Bereitstellungsmodell mit dem Ressourcen-Manager.

    c. **Windows Azure bei Betrieb über 21Vianet** ist eine Option in China, die das klassische Bereitstellungsmodell verwendet.

    Wählen Sie zum Bereitstellen im Resource Manager-Modell **Microsoft Azure** aus.

    ![SAP CAL-Kontodetails](./media/cal-s4h/s4h-pic-2a.png)

3. Geben Sie die Azure-**Abonnement-ID** ein, die Sie im Azure-Portal finden.

   ![SAP CAL-Konten](./media/cal-s4h/s4h-pic3c.png)

4. Um die SAP CAL für die Bereitstellung im angegebenen Azure-Abonnement zu autorisieren, klicken Sie auf **Authorize** (Autorisieren). Die folgende Seite wird auf der Browserregisterkarte angezeigt:

   ![Anmeldung beim Clouddienst in Internet Explorer](./media/cal-s4h/s4h-pic4c.png)

5. Wenn mehrere Benutzer aufgeführt sind, wählen Sie das Microsoft-Konto, das als Co-Administrator mit dem ausgewählten Azure-Abonnement verknüpft ist. Die folgende Seite wird auf der Browserregisterkarte angezeigt:

   ![Bestätigung für Clouddienste in Internet Explorer](./media/cal-s4h/s4h-pic5a.png)

6. Klicken Sie auf **Annehmen**. Wenn die Autorisierung erfolgreich ist, wird die Definition des SAP CAL-Kontos erneut angezeigt. Nach kurzer Zeit bestätigt eine Meldung, dass die Autorisierung erfolgreich war.

7. Um Ihrem Benutzer das neu erstellte SAP CAL-Konto zuzuweisen, geben Sie Ihre **Benutzer-ID** in das Textfeld rechts ein, und klicken dann auf **Add** (Hinzufügen).

   ![Zuordnen des Kontos zum Benutzer](./media/cal-s4h/s4h-pic8a.png)

8. Um Ihr Konto dem Benutzer zuzuordnen, der sich bei der SAP CAL anmeldet, klicken Sie auf **Review** (Überprüfen). 
 
9. Um die Zuordnung zwischen dem Benutzer und dem neu erstellten SAP CAL-Konto zu erstellen, klicken Sie auf **Create** (Erstellen).

   ![Zuordnung des SAP CAL-Kontos zum Benutzer](./media/cal-s4h/s4h-pic9b.png)

Sie haben erfolgreich ein SAP CAL-Konto mit den folgenden Möglichkeiten erstellt:

- Verwendung des Resource Manager-Bereitstellungsmodells
- Bereitstellung von SAP-Systemen in Ihrem Azure-Abonnement

Nun können Sie beginnen, S/4HANA in Ihrem Benutzerabonnement in Azure bereitzustellen.

> [!NOTE]
> Überprüfen Sie, ob Sie über die erforderlichen Azure-Kernkontingente verfügen, bevor Sie fortfahren. In einigen Lösungen verwendet die SAP CAL VMs der M-Serie von Azure, um einige der SAP HANA-basierten Lösungen bereitzustellen. Ihr Azure-Abonnement verfügt möglicherweise nicht über Kernkontingente für die M-Serie. Wenden Sie sich in diesem Falle ggf. an den Azure-Support, um das erforderliche Kontingent zu erhalten.

> [!NOTE]
> Wenn Sie eine Lösung in Azure in der SAP CAL bereitstellen, stellen Sie möglicherweise fest, dass Sie nur eine Azure-Region auswählen können. Für eine Bereitstellung in Azure-Regionen, die nicht der von der SAP CAL vorgeschlagenen Region entsprechen, müssen Sie bei SAP ein CAL-Abonnement erwerben. Sie müssen ggf. auch in einer Nachricht SAP bitten, Ihre CAL so zu aktivieren, dass eine Bereitstellung in Azure-Regionen möglich wird, die sich von den anfänglich vorgeschlagenen unterscheidet.

## <a name="deploy-a-solution"></a>Bereitstellen einer Lösung

Lassen Sie uns auf der Seite **Lösungen** der SAP CAL eine Lösung bereitstellen. Die SAP CAL weist für die Bereitstellung zwei Sequenzen auf:

- Eine Standardsequenz, bei der eine Seite genutzt wird, um das bereitzustellende System zu definieren
- Eine erweiterte Sequenz, die Ihnen eine gewisse Auswahl bei VM-Größen bietet 

Wir veranschaulichen nun die grundlegende Vorgehensweise zur Bereitstellung.

1. Führen Sie auf der Seite **Kontodetails** die folgenden Schritte aus:

    a. Wählen Sie ein SAP CAL-Konto aus. (Wählen Sie ein Konto, das für eine Bereitstellung gemäß dem Ressourcen-Manager-Bereitstellungsmodell vorgesehen ist.)

    b. Geben Sie einen **Namen** für die Instanz ein.

    c. Wählen Sie eine Azure-**Region** aus. Die SAP CAL schlägt eine Region vor. Wenn Sie eine andere Azure-Region benötigen und über kein SAP CAL-Abonnement verfügen, müssen Sie bei SAP ein CAL-Abonnement bestellen.

    d. Geben Sie für die Lösung ein **Masterkennwort** mit acht oder neun Zeichen ein. Das Kennwort wird von den Administratoren der verschiedenen Komponenten verwendet.

   ![Einfacher SAP CAL-Modus: Instanz erstellen](./media/cal-s4h/s4h-pic10a.png)

2. Klicken Sie auf **Erstellen** und im angezeigten Meldungsfeld auf **OK**.

   ![SAP CAL: Unterstützte VM-Größen](./media/cal-s4h/s4h-pic10b.png)

3. Klicken Sie im Dialogfeld **Privater Schlüssel** auf **Speichern**, um den privaten Schlüssel in der SAP CAL zu speichern. Klicken Sie zur Verwendung des Kennwortschutzes für den privaten Schlüssel auf **Herunterladen**. 

   ![SAP CAL: Privater Schlüssel](./media/cal-s4h/s4h-pic10c.png)

4. Lesen die SAP CAL-**Warnmeldung**, und klicken Sie auf **OK**.

   ![SAP CAL-Warnung](./media/cal-s4h/s4h-pic10d.png)

    Nun kann die Bereitstellung erfolgen. Nach einiger Zeit wird je nach Größe und Komplexität der Lösung (die SAP CAL zeigt eine Schätzung an) der Status als aktiv und betriebsbereit angezeigt.

5. Um die virtuellen Computer zu finden, die mit den anderen zugehörigen Ressourcen in einer Ressourcengruppe gesammelt wurden, wechseln Sie zum Azure-Portal: 

   ![Im neuen Portal angezeigte SAP CAL-Objekte](./media/cal-s4h/sapcaldeplyment_portalview.png)

6. Im SAP CAL-Portal wird der Status als **Aktiv** angezeigt. Klicken Sie zum Verbinden mit der Lösung auf **Verbinden**. Diese Lösung bietet verschiedene Optionen zum Herstellen von Verbindungen mit verschiedenen Komponenten.

   ![SAP CAL-Instanzen](./media/cal-s4h/active_solution.png)

7. Bevor Sie eine der Optionen zum Herstellen einer Verbindung mit den bereitgestellten Systemen nutzen können, klicken Sie auf **Getting Started Guide** (Leitfaden für erste Schritte). 

   ![Herstellen einer Verbindung mit der Instanz](./media/cal-s4h/connect_to_solution.png)

    Die Dokumentation nennt die Benutzer für jede der Verbindungsmethoden. Die Kennwörter für diese Benutzer sind auf das Masterkennwort festgelegt, das Sie am Anfang des Bereitstellungsprozesses festgelegt haben. In der Dokumentation werden weitere Funktionsbenutzer mit ihren Kennwörtern aufgeführt, die Sie zum Anmelden beim bereitgestellten System verwenden können. 

    Wenn Sie beispielsweise die SAP GUI verwenden, die auf dem Windows-Remotedesktop-Computer vorinstalliert ist, sieht das S/4-System in etwa so aus:

   ![SM50 auf der vorinstallierten SAP GUI](./media/cal-s4h/gui_sm50.png)

    Wenn Sie DBACockpit verwenden, sieht die Instanz wie folgt aus:
 

   ![SM50 auf der SAP GUI von DBACockpit](./media/cal-s4h/dbacockpit.png)

Innerhalb weniger Stunden wird eine fehlerfreie S/4 SAP-Appliance in Azure bereitgestellt.

Wenn Sie ein SAP CAL-Abonnement erworben haben, werden Bereitstellungen über die SAP CAL in Azure vollständig von SAP unterstützt. Die Supportwarteschlange heißt BC-VCM-CAL.








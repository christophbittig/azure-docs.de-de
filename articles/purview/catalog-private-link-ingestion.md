---
title: Privates und sicheres Überprüfen Ihrer Datenquellen
description: In diesem Artikel wird beschrieben, wie Sie einen privaten Endpunkt einrichten können, um Datenquellen aus einem eingeschränkten Netzwerk zu überprüfen.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 08/18/2021
ms.openlocfilehash: cbdf2220d1b4087376bc40db5b7da167144e5d9b
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/31/2021
ms.locfileid: "123257196"
---
# <a name="scan-your-data-sources-privately-and-securely"></a>Privates und sicheres Überprüfen Ihrer Datenquellen

Wenn Sie planen, Ihre Datenquellen in privaten Netzwerken, virtuellen Netzwerken und hinter privaten Endpunkten zu überprüfen, müssen private Azure Purview-Erfassungsendpunkte bereitgestellt werden, um Netzwerkisolierung für Ihre Metadaten sicherzustellen, die von den Datenquellen, die überprüft werden, zu Azure Purview Data Map fließen.

Azure Purview kann Datenquellen in Azure oder in einer lokalen Umgebung mithilfe privater Endpunkte für die _Erfassung_ überprüfen. Drei Ressourcen für private Endpunkte müssen bereitgestellt und mit den verwalteten Azure Purview-Ressourcen verknüpft werden, wenn private Endpunkte für die Erfassung bereitgestellt werden:

- Der private Blobendpunkt ist mit einem verwalteten Azure Purview-Speicherkonto verknüpft.
- Der private Warteschlangenendpunkt ist mit einem verwalteten Azure Purview-Speicherkonto verknüpft.
- Der private Endpunktnamespace ist mit dem verwalteten Event-Hub-Namespace von Azure Purview verknüpft.

:::image type="content" source="media/catalog-private-link/purview-private-link-architecture-ingestion.png" alt-text="Abbildung, die die Architektur von Azure Purview und Private Link zeigt.":::

## <a name="deployment-checklist"></a>Bereitstellungsprüfliste
Aktivieren Sie mithilfe einer der Bereitstellungsoptionen aus diesem Leitfaden private Erfassungsendpunkte für Ihr Azure Purview-Konto:

1. Wählen Sie ein geeignetes virtuelles Azure-Netzwerk und ein Subnetz aus, um private Erfassungsendpunkte für Azure Purview bereitzustellen. Wählen Sie eine der folgenden Optionen aus:
   - Stellen Sie ein [neues virtuelles Netzwerk](../virtual-network/quick-create-portal.md) in Ihrem Azure-Abonnement bereit.
   - Suchen Sie ein vorhandenes virtuelles Azure-Netzwerk und ein Subnetz in Ihrem Azure-Abonnement.
  
2. Definieren Sie eine geeignete [DNS-Namensauflösungsmethode](./catalog-private-link-name-resolution.md#deployment-options) für die Erfassung, damit Azure Purview Datenquellen über ein privates Netzwerk überprüfen kann. Sie können auch eine der folgenden Optionen verwenden:
   - Stellen Sie neue Azure DNS-Zonen mithilfe der weiter unten in diesem Leitfaden beschriebenen Schritte bereit.
   - Fügen Sie den vorhandenen Azure DNS-Zonen die erforderlichen DNS-Einträge hinzu, indem Sie die weiter unten in diesem Leitfaden beschriebenen Schritte ausführen.
   - Fügen Sie nach Abschluss der Schritte in diesem Leitfaden den vorhandenen DNS-Servern die erforderlichen DNS-A-Einträge manuell hinzu.
3. Stellen Sie ein [neues Purview-Konto](#option-1---deploy-a-new-azure-purview-account-with-ingestion-private-endpoint) mit privaten Erfassungsendpunkten bereit, oder stellen Sie private Erfassungsendpunkte für ein [vorhandenes Purview-Konto](#option-2---enable-ingestion-private-endpoint-on-existing-azure-purview-accounts) bereit.
4. Stellen Sie eine [selbstgehostete Integration Runtime](#deploy-self-hosted-integration-runtime-ir-and-scan-your-data-sources) in demselben VNet bereit, in dem die privaten Endpunkte der Azure Purview-Erfassung bereitgestellt werden, und registrieren Sie sie.
5. Passen Sie nach Abschluss dieses Leitfadens bei Bedarf die DNS-Konfigurationen an.
6. Überprüfen Sie Ihre Netzwerk- und Namensauflösung zwischen dem Verwaltungscomputer, der selbstgehosteten IR auf dem virtuellen Computer und den Datenquellen in Azure Purview. 

## <a name="option-1---deploy-a-new-azure-purview-account-with-_ingestion_-private-endpoint"></a>Option 1: Bereitstellen eines neuen Azure Purview-Kontos mit privatem _Erfassungsendpunkt_

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com) und dann zur Seite **Purview-Konto**. Wählen Sie **+ Erstellen** aus, um ein neues Azure Purview-Konto zu erstellen.

2. Geben Sie die grundlegenden Informationen ein, und legen Sie auf der Registerkarte **Netzwerk** als Konnektivitätsmethode **Privater Endpunkt** fest. Legen Sie „Privaten Endpunkt aktivieren“ auf **Nur Erfassung** fest.

      :::image type="content" source="media/catalog-private-link/purview-pe-scenario-2-1.png" alt-text="Screenshot: Auswahl auf der Seite „Privaten Endpunkt erstellen“.":::

3. Richten Sie Ihre privaten Endpunkte für die Erfassung ein, indem Sie Details für die Elemente **Abonnement**, **Virtuelles Netzwerk** und **Subnetz** eingeben, die mit Ihrem privaten Endpunkt verbunden werden sollen.

4. Wählen Sie optional **Private DNS-Integration** aus, um private DNS-Zonen von Azure zu verwenden.
   
   > [!IMPORTANT]
   > Es ist wichtig, die richtigen privaten DNS-Zonen von Azure auszuwählen, um eine ordnungsgemäße Namensauflösung zwischen Azure Purview und den Datenquellen zu ermöglichen. Sie können auch Ihre vorhandenen privaten DNS-Zonen von Azure verwenden oder die DNS-Einträge später manuell in Ihren DNS-Servern erstellen. Weitere Informationen finden Sie unter [Konfigurieren von DNS-Namensauflösung für private Endpunkte](./catalog-private-link-name-resolution.md)

5.  Klicken Sie auf **Überprüfen + erstellen**. Auf der Seite **Überprüfen + Erstellen** überprüft Azure Ihre Konfiguration.

6.  Wenn die Meldung „Überprüfung erfolgreich“ angezeigt wird, wählen Sie **Erstellen** aus.

    :::image type="content" source="media/catalog-private-link/validation-passed.png" alt-text="Screenshot: Die Überprüfung für die Kontoerstellung war erfolgreich.":::

## <a name="option-2---enable-_ingestion_-private-endpoint-on-existing-azure-purview-accounts"></a>Option 2: Aktivieren der privaten Endpunkte für die _Erfassung_ in vorhandenen Azure Purview-Konten

1.  Navigieren Sie zum [Azure-Portal](https://portal.azure.com). Klicken Sie auf Ihr Azure Purview-Konto und wählen Sie unter **Einstellungen** **Netzwerk** die Option **Private Erfassungsendpunktverbindungen** aus.

2. Wählen Sie unter „Private Erfassungsendpunktverbindungen“ **+ Neu** aus, um einen neuen privaten Endpunkt für die Erfassung zu erstellen.

3. Geben Sie die grundlegenden Informationen ein, und wählen Sie Ihr vorhandenes virtuelles Netzwerk und ein Subnetz aus. Wählen Sie optional **Private DNS-Integration** aus, um private DNS-Zonen von Azure zu verwenden. 
   
   :::image type="content" source="media/catalog-private-link/ingestion-pe-fill-details.png" alt-text="Screenshot: Ausfüllen der Details des privaten Endpunkts.":::
   
   > [!IMPORTANT]
   > Es ist wichtig, die richtigen privaten DNS-Zonen von Azure auszuwählen, um eine ordnungsgemäße Namensauflösung zwischen Azure Purview und den Datenquellen zu ermöglichen. Sie können auch Ihre vorhandenen privaten DNS-Zonen von Azure verwenden oder die DNS-Einträge später manuell in Ihren DNS-Servern erstellen. 
   > 
   >Weitere Informationen finden Sie unter [Konfigurieren privater Endpunkte für die DNS-Namensauflösung](./catalog-private-link-name-resolution.md).


4. Wählen Sie **Erstellen** aus, um die Einrichtung abzuschließen.

> [!NOTE]
> Private Endpunkte für die Erfassung können nur über die oben beschriebene Benutzeroberfläche im Azure Purview-Portal erstellt werden. Sie können nicht über Private Link Center erstellt werden.

## <a name="deploy-self-hosted-integration-runtime-ir-and-scan-your-data-sources"></a>Stellen Sie die selbstgehostete Integration Runtime (IR) bereit, und überprüfen Sie Ihre Datenquellen.
Nachdem Sie die privaten Erfassungsendpunkte für Azure Purview bereitgestellt haben, müssen Sie mindestens eine selbstgehostete Integration Runtime (IR) einrichten und registrieren:

- Alle lokalen Quelltypen wie z. B. Microsoft SQL Server, Oracle oder SAP werden derzeit nur über Überprüfungen mit einer selbstgehosteten Integration Runtime (IR) unterstützt. Die selbstgehostete IR muss innerhalb Ihres privaten Netzwerks ausgeführt und mit Ihrem virtuellen Netzwerk in Azure durch Peering verbunden werden. 
   
- Für alle Azure-Quelltypen wie Azure Blob Storage und Azure SQL-Datenbank müssen Sie die Überprüfung explizit mithilfe einer selbstgehosteten Integration Runtime ausführen, die in demselben VNet wie der private Azure Purview-Erfassungsendpunkt bereitgestellt wird. 

Führen Sie die Schritte unter [Erstellen und Verwalten einer selbstgehosteten Integration Runtime](manage-integration-runtimes.md), um eine selbstgehostete IR einzurichten. Richten Sie anschließend die Überprüfung für die Azure-Quelle ein, indem Sie in der Dropdownliste **Verbindung über Integration Runtime herstellen** Ihre selbstgehostete IR auswählen, um Netzwerkisolation sicherzustellen.
    
   :::image type="content" source="media/catalog-private-link/shir-for-azure.png" alt-text="Screenshot: Ausführen einer Azure-Überprüfung mithilfe einer selbstgehosteten IR.":::

> [!IMPORTANT]
> Wenn Sie Ihr Azure Purview-Konto nach dem 18. August 2021 erstellt haben, stellen Sie sicher, dass Sie die neueste Version der selbstgehosteten Integration Runtime aus dem [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=39717) herunterladen und installieren.
> 
## <a name="next-steps"></a>Nächste Schritte

-  [Überprüfen der Auflösung für private Endpunkte](./catalog-private-link-name-resolution.md)
-  [Verwalten von Datenquellen in Azure Purview](./manage-data-sources.md)
-  [Problembehandlung bei der Konfiguration privater Endpunkte für Ihr Azure Purview-Konto](./catalog-private-link-troubleshoot.md)

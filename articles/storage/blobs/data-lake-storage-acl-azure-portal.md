---
title: Verwenden des Azure-Portals zum Verwalten von Zugriffssteuerungslisten in Azure Data Lake Storage Gen2
description: Verwenden Sie das Azure-Portal zum Verwalten von Zugriffssteuerungslisten in Speicherkonten, für die hierarchische Namespaces aktiviert sind.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 04/15/2021
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: bb9404351142e60e242e1e4181961c1ed4609012
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128564976"
---
# <a name="use-the-azure-portal-to-manage-acls-in-azure-data-lake-storage-gen2"></a>Verwenden des Azure-Portals zum Verwalten von Zugriffssteuerungslisten in Azure Data Lake Storage Gen2

In diesem Artikel erfahren Sie, wie Sie das [Azure-Portal](https://ms.portal.azure.com/) verwenden, um die Zugriffssteuerungsliste (ACL) eines Verzeichnisses oder Blobs in Speicherkonten zu verwalten, für die das Feature für hierarchische Namespaces aktiviert ist.

Informationen zur Struktur der Zugriffssteuerungsliste finden Sie unter [Zugriffssteuerungslisten in Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

Informationen über die Verwendung von Zugriffssteuerungslisten mit Azure-Rollen finden Sie unter [Zugriffssteuerungsmodell in Azure Data Lake Storage Gen2](data-lake-storage-access-control-model.md).

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Abonnement. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).

- Sie benötigen ein Speicherkonto mit aktiviertem Feature für hierarchische Namespaces. Befolgen Sie [diese Anleitung](create-data-lake-storage-account.md) für die Erstellung.

- Sie benötigen eine der folgenden Sicherheitsberechtigungen:

  - Ihrer Benutzeridentität muss die Rolle [Besitzer von Speicherblobdaten](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) im Umfang des Zielcontainers, des Speicherkontos, der übergeordneten Ressourcengruppe oder des Abonnements zugewiesen sein.

  - Sie müssen Besitzer des Zielcontainers, -verzeichnisses oder -blobs sein, in dem Sie die Einstellungen für die Zugriffssteuerungsliste anwenden möchten.

## <a name="manage-an-acl"></a>Verwalten einer Zugriffssteuerungsliste

1. Melden Sie sich zunächst beim [Azure-Portal](https://portal.azure.com/) an.

2. Suchen Sie nach Ihrem Speicherkonto, und zeigen Sie die Kontoübersicht an.

3. Wählen Sie unter **Datenspeicherung** die Option **Container** aus.

   Daraufhin werden die Container im Speicherkonto angezeigt.

   > [!div class="mx-imgBorder"]
   > ![Speicherkontocontainer im Azure-Portal](./media/data-lake-storage-acl-azure-portal/find-containers-in-azure-portal.png)

5. Navigieren Sie zu einem beliebigen Container, Verzeichnis oder Blob. Klicken Sie mit der rechten Maustaste auf das Objekt, und wählen Sie dann **ACL verwalten** aus.

   > [!div class="mx-imgBorder"]
   > ![Kontextmenü zum Verwalten einer Zugriffssteuerungsliste](./media/data-lake-storage-acl-azure-portal/manage-acl-menu-item.png)

   Daraufhin wird die Registerkarte **Zugriffsberechtigungen** der Seite **ACL verwalten** angezeigt. Verwenden Sie die Steuerelemente auf dieser Registerkarte, um den Zugriff auf das Objekt zu verwalten.

   > [!div class="mx-imgBorder"]
   > ![Registerkarte zum Verwalten des Zugriffs auf der Seite „ACL verwalten“](./media/data-lake-storage-acl-azure-portal/access-acl-page.png)

7. Klicken Sie auf die Schaltfläche **Prinzipal hinzufügen**, um einen *Sicherheitsprinzipal* zur Zugriffssteuerungsliste hinzuzufügen.

   > [!TIP]
   > Ein Sicherheitsprinzipal ist ein Objekt, das einen Benutzer, eine Gruppe, einen Dienstprinzipal oder eine verwaltete Identität darstellt, der bzw. die in Azure Active Directory (AD) definiert ist.

   Suchen Sie den Sicherheitsprinzipal mithilfe des Suchfelds, und klicken Sie dann auf **Auswählen**.

   > [!div class="mx-imgBorder"]
   > ![Hinzufügen eines Sicherheitsprinzipals zur Zugriffssteuerungsliste](./media/data-lake-storage-acl-azure-portal/get-security-principal.png)

   > [!NOTE]
   > Es wird empfohlen, dass Sie eine Sicherheitsgruppe in Azure AD erstellen und dann die Berechtigungen für die Gruppe verwalten, anstatt diese für einzelne Benutzer zu verwalten. Einzelheiten zu dieser Empfehlung sowie zu anderen bewährten Methoden finden Sie unter [Zugriffssteuerungsmodell in Azure Data Lake Storage Gen2](data-lake-storage-access-control-model.md).

8. Wählen Sie die Registerkarte **Standardberechtigungen** aus, und aktivieren Sie dann das Kontrollkästchen **Standardberechtigungen konfigurieren**, um die *Standard-ACL* zu verwalten.

   > [!TIP]
   > Eine Standard-ACL ist eine Vorlage für eine Zugriffssteuerungsliste, die die Zugriffssteuerungslisten für untergeordnete Elemente bestimmt, die unter einem Verzeichnis erstellt werden. Ein Blob verfügt über keine Standard-ACL, weshalb diese Registerkarte nur für Verzeichnisse angezeigt wird.

   > [!div class="mx-imgBorder"]
   > ![Registerkarte für die Standard-ACL auf der Seite „ACL verwalten“](./media/data-lake-storage-acl-azure-portal/default-acl-page.png)

## <a name="apply-an-acl-recursively"></a>Rekursives Anwenden einer Zugriffssteuerungsliste

Sie können Einträge aus Zugriffssteuerungslisten rekursiv auf die vorhandenen untergeordneten Elemente eines übergeordneten Verzeichnisses anwenden, ohne diese Änderungen für jedes untergeordnete Element einzeln vornehmen zu müssen. Allerdings können Sie ACL-Einträge über das Azure-Portal nicht rekursiv anwenden.

Verwenden Sie den Azure Storage-Explorer, PowerShell oder die Azure CLI, um Zugriffssteuerungslisten rekursiv anzuwenden. Wenn Sie es bevorzugen, Code zu schreiben, können Sie auch die .NET-, Java-, Python- oder Node.js-APIs verwenden.

Eine vollständige Liste der zugehörigen Leitfäden finden Sie unter [Festlegen von Zugriffssteuerungslisten](data-lake-storage-access-control.md#how-to-set-acls).

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über das Berechtigungsmodell von Data Lake Storage Gen2.

> [!div class="nextstepaction"]
> [Zugriffssteuerungsmodell in Azure Data Lake Storage Gen2](./data-lake-storage-access-control-model.md)

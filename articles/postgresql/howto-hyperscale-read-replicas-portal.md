---
title: Verwalten von Lesereplikaten – Azure-Portal – Azure Database for PostgreSQL – Hyperscale (Citus)
description: Hier erfahren Sie, wie Sie Lesereplikate für Azure Database for PostgreSQL verwalten – Hyperscale (Citus) über das Azure-Portal.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 08/03/2021
ms.openlocfilehash: 45867bc93b90c76d971fc4b7d8e4d8cc094929d6
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128629007"
---
# <a name="create-and-manage-read-replicas-in-azure-database-for-postgresql---hyperscale-citus-from-the-azure-portal"></a>Erstellen und Verwalten von Lesereplikaten in Azure Database for PostgreSQL – Hyperscale (Citus) über das Azure-Portal

In diesem Artikel erfahren Sie, wie Sie Lesereplikate in Hyperscale (Citus) über das Azure-Portal erstellen und verwalten. Weitere Informationen zu Lesereplikaten finden Sie in der [Übersicht](concepts-hyperscale-read-replicas.md).


## <a name="prerequisites"></a>Voraussetzungen

Eine [Hyperscale (Citus)-Servergruppe](quickstart-create-hyperscale-portal.md) als primäre Servergruppe.

## <a name="create-a-read-replica"></a>Erstellen eines Lesereplikats

Führen Sie die folgenden Schritte aus, um ein Lesereplikat zu erstellen:

1. Wählen Sie eine vorhandene Azure Database for PostgreSQL-Servergruppe aus, die Sie als primäre Servergruppe verwenden möchten. 

2. Wählen Sie auf der Seitenleiste der Servergruppe unter **Servergruppenverwaltung** die Option **Replikation** aus.

3. Wählen Sie **Replikat hinzufügen**.

4. Geben Sie einen Namen für das Lesereplikat ein. 

5. Wählen Sie **OK**, um die Erstellung des Replikats zu bestätigen.

Nach der Erstellung des Lesereplikats wird dieses im Fenster **Replikation** angezeigt.

> [!IMPORTANT]
>
> Lesen Sie den [Abschnitt „Überlegungen“ in der Übersicht über Lesereplikate](concepts-hyperscale-read-replicas.md#considerations).
>
> Bevor die Einstellung für eine primäre Servergruppe auf einen neuen Wert aktualisiert wird, aktualisieren Sie die Replikateinstellung auf den gleichen oder einen größeren Wert. Diese Aktion sorgt dafür, dass das Replikat mit allen Änderungen auf dem Masterserver Schritt halten kann.

## <a name="delete-a-primary-server-group"></a>Löschen einer primären Servergruppe

Für das Löschen einer primären Servergruppe führen Sie die gleichen Schritte wie für eine eigenständige Hyperscale (Citus)-Servergruppe durch. Führen Sie im Azure-Portal die folgenden Schritte aus:

1. Wählen Sie im Azure-Portal Ihre primäre Azure Database for PostgreSQL-Servergruppe aus.

2. Öffnen Sie die Seite **Übersicht** für die Servergruppe. Wählen Sie **Löschen**.
 
3. Geben Sie den Namen der zu löschenden primären Servergruppe ein. Wählen Sie **Löschen** aus, um das Löschen der primären Servergruppe zu bestätigen.
 

## <a name="delete-a-replica"></a>Löschen eines Replikats

Sie können ein Lesereplikat auf ähnliche Weise löschen wie eine primäre Servergruppe.

- Öffnen Sie im Azure-Portal die Seite **Übersicht** für das Lesereplikat. Klicken Sie auf **Löschen**.
 
Sie können das Lesereplikat auch über das Fenster **Replikation** löschen, indem Sie folgendermaßen vorgehen:

1. Wählen Sie im Azure-Portal Ihre primäre Hyperscale (Citus)-Servergruppe aus.

2. Wählen Sie im Menü der Servergruppe unter **Servergruppenverwaltung** die Option **Replikation** aus.

3. Wählen Sie das zu löschende Lesereplikat aus.
 
4. Wählen Sie **Replikat löschen** aus.
 
5. Geben Sie den Namen des zu löschenden Replikats ein. Wählen Sie **Löschen**, um das Löschen des Replikats zu bestätigen.

## <a name="next-steps"></a>Nächste Schritte

* Hier erfahren Sie mehr über [Lesereplikate in Azure Database for PostgreSQL – Hyperscale (Citus)](concepts-hyperscale-read-replicas.md).

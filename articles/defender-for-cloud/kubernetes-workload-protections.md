---
title: Workloadschutz für Ihre Kubernetes-Workloads
description: Erfahren Sie, wie Sie die Sicherheitsempfehlungen von Microsoft Defender für Cloud zum Schutz von Kubernetes-Workloads umsetzen.
services: security-center
author: memildin
manager: rkarlin
ms.service: defender-for-cloud
ms.topic: how-to
ms.date: 11/09/2021
ms.author: memildin
ms.openlocfilehash: 5f8bf1f34ba33a4a6c3d258e8cb813d062f1f98f
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132526160"
---
# <a name="protect-your-kubernetes-workloads"></a>Schützen Ihrer Kubernetes-Workloads

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Auf dieser Seite wird beschrieben, wie Sie die Sicherheitsempfehlungen von Microsoft Defender für Cloud für den Schutze von Kubernetes-Workloads umsetzen.

Weitere Informationen zu diesen Features finden Sie unter [Bewährte Methoden zum Workloadschutz mithilfe der Kubernetes-Zugangssteuerung](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control).

Defender für Cloud bietet weitere Sicherheitsfeatures für Container, sofern Sie Microsoft Defender für Kubernetes aktivieren. Das bedeutet Folgendes:

- Überprüfen Ihrer Containerregistrierungen auf Sicherheitsrisiken mit [Microsoft Defender für Containerregistrierungen](defender-for-container-registries-introduction.md)
- Abrufen von Warnungen zur Bedrohungserkennung in Echtzeit für Ihre K8s-Cluster mit [Microsoft Defender für Kubernetes](defender-for-kubernetes-introduction.md)

> [!TIP]
> Eine Liste *aller* Sicherheitsempfehlungen für Kubernetes-Cluster und -Knoten finden Sie im Abschnitt [Compute](recommendations-reference.md#recs-compute) der Referenztabelle zu Empfehlungen.



## <a name="availability"></a>Verfügbarkeit

| Aspekt                          | Details                                                                                                                                      |
|---------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------|
| Status des Release:                  | Allgemeine Verfügbarkeit (General Availability, GA)                                                                                                                    |
| Preise:                        | Kostenlos                                                                                                                                         |
| Erforderliche Rollen und Berechtigungen: | **Besitzer** oder **Sicherheitsadministrator** zum Bearbeiten einer Zuweisung<br>**Leser** zum Anzeigen der Empfehlungen                                              |
| Umgebungsanforderungen:       | Kubernetes Version 1.14 oder höher ist erforderlich.<br>Keine PodSecurityPolicy-Ressource (altes PSP-Modell) in den Clustern<br>Windows-Knoten werden nicht unterstützt. |
| Clouds:                         | :::image type="icon" source="./media/icons/yes-icon.png"::: Kommerzielle Clouds<br>:::image type="icon" source="./media/icons/yes-icon.png"::: National (Azure Government, Azure China 21Vianet) |
|                                 |                                                                                                                                              |


## <a name="set-up-your-workload-protection"></a>Einrichten des Workloadschutzes

Microsoft Defender für Cloud bietet verschiedene Empfehlungen, die verfügbar sind, wenn Sie das **Azure Policy-Add-On für Kubernetes** installieren.

### <a name="step-1-deploy-the-add-on"></a>Schritt 1: Bereitstellen des Add-Ons

Installieren Sie zum Konfigurieren der Empfehlungen das **Azure Policy-Add-On für Kubernetes**. 

- Sie können dieses Add-On auch automatisch bereitstellen, wie unter [Aktivieren der automatischen Bereitstellung von Log Analytics-Agent und -Erweiterungen](enable-data-collection.md#auto-provision-mma) erläutert. Wenn die automatische Bereitstellung für das Add-On auf „Ein“ festgelegt ist, wird die Erweiterung standardmäßig in allen vorhandenen und zukünftigen Clustern aktiviert (sofern diese die Add-On-Installationsanforderungen erfüllen).

    :::image type="content" source="media/defender-for-kubernetes-usage/policy-add-on-auto-provision.png" alt-text="Verwenden des Defender für Cloud-Tools zur automatischen Bereitstellung zur Installation des Richtlinien-Add-Ons für Kubernetes":::

- Gehen Sie folgendermaßen vor, um das Add-On manuell bereitzustellen:

    1. Suchen Sie auf der Seite „Empfehlungen“ nach der Empfehlung **Azure Policy-Add-On für Kubernetes muss auf Ihren Clustern installiert und aktiviert sein**. 

        :::image type="content" source="./media/defender-for-kubernetes-usage/recommendation-to-install-policy-add-on-for-kubernetes.png" alt-text="Empfehlung **Azure Policy-Add-On für Kubernetes muss auf Ihren Clustern installiert und aktiviert sein**":::

        > [!TIP]
        > Die Empfehlung ist in fünf verschiedenen Sicherheitskontrollen enthalten, und es spielt keine Rolle, welche Sie im nächsten Schritt auswählen.

    1. Wählen Sie die Empfehlung in einer der Sicherheitskontrollen aus, um die Ressourcen anzuzeigen, in denen Sie das Add-On installieren können.
    1. Wählen Sie den entsprechenden Cluster aus, und klicken Sie auf **Korrigieren**.

        :::image type="content" source="./media/defender-for-kubernetes-usage/recommendation-to-install-policy-add-on-for-kubernetes-details.png" alt-text="Detailseite der Empfehlung **Azure Policy-Add-On für Kubernetes muss auf Ihren Clustern installiert und aktiviert sein**":::

### <a name="step-2-view-and-configure-the-bundle-of-recommendations"></a>Schritt 2: Anzeigen und Konfigurieren des Empfehlungspakets

1. Ungefähr 30 Minuten nach Abschluss der Installation des Add-Ons zeigt Defender für Cloud den Integritätsstatus der Cluster für die folgenden Empfehlungen an (jeweils in der relevanten Sicherheitskontrolle):

    > [!NOTE]
    > Wenn Sie das Add-On zum ersten Mal installieren, werden diese Empfehlungen in der zugehörigen Liste als neue Ergänzungen angezeigt. 

    > [!TIP]
    > Einige Empfehlungen weisen Parameter auf, die über Azure Policy angepasst werden müssen, damit sie effektiv verwendet werden können. Um beispielsweise von der Empfehlung **Containerimages sollten nur von vertrauenswürdigen Registrierungen bereitgestellt werden** zu profitieren, müssen Sie Ihre vertrauenswürdigen Registrierungen definieren.
    > 
    > Wenn Sie die erforderlichen Parameter für die Empfehlungen, die konfiguriert werden müssen, nicht eingeben, werden Ihre Workloads als fehlerhaft angezeigt.

    | Name der Empfehlung                                                         | Sicherheitskontrolle                         | Konfiguration erforderlich |
    |-----------------------------------------------------------------------------|------------------------------------------|------------------------|
    | Container dürfen nur an zulässigen Ports lauschen                              | Nicht autorisierten Netzwerkzugriff einschränken     | **Ja**                |
    | Dienste dürfen nur an zulässigen Ports lauschen                                | Nicht autorisierten Netzwerkzugriff einschränken     | **Ja**                |
    | Verwendung von Hostnetzwerken und -ports einschränken                     | Nicht autorisierten Netzwerkzugriff einschränken     | **Ja**                |
    | Überschreiben oder Deaktivieren des AppArmor-Profils für Container einschränken | Optimieren von Sicherheitskonfigurationen        | **Ja**                |
    | Containerimages sollten nur von vertrauenswürdigen Registrierungen bereitgestellt werden            | Sicherheitsrisiken beheben                | **Ja**                |
    | Linux-Funktionen mit den niedrigsten Berechtigungen für Container erzwingen       | Zugriff und Berechtigungen verwalten            | **Ja**                |
    | Verwendung von HostPath-Volumeeinbindungen von Pods auf eine bekannte Liste beschränken    | Zugriff und Berechtigungen verwalten            | **Ja**                |
    | Privilegierte Container müssen vermieden werden                                     | Zugriff und Berechtigungen verwalten            | Nein                     |
    | Container mit Rechteausweitung müssen vermieden werden                       | Zugriff und Berechtigungen verwalten            | Nein                     |
    | Für Kubernetes-Cluster muss die automatische Bereitstellung von API-Anmeldeinformationen deaktiviert werden             | Zugriff und Berechtigungen verwalten            | Nein                     |
    | Unveränderliches (schreibgeschütztes) Stammdateisystem für Container erzwingen     | Zugriff und Berechtigungen verwalten            | Nein                     |
    | Container mit Rechteausweitung müssen vermieden werden                       | Zugriff und Berechtigungen verwalten            | Nein                     |
    | Das Ausführen von Containern als Root-Benutzer muss vermieden werden                           | Zugriff und Berechtigungen verwalten            | Nein                     |
    | Container mit Freigabe sensibler Hostnamespaces vermeiden              | Zugriff und Berechtigungen verwalten            | Nein                     |
    | Für Container müssen CPU- und Arbeitsspeicherlimits erzwungen werden                          | Anwendungen vor DDoS-Angriffen schützen | Nein                     |
    | Auf Kubernetes-Cluster sollte nur über HTTPS zugegriffen werden können                    | Verschlüsseln von Daten während der Übertragung                  | Nein                     |
    | Kubernetes-Cluster dürfen nicht den Standardnamespace verwenden                    | Bewährte Sicherheitsmethoden implementieren        | Nein                     |
    ||||


1. Passen Sie bei Empfehlungen, die dies erfordern, die Parameter an:

    1. Wählen Sie im Menü von Defender für Cloud die Option **Sicherheitsrichtlinie** aus.
    1. Wählen Sie das relevante Abonnement aus.
    1. Wählen Sie im Abschnitt **Defender für Cloud-Standardrichtlinie** die Option **Effektive Richtlinie anzeigen** aus.
    1. Wählen Sie die Standardrichtlinie für den Bereich aus, den Sie aktualisieren.
    1. Öffnen Sie die Registerkarte **Parameter**, und ändern Sie die Werte nach Bedarf.

        :::image type="content" source="media/kubernetes-workload-protections/containers-parameter-requires-configuration.png" alt-text="Ändern der Parameter für eine der Empfehlungen im Kubernetes-Workloadschutzpaket.":::

    1. Klicken Sie auf **Überprüfen + speichern**.
    1. Wählen Sie **Speichern** aus.


1. Um eine der Empfehlungen zu erzwingen, gehen Sie wie folgt vor: 

    1. Öffnen Sie die Seite mit den Details zur Empfehlung, und wählen Sie **Ablehnen** aus:

        :::image type="content" source="./media/defender-for-kubernetes-usage/enforce-workload-protection-example.png" alt-text="Option „Ablehnen“ für Azure Policy-Parameter.":::

        Dadurch wird das Fenster geöffnet, in dem Sie den Bereich festlegen. 

    1. Wenn Sie den Bereich festgelegt haben, wählen Sie **In „Ablehnen“ ändern** aus.

1. So zeigen Sie die für Ihre Cluster geltenden Empfehlungen an

    1. Öffnen Sie die Seite [Ressourcenbestand](asset-inventory.md) von Defender für Cloud, und wählen Sie den Ressourcentypfilter **Kubernetes-Dienste**.

    1. Wählen Sie einen Cluster aus, um die für diesen verfügbaren Empfehlungen zu untersuchen und zu überprüfen. 

1. Wenn Sie eine Empfehlung für den Workloadschutz anzeigen, sehen Sie neben dem Cluster auch die Anzahl der betroffenen Pods („Kubernetes-Komponenten“). Wählen Sie den Cluster und dann **Aktion ausführen** aus, um eine Liste mit den jeweiligen Pods auszuwählen.

    :::image type="content" source="./media/defender-for-kubernetes-usage/view-affected-pods-for-recommendation.gif" alt-text="Anzeigen der betroffenen Pods für eine K8s-Empfehlung."::: 

1. Um die Erzwingung zu testen, verwenden Sie die folgenden beiden Kubernetes-Bereitstellungen:

    - Eine ist eine fehlerfreie Bereitstellung, die mit den Empfehlungen zum Workloadschutz konform ist.
    - Die andere stellt eine fehlerhafte Bereitstellung dar, die mit *keiner* der Empfehlungen konform ist.

    Stellen Sie die YAML-Beispieldateien unverändert bereit, oder verwenden Sie sie als Referenz zum Korrigieren Ihrer eigenen Workload (Schritt VIII).  


## <a name="healthy-deployment-example-yaml-file"></a>YAML-Dateibeispiel einer fehlerfreien Bereitstellung

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis-healthy-deployment
  labels:
    app: redis
spec:
  replicas: 3
  selector:
    matchLabels:
      app: redis
  template:
    metadata:
      labels:
        app: redis
      annotations:
        container.apparmor.security.beta.kubernetes.io/redis: runtime/default
    spec:
      containers:
      - name: redis
        image: <customer-registry>.azurecr.io/redis:latest
        ports:
        - containerPort: 80
        resources:
          limits:
            cpu: 100m
            memory: 250Mi
        securityContext:
          privileged: false
          readOnlyRootFilesystem: true
          allowPrivilegeEscalation: false
          runAsNonRoot: true
          runAsUser: 1000
---
apiVersion: v1
kind: Service
metadata:
  name: redis-healthy-service
spec:
  type: LoadBalancer
  selector:
    app: redis
  ports:
  - port: 80
    targetPort: 80
```

## <a name="unhealthy-deployment-example-yaml-file"></a>YAML-Dateibeispiel einer fehlerhaften Bereitstellung

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis-unhealthy-deployment
  labels:
    app: redis
spec:
  replicas: 3
  selector:
    matchLabels:
      app: redis
  template:
    metadata:      
      labels:
        app: redis
    spec:
      hostNetwork: true
      hostPID: true 
      hostIPC: true
      containers:
      - name: redis
        image: redis:latest
        ports:
        - containerPort: 9001
          hostPort: 9001
        securityContext:
          privileged: true
          readOnlyRootFilesystem: false
          allowPrivilegeEscalation: true
          runAsUser: 0
          capabilities:
            add:
              - NET_ADMIN
        volumeMounts:
        - mountPath: /test-pd
          name: test-volume
          readOnly: true
      volumes:
      - name: test-volume
        hostPath:
          # directory location on host
          path: /tmp
---
apiVersion: v1
kind: Service
metadata:
  name: redis-unhealthy-service
spec:
  type: LoadBalancer
  selector:
    app: redis
  ports:
  - port: 6001
    targetPort: 9001
```



## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie den Kubernetes-Workloadschutz konfigurieren. 

Weitere verwandte Informationen finden Sie auf den folgenden Seiten: 

- [Defender for Cloud-Empfehlungen für Compute](recommendations-reference.md#recs-compute)
- [Warnungen auf AKS-Clusterebene](alerts-reference.md#alerts-k8scluster)
- [Warnungen auf Containerhostebene](alerts-reference.md#alerts-containerhost)

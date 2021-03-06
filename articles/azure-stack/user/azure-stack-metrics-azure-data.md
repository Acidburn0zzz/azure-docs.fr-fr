---
title: Azure Monitor sur Azure Stack | Microsoft Docs
description: En savoir plus sur Azure Monitor sur Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/11/2019
ms.author: mabrigg
ms.lastreviewed: 12/01/2018
ms.openlocfilehash: 469213ae67f1f2bcece6afa5d9b7873bfdfe8d6c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58095736"
---
# <a name="azure-monitor-on-azure-stack"></a>Azure Monitor sur Azure Stack

*S’applique à : systèmes intégrés Azure Stack*

Cet article fournit une vue d’ensemble du service Azure Monitor sur Azure Stack. Il vous permet de découvrir le fonctionnement d’Azure Monitor et vous fournit des informations supplémentaires à propos de l’utilisation d’Azure Monitor sur Azure Stack. 

Pour obtenir une présentation d’Azure Monitor, parcourir une vue d’ensemble et un guide de prise en main, consultez l’article Azure général [Prise en main d’Azure Monitor sur Azure Stack](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-get-started).

![Panneau Azure Stack Monitor](./media/azure-stack-metrics-azure-data/azs-monitor.png)

Azure Monitor est le service de plateforme qui fournit une source unique d’analyse des ressources Azure. Azure Monitor permet de visualiser, d’interroger, de router et d’archiver les métriques et journaux provenant de ressources Azure, ainsi que d’entreprendre des actions sur ceux-ci. Vous pouvez utiliser ces données à l’aide du portail d’administration Azure Stack, des cmdlets PowerShell Monitor, de l’interface de ligne de commande multiplateforme ou des API REST Azure Monitor. Pour en savoir plus sur la connectivité spécifique prise en charge par Azure Stack, consultez l’article portant sur la [consommation des données de surveillance issues d’Azure Stack](azure-stack-metrics-monitor.md).

> [!Note]
> Les métriques et les journaux de diagnostic ne sont pas disponibles pour le kit de développement Azure Stack.

## <a name="prerequisites-for-azure-monitor-on-azure-stack"></a>Prérequis pour Azure Monitor sur Azure Stack

Enregistrer le fournisseur de ressources **Microsoft.insights** dans les paramètres relatifs à l’offre d’abonnement du fournisseurs de ressources. Pour vérifier que le fournisseur de ressources est disponible dans l’offre qui est associée à votre abonnement, procédez comme suit :

1. Ouvrez le portail d’administration Azure Stack.
2. Sélectionnez **Offres**.
3. Sélectionnez l’offre associée à l’abonnement.
4. Sélectionnez **Fournisseurs de ressources** sous **Paramètres**. 
5. Recherchez **Microsoft.Insights** dans la liste et vérifiez que l’état est **Inscrit**.

## <a name="overview-of-azure-monitor-on-azure-stack"></a>Vue d’ensemble d’Azure Monitor sur Azure Stack

Tout comme Azure Monitor sur Azure, Azure Monitor sur Azure Stack fournit des métriques et journaux de base à propos de l’infrastructure pour une majorité de services.

## <a name="azure-monitor-sources-compute-subset"></a>Sources Azure Monitor : sous-ensemble de calcul

![Sources Azure Monitor sur Azure Stack : sous-ensemble de calcul](media//azure-stack-metrics-azure-data/azs-monitor-computersubset.png)

Le fournisseur de ressources **Microsoft.Compute** dans Azure Stack inclut :
 - Virtual Machines 
 - Groupes de machines virtuelles identiques

### <a name="application---diagnostics-logs-application-logs-and-metrics"></a>Application – Journaux de diagnostic, journaux d’application et métriques

Les applications peuvent s’exécuter dans le système d’exploitation d’une machine virtuelle fonctionnant avec le fournisseur de ressources **Microsoft.Compute**. Ces applications et les machines virtuelles émettent leur propre ensemble de journaux et de métriques. Azure Monitor s’appuie sur l’extension de diagnostics Azure pour collecter la plupart des métriques et journaux de niveau application (Windows ou Linux). 

Parmi ces types de mesures, on compte :
 - Compteurs de performances
 - Journaux d’application
 - Journaux des événements Windows
 - Source d’événement .NET
 - Journaux IIS
 - Suivi d’événements pour Windows basé sur les manifestes
 - Vidages sur incident
 - Journaux d'erreurs client

> [!Note]  
> L’extension Linux Diagnostics n’est pas prise en charge sur Azure Stack.

### <a name="host-and-guest-vm-metrics"></a>Métriques d’hôte et machine virtuelle invitée

Les ressources de calcul répertoriées précédemment disposent d’une machine virtuelle hôte et d’un système d’exploitation invité dédiés. La machine virtuelle hôte et le SE invité sont l’équivalent de la machine virtuelle racine et de la machine virtuelle invitée dans l’hyperviseur Hyper-V. Vous pouvez collecter des métriques pour la machine virtuelle hôte et le système SE invité. Par ailleurs, vous pouvez collecter des journaux de diagnostic pour le SE invité. La liste des métriques que vous pouvez collecter pour les machines virtuelles hôtes et invitées sur Azure Stack est disponible dans l’article [Métriques prises en charge avec Azure Monitor sur Azure Stack](azure-stack-metrics-supported.md). 

### <a name="activity-log"></a>Journal d’activité

Vous pouvez effectuer des recherches dans les journaux d’activité afin d’obtenir des informations sur vos ressources de calcul telles qu’elles sont analysées par l’infrastructure Azure Stack. Ce journal contient des informations telles que les dates et heures de création ou de destruction de ressources. Les journaux d’activité sur Azure Stack sont cohérents avec Azure. Pour en savoir plus, consultez l’article [Présentation du journal d’activité Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs). 


## <a name="azure-monitor-sources-everything-else"></a>Sources Azure Monitor : tout le reste

![Sources Azure Monitor sur Azure Stack : tout le reste](media//azure-stack-metrics-azure-data/azs-monitor-othersubset.png)

### <a name="resources---metrics-and-diagnostics-logs"></a>Ressources – Métriques et journaux de diagnostics

Les métriques et les journaux de diagnostics que vous pouvez collecter varient en fonction du type de ressource. La liste des métriques que vous pouvez collecter actuellement pour chaque ressource est disponible dans l’article relatif aux métriques prises en charge. Pour en savoir plus, consultez [Métriques prises en charge avec Azure Monitor sur Azure Stack](azure-stack-metrics-supported.md).

### <a name="activity-log"></a>Journal d’activité

Le journal d’activité est identique à celui des ressources de calcul. 

### <a name="uses-for-monitoring-data"></a>Utilisations de l’analyse de données

**Stockage et archivage**  

Certaines données de surveillance sont déjà disponibles dans Azure Monitor et enregistrées pendant un laps de temps défini. 
 - Les métriques sont stockées pendant 90 jours. 
 - Les entrées de journal d’activité sont conservées pendant 90 jours. 
 - Les journaux de diagnostic ne sont pas stockés.
 - Archivez les données dans un compte de stockage pour les conserver à long terme.

**Requête**  

Vous pouvez utiliser l’API REST Azure Monitor, les commandes de l’Interface de ligne de commande interplateforme (CLI), les cmdlets PowerShell ou le kit de développement logiciel .NET pour accéder aux données présentes dans le système ou dans le stockage Azure. 

**Visualisation**

En affichant les données analysées sous forme de graphiques et de diagrammes, vous êtes en mesure d’identifier des tendances plus rapidement qu’en lisant simplement les données brutes vous-même. 

Il existe quelques méthodes de visualisation, qui sont les suivantes :
 - Utilisation du portail de l’administrateur et de l’utilisateur Azure Stack
 - Routage des données vers Microsoft Power BI
 - Routage de données vers un outil de visualisation tiers par streaming en direct ou par lecture de l’outil à partir d’une archive située dans le stockage Azure

## <a name="methods-of-accessing-azure-monitor-on-azure-stack"></a>Méthodes d’accès à Azure Monitor sur Azure Stack

En général, vous pouvez manipuler le suivi, le routage et la récupération des données à l’aide de l’une des méthodes suivantes. Toutes les méthodes ne sont pas disponibles pour toutes les actions ou tous les types de données.

 - [Portail Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-use-portal)
 - [PowerShell](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-powershell-samples)
 - [Interface de ligne de commande interplateforme (CLI)](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-cli-samples)
 - [API REST](https://docs.microsoft.com/rest/api/monitor)
 - [Kit de développement logiciel (SDK) .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor)

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les options de surveillance de la consommation de données sur Azure Stack, consultez l’article [Consommation de données de surveillance à partir d’Azure Stack](azure-stack-metrics-monitor.md).

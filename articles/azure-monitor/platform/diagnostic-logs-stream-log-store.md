---
title: Diffuser en continu les journaux de diagnostic Azure vers Log Analytics
description: Découvrez comment diffuser en continu les journaux de diagnostic Azure vers un espace de travail Log Analytics.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/04/2018
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: bd760fca20a602127e7d33913547dcb2c6bc95f6
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2019
ms.locfileid: "58351546"
---
# <a name="stream-azure-diagnostic-logs-to-log-analytics"></a>Diffuser en continu les journaux de diagnostic Azure vers Log Analytics

**[Les journaux de diagnostic Azure](diagnostic-logs-overview.md)** peuvent être diffusés en continu quasiment en temps réel vers Azure Log Analytics à l’aide du portail, des applets de commande PowerShell ou de l’interface de ligne de commande Azure.

## <a name="what-you-can-do-with-diagnostics-logs-in-log-analytics"></a>Ce que vous pouvez faire avec les journaux de diagnostic dans Log Analytics

Azure Log Analytics est un outil d’analytique et de recherche de journaux flexible qui vous offre une meilleure visibilité sur les données de journal brutes générées à partir des ressources Azure. Il inclut, entre autres, les fonctionnalités suivantes :

* **Recherche dans les journaux** : écrivez des requêtes avancées pour rechercher des données de journal spécifiques, mettez en corrélation des journaux à partir de diverses sources, ou encore générez des graphiques qui peuvent être épinglés à votre tableau de bord Azure.
* **Génération d’alertes** : soyez informé par e-mail ou appel webhook lorsqu’un ou plusieurs événements correspond(ent) à une requête particulière.
* **Solutions** : utilisez des tableaux de bord et des vues prédéfinies pour bénéficier d’une meilleure visibilité sur vos données de journal.
* **Analytique avancée** : appliquez des algorithmes de Machine Learning et de correspondance à des critères spéciaux pour identifier d’éventuels problèmes consignés dans vos journaux.

## <a name="enable-streaming-of-diagnostic-logs-to-log-analytics"></a>Activer la diffusion en continu des journaux de diagnostic vers Log Analytics

Vous pouvez activer la diffusion en continu des journaux de diagnostic par programme, via le portail ou à l’aide des [API REST Azure Monitor](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings). Dans tous les cas, vous créez un paramètre de diagnostic dans lequel vous spécifiez un espace de travail Log Analytics et les catégories de journal et les indicateurs de performance que vous voulez envoyer dans cet espace de travail. Une **catégorie de journal** de diagnostic est un type de journal qu’une ressource peut générer.

Il n’est pas nécessaire que l’espace de travail Log Analytics se trouve dans le même abonnement que la ressource générant des journaux, à condition que l’utilisateur qui configure le paramètre ait un accès RBAC approprié aux deux abonnements.

> [!NOTE]
> L’envoi de métriques multidimensionnelles via les paramètres de diagnostic n’est pas pris en charge actuellement. Les métriques à plusieurs dimensions sont exportées en tant que métriques dimensionnelles uniques aplaties, puis agrégées dans les valeurs de la dimension.
>
> *Par exemple* : La métrique « Messages entrants » sur un Event Hub peut être examinée et représentée sur un niveau par file d’attente. Toutefois, lors de l’exportation via les paramètres de diagnostic, la métrique est représentée sous la forme de tous les messages entrants, dans toutes les files d’attente de l’Event Hub.
>
>

## <a name="stream-diagnostic-logs-using-the-portal"></a>Diffuser en continu les journaux de diagnostic à l’aide du portail
1. Dans le portail, accédez à Azure Monitor, puis cliquez sur **Paramètres de diagnostic**

    ![Section Surveillance d’Azure Monitor](media/diagnostic-logs-stream-log-store/diagnostic-settings-blade.png)

2. Vous pouvez également filtrer la liste par type ou groupe de ressources, puis cliquez sur la ressource pour laquelle vous souhaitez définir un paramètre de diagnostic.

3. S’il n’existe aucun paramètre sur la ressource que vous avez sélectionnée, vous êtes invité à créer un paramètre. Cliquez sur « Activer les diagnostics ».

   ![Ajouter le paramètre de diagnostic - aucun paramètre existant](media/diagnostic-logs-stream-log-store/diagnostic-settings-none.png)

   S’il existe des paramètres existants sur la ressource, vous verrez une liste de paramètres déjà configurés sur cette ressource. Cliquez sur « Ajouter le paramètre de diagnostic ».

   ![Ajouter le paramètre de diagnostic - paramètres existants](media/diagnostic-logs-stream-log-store/diagnostic-settings-multiple.png)

3. Donnez un nom à votre définition, cochez la case **Envoyer à Log Analytics**, puis sélectionnez un espace de travail Log Analytics.

   ![Ajouter le paramètre de diagnostic - paramètres existants](media/diagnostic-logs-stream-log-store/diagnostic-settings-configure.png)

4. Cliquez sur **Enregistrer**.

Après quelques instants, le nouveau paramètre apparaît dans la liste des paramètres de cette ressource, et les journaux de diagnostic sont diffusés en continu dans cet espace de travail dès que de nouvelles données d’événement sont générées. Notez qu’un délai de quinze minutes peut s’écouler entre l’événement et sa consignation dans Log Analytics.

### <a name="via-powershell-cmdlets"></a>Via les applets de commande PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Pour activer la diffusion en continu via les [applets de commande Azure PowerShell](../../azure-monitor/platform/powershell-quickstart-samples.md), vous pouvez utiliser l’applet de commande `Set-AzDiagnosticSetting` avec ces paramètres :

```powershell
Set-AzDiagnosticSetting -ResourceId [your resource ID] -WorkspaceID [resource ID of the Log Analytics workspace] -Categories [list of log categories] -Enabled $true
```

Notez que la propriété workspaceID tient compte de l’ID complet de la ressource Azure dans l’espace de travail, et non de la clé/de l’ID de l’espace de travail affiché(e) dans le portail Log Analytics.

### <a name="via-azure-cli"></a>Via l’interface de ligne de commande Azure

Pour activer la diffusion en continu par le biais [d’Azure CLI](../../azure-monitor/platform/cli-samples.md), vous pouvez utiliser la commande [az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create).

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --workspace <log analytics name or object ID> \
    --resource <target resource object ID> \
    --resource-group <log analytics workspace resource group> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true
    }
    ]'
```

Vous pouvez ajouter des catégories supplémentaires dans le journal de diagnostic par l’adjonction de dictionnaires au tableau JSON transmis en tant que paramètre `--logs`.

L’argument `--resource-group` est obligatoire seulement si `--workspace` n’est pas un ID d’objet.

## <a name="how-do-i-query-the-data-in-log-analytics"></a>Comment faire pour interroger les données dans Log Analytics ?

Dans le panneau Recherche dans les journaux du portail ou dans la fonctionnalité Analytique avancée accessible depuis Log Analytics, vous pouvez interroger les journaux de diagnostic dans le cadre de la solution de gestion des journaux au niveau de la table AzureDiagnostics. Il existe également [plusieurs solutions pour les ressources Azure](../../azure-monitor/insights/solutions.md) que vous pouvez installer pour avoir une visibilité immédiate sur les données de journal que vous envoyez vers Log Analytics.

### <a name="known-limitation-column-limit-in-azurediagnostics"></a>Limitation connue : limite de colonne dans AzureDiagnostics
Étant donné que de nombreuses ressources envoyer tous les types de données sont envoyés à la même table (_AzureDiagnostics_), le schéma de cette table est le jeu super des schémas de tous les types de données différents sont collectées. Par exemple, si vous avez créé des paramètres de diagnostic pour la collection de types de données suivants, tous sont envoyées à l’espace de travail :
- Rapports d’audit de 1 ressource (ayant un schéma constitué de colonnes A, B et C)  
- Journaux d’erreurs de ressource 2 (ayant un schéma constitué de colonnes D, E et F)  
- Journaux de flux de données de 3 ressources (ayant un schéma constitué de colonnes G, H et j’ai)  
 
La table AzureDiagnostics se présente comme suit, avec des exemples de données :  
 
| ResourceProvider | Catégorie | A | b | C | D | E | F | G | H | I |
| -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- |
| Microsoft.Resource1 | AuditLogs | x1 | y1 | z1 |
| Microsoft.Resource2 | ErrorLogs | | | | q1 | w1 | e1 |
| Microsoft.Resource3 | DataFlowLogs | | | | | | | j1 | k1 | l1|
| Microsoft.Resource2 | ErrorLogs | | | | q2 | w2 | e2 |
| Microsoft.Resource3 | DataFlowLogs | | | | | | | j3 | k3 | l3|
| Microsoft.Resource1 | AuditLogs | x5 | y5 | z5 |
| ... |
 
Il existe une limite explicite d’une table de journal Azure donnée n’a ne pas plus de 500 colonnes. Une fois atteinte, toutes les lignes contenant des données avec n’importe quelle colonne en dehors des 500 premiers sont supprimées au moment de l’ingestion. La table AzureDiagnostics est particulièrement sensible pour être affectées à cette limite. Cela produit généralement parce qu’une grande variété de sources de données sont envoyées à l’espace de travail, ou plusieurs sources de données très détaillée adressés au même espace de travail. 
 
#### <a name="azure-data-factory"></a>Azure Data Factory  
Azure Data Factory, en raison d’un ensemble très détaillé des journaux, est une ressource qui est connue pour être particulièrement affectées par cette limite. notamment :  
- *Les paramètres utilisateur définis par rapport à toutes les activités dans votre pipeline*: il y aura une nouvelle colonne pour chaque paramètre nommé de manière unique un utilisateur par rapport à n’importe quelle activité créée. 
- *Activité entrées et sorties*: ces varient d’activité à et générer une grande quantité de colonnes en raison de leur nature détaillée. 
 
Comme avec les propositions plus larges de solution de contournement ci-dessous, il est recommandé pour isoler les journaux de l’ADF dans leur propre espace de travail pour réduire le risque de ces journaux ayant un impact sur d’autres types de journaux collectés dans vos espaces de travail. Nous prévoyons d’avoir organisé les journaux pour Azure Data Factory est disponible par mid-avril 2019.
 
#### <a name="workarounds"></a>Solutions de contournement
À court terme, jusqu'à ce que la limite de 500 colonnes est redéfinie, il est recommandé pour séparer les types de données détaillés en espaces de travail distincts afin de réduire le risque d’atteindre la limite.
 
À long terme, les Diagnostics Azure pour déplacer en dehors d’un schéma unifié, partiellement alloué dans des tables individuelles par chaque type de données ; associé à la prise en charge pour les types dynamiques, ceci considérablement améliore la facilité d’utilisation de données entrantes dans les journaux Azure via le mécanisme de Diagnostics Azure. Vous pouvez déjà voir cela pour sélectionner les types de ressources Azure, par exemple [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics) ou [Intune](https://docs.microsoft.com/intune/review-logs-using-azure-monitor) journaux. Veuillez rechercher les dernières nouvelles concernant les nouveaux types de ressources dans Azure prenant en charge de ces journaux organisé sur le [mises à jour Azure](https://azure.microsoft.com/updates/) blog !


## <a name="next-steps"></a>Étapes suivantes

* [En savoir plus sur les journaux de diagnostic Azure](diagnostic-logs-overview.md)


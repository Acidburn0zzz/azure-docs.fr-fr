---
title: Utilisation du point de terminaison privilégié dans Azure Stack | Microsoft Docs
description: Montre comment utiliser le point de terminaison privilégié dans Azure Stack (pour un opérateur Azure Stack).
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2019
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 01/25/2019
ms.openlocfilehash: 9eb2e8ddde13783eabf3d82173e6a2fa75ec2b06
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58082668"
---
# <a name="using-the-privileged-endpoint-in-azure-stack"></a>Utilisation du point de terminaison privilégié dans Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

En tant qu’opérateur Azure Stack, vous devez utiliser le portail administrateur, PowerShell ou les API Azure Resource Manager pour la plupart des tâches d’administration quotidiennes. Toutefois, pour certaines opérations moins courantes, vous devez utiliser le *point de terminaison privilégié* (PEP). Ce point de terminaison est une console PowerShell distante préconfigurée qui vous fournit suffisamment de fonctionnalités pour vous aider à effectuer une tâche requise. Le point de terminaison s’appuie sur [PowerShell JEA (Just Enough Administration)](https://docs.microsoft.com/powershell/jea/overview) pour exposer uniquement un ensemble limité d’applets de commande. Pour accéder au point de terminaison privilégié et appeler l’ensemble limité d’applets de commande, un compte à faibles privilèges est utilisé. Aucun compte d’administrateur n’est requis. Pour plus de sécurité, les scripts ne sont pas autorisés.

Vous pouvez utiliser le point de terminaison privilégié pour effectuer les tâches suivantes :

- Effectuer des tâches de bas niveau, telles que [collecter les journaux de diagnostic](https://docs.microsoft.com/azure/azure-stack/azure-stack-diagnostics#log-collection-tool).
- Effectuer de nombreuses tâches post-déploiement d’intégration au centre de données pour les systèmes intégrés, telles que l’ajout de redirecteurs DNS (Domain Name System), la configuration de l’intégration de Graph, l’intégration des services de fédération Active Directory (AD FS) ou la permutation des certificats.
- Collaborer avec l’équipe de support afin d’obtenir un accès global temporaire pour un dépannage approfondi d’un système intégré.

Le point de terminaison privilégié journalise chaque action (et sa sortie correspondante) que vous effectuez pendant la session PowerShell. Transparence totale et audit complet des opérations sont ainsi assurés. Vous pouvez conserver ces fichiers journaux en vue d’opérations d’audit futures.

> [!NOTE]
> Dans le Kit de développement Azure Stack (ASDK), vous pouvez exécuter certaines commandes disponibles dans le point de terminaison privilégié directement à partir d’une session PowerShell sur l’hôte du Kit de développement. Toutefois, vous pouvez être amené à tester certaines opérations à l’aide du point de terminaison privilégié, telles que la collecte de journaux, car c’est la seule méthode disponible pour effectuer certaines opérations dans un environnement de systèmes intégrés.

## <a name="access-the-privileged-endpoint"></a>Accéder au point de terminaison privilégié

Vous accédez au point de terminaison privilégié via une session PowerShell distante sur la machine virtuelle qui héberge le point de terminaison privilégié. Dans le Kit ASDK, cette machine virtuelle est nommée **AzS-ERCS01**. Si vous utilisez un système intégré, il existe trois instances du point de terminaison privilégié, s’exécutant chacune à l’intérieur d’une machine virtuelle (*Préfixe*-ERCS01, *Préfixe*-ERCS02 ou *Préfixe*-ERCS03) sur des hôtes différents à des fins de résilience. 

Avant de commencer cette procédure pour un système intégré, vérifiez que vous pouvez accéder à un point de terminaison privilégié par adresse IP ou via DNS. Après le déploiement initial d’Azure Stack, vous pouvez accéder au point de terminaison privilégié uniquement par adresse IP, car l’intégration de DNS n’est pas encore configurée. Votre fournisseur de matériel OEM vous fournira un fichier JSON nommé **AzureStackStampDeploymentInfo** qui contient les adresses IP de point de terminaison privilégié.


> [!NOTE]
> Pour des raisons de sécurité, nous exigeons que vous vous connectiez au point de terminaison privilégié uniquement à partir d’une machine virtuelle renforcée s’exécutant par-dessus l’hôte de cycle de vie du matériel ou d’un ordinateur verrouillé dédié, par exemple une [station de travail à accès privilégié](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations). La configuration d’origine de l’hôte de cycle de vie du matériel ne doit pas être modifiée, même lors de l’installation de nouveaux logiciels. Elle ne doit pas non plus être utilisée pour se connecter au point de terminaison privilégié.

1. Établir une relation de confiance.

    - Sur un système intégré, exécutez la commande suivante à partir d’une session Windows PowerShell avec élévation de privilèges pour ajouter le point de terminaison privilégié en tant qu’hôte approuvé sur la machine virtuelle renforcée, en cours d’exécution sur l’hôte de cycle de vie du matériel, ou sur la station de travail d’accès privilégié.

      ```PowerShell
        winrm s winrm/config/client '@{TrustedHosts="<IP Address of Privileged Endpoint>"}'
      ```
    - Si vous exécutez le Kit ASDK, connectez-vous à l’hôte du Kit de développement.

2. Sur la machine virtuelle renforcée en cours d’exécution sur l’hôte de cycle de vie du matériel ou sur la station de travail d’accès privilégié, ouvrez une session Windows PowerShell. Exécutez les commandes suivantes pour établir une session distante sur la machine virtuelle qui héberge le point de terminaison privilégié :
 
   - Sur un système intégré :
     ```PowerShell
       $cred = Get-Credential

       Enter-PSSession -ComputerName <IP_address_of_ERCS> `
         -ConfigurationName PrivilegedEndpoint -Credential $cred
     ```
     Le paramètre `ComputerName` peut être l’adresse IP ou le nom DNS de l’une des machines virtuelles qui héberge un point de terminaison privilégié. 
   - Si vous exécutez le Kit ADSK :
     
     ```PowerShell
       $cred = Get-Credential

       Enter-PSSession -ComputerName azs-ercs01 `
         -ConfigurationName PrivilegedEndpoint -Credential $cred
     ``` 
     Quand vous y êtes invité, utilisez les informations d’identification suivantes :

     - **Nom d’utilisateur :** spécifiez le compte CloudAdmin, au format **&lt;*domaine Azure Stack*&gt;\cloudadmin**. (Pour le Kit ASDK, le nom d’utilisateur est **azurestack\cloudadmin**.)
     - **Mot de passe** : entrez le mot de passe du compte d'administrateur de domaine AzureStackAdmin tel qu'il vous a été fourni pendant l'installation.

     > [!NOTE]
     > Si vous ne parvenez pas à vous connecter au point de terminaison ERCS, essayez à nouveau les étapes 1 et 2 en utilisant l’adresse IP d’une machine virtuelle ERCS à laquelle vous n’avez pas déjà essayé de vous connecter.

3. Une fois connecté, l'invite devient **[*adresse IP ou nom de la machine virtuelle ERCS*]: PS>** ou à **[azs-ercs01]: PS>**, en fonction de l'environnement. Depuis cette invite, exécutez `Get-Command` pour afficher la liste des applets de commande disponibles.

   Un grand nombre de ces applets de commande sont uniquement destinées aux environnements de système intégré (par exemple, les applets de commande associées à l’intégration au centre de données). Dans le Kit ASDK, les applets de commande suivantes ont été validées :

   - Clear-Host
   - Close-PrivilegedEndpoint
   - Exit-PSSession
   - Get-AzureStackLog
   - Get-AzureStackStampInformation
   - Get-Command
   - Get-FormatData
   - Get-Help
   - Get-ThirdPartyNotices
   - Measure-Object
   - New-CloudAdminUser
   - Out-Default
   - Remove-CloudAdminUser
   - Select-Object
   - Set-CloudAdminUserPassword
   - Test-AzureStack
   - Stop-AzureStack
   - Get-ClusterLog

## <a name="tips-for-using-the-privileged-endpoint"></a>Conseils d’utilisation du point de terminaison privilégié 

Comme mentionné ci-dessus, le point de terminaison privilégié est un point de terminaison [PowerShell JEA](https://docs.microsoft.com/powershell/jea/overview). Tout en procurant une couche de sécurité renforcée, un point de terminaison JEA réduit certaines des fonctionnalités de base de PowerShell, comme l’écriture de scripts ou la saisie semi-automatique via la touche Tab. Toute tentative d’opération de script est vouée à l’échec et se solde par l’erreur **ScriptsNotAllowed**. Ce comportement est normal.

Ainsi, par exemple, pour obtenir la liste des paramètres d’une applet de commande donnée, exécutez la commande suivante :

```PowerShell
    Get-Command <cmdlet_name> -Syntax
```

Sinon, vous pouvez utiliser l’applet de commande [Import-PSSession](https://docs.microsoft.com/powershell/module/Microsoft.PowerShell.Utility/Import-PSSession?view=powershell-5.1) pour importer l’ensemble des applets de commande de point de terminaison privilégié dans la session actuelle sur votre ordinateur local. En procédant ainsi, l’ensemble des applets de commande et des fonctions du point de terminaison sont désormais disponibles sur votre ordinateur local, avec la saisie semi-automatique via la touche Tab et, plus généralement, l’écriture de scripts. 

Pour importer la session du point de terminaison privilégié sur votre ordinateur local, procédez ainsi :

1. Établir une relation de confiance.

    - Sur un système intégré, exécutez la commande suivante à partir d’une session Windows PowerShell avec élévation de privilèges pour ajouter le point de terminaison privilégié en tant qu’hôte approuvé sur la machine virtuelle renforcée en cours d’exécution sur l’hôte de cycle de vie du matériel ou sur la station de travail d’accès privilégié.

      ```PowerShell
        winrm s winrm/config/client '@{TrustedHosts="<IP Address of Privileged Endpoint>"}'
      ```
    - Si vous exécutez le Kit ASDK, connectez-vous à l’hôte du Kit de développement.

2. Sur la machine virtuelle renforcée en cours d’exécution sur l’hôte de cycle de vie du matériel ou sur la station de travail d’accès privilégié, ouvrez une session Windows PowerShell. Exécutez les commandes suivantes pour établir une session distante sur la machine virtuelle qui héberge le point de terminaison privilégié :
 
   - Sur un système intégré :
     ```PowerShell
       $cred = Get-Credential

       $session = New-PSSession -ComputerName <IP_address_of_ERCS> `
         -ConfigurationName PrivilegedEndpoint -Credential $cred
     ```
     Le paramètre `ComputerName` peut être l’adresse IP ou le nom DNS de l’une des machines virtuelles qui héberge un point de terminaison privilégié. 
   - Si vous exécutez le Kit ADSK :
     
     ```PowerShell
      $cred = Get-Credential

      $session = New-PSSession -ComputerName azs-ercs01 `
         -ConfigurationName PrivilegedEndpoint -Credential $cred
     ``` 
     Quand vous y êtes invité, utilisez les informations d’identification suivantes :

     - **Nom d’utilisateur :** spécifiez le compte CloudAdmin, au format **&lt;*domaine Azure Stack*&gt;\cloudadmin**. (Pour le Kit ASDK, le nom d’utilisateur est **azurestack\cloudadmin**.)
     - **Mot de passe** : entrez le mot de passe du compte d'administrateur de domaine AzureStackAdmin tel qu'il vous a été fourni pendant l'installation.

3. Importer la session du point de terminaison privilégié dans votre ordinateur local
    ```PowerShell 
        Import-PSSession $session
    ```
4. Vous pouvez désormais utiliser la saisie semi-automatique via la touche Tab et écrire des scripts comme de coutume sur votre session locale PowerShell à l’aide de l’ensemble des fonctions et des applets de commande du point de terminaison privilégié, sans réduire l’état de la sécurité d’Azure Stack. Vous n’avez plus qu’à l’utiliser !


## <a name="close-the-privileged-endpoint-session"></a>Fermer la session du point de terminaison privilégié

 Comme indiqué plus haut, le point de terminaison privilégié journalise chaque action (et sa sortie correspondante) que vous effectuez pendant la session PowerShell. Vous devez fermer la session à l’aide de la cmdlet `Close-PrivilegedEndpoint`. Cette applet de commande ferme correctement le point de terminaison et transfère les fichiers journaux vers un partage de fichiers externe à des fins de rétention.

Pour fermer la session du point de terminaison :

1. Créez un partage de fichiers externe qui est accessible au point de terminaison privilégié. Dans un environnement avec Kit de développement, vous pouvez simplement créer un partage de fichiers sur l’hôte du Kit de développement.
2. Exécutez l’applet de commande `Close-PrivilegedEndpoint`. 
3. Vous êtes invité à entrer le chemin sur lequel stocker le fichier journal de transcription. Spécifiez le partage de fichiers que vous avez créé précédemment, dans le format &#92;&#92;*nom_serveur*&#92;*nom_partage*. Si vous ne spécifiez pas de chemin, l’applet de commande échoue et la session reste ouverte. 

    ![Sortie de l’applet de commande Close-PrivilegedEndpoint qui indique où vous spécifiez le chemin de destination de la transcription](media/azure-stack-privileged-endpoint/closeendpoint.png)

Une fois les fichiers journaux de transcription correctement transférés vers le partage de fichiers, ils sont automatiquement supprimés du point de terminaison privilégié. 

> [!NOTE]
> Si vous fermez la session du point de terminaison privilégié à l’aide des applets de commande `Exit-PSSession` ou `Exit`, ou que vous fermez simplement la console PowerShell, les journaux de transcription ne sont pas transférés vers un partage de fichiers. Ils demeurent dans le point de terminaison privilégié. La prochaine fois que vous exécutez `Close-PrivilegedEndpoint` et que vous incluez un partage de fichiers, les journaux de transcription issus de la (des) session(s) précédente(s) sont également transférés. N’utilisez pas `Exit-PSSession` ou `Exit` pour fermer la session PEP ; utilisez `Close-PrivilegedEndpoint`.


## <a name="next-steps"></a>Étapes suivantes

[Outils de diagnostic Azure Stack](azure-stack-diagnostics.md)

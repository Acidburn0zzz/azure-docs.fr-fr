---
title: Déployer une machine virtuelle avec un mot de passe stocké de façon sécurisée sur Azure Stack | Microsoft Docs
description: Découvrez comment déployer une machine virtuelle en utilisant un mot de passe stocké dans Azure Stack Key Vault
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/14/2019
ms.author: mabrigg
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 30ebccd6bee26900ac01f7f4d17993e24c9db745
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/12/2019
ms.locfileid: "57779247"
---
# <a name="create-a-virtual-machine-using-a-secure-password-stored-in-azure-stack-key-vault"></a>Créer une machine virtuelle avec un mot de passe sécurisé stocké dans un coffre de clés Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Cet article décrit pas à pas le déploiement d’une machine virtuelle Windows Server avec un mot de passe stocké dans un coffre de clés Azure Stack. L’utilisation d’un mot de passe d’un coffre de clés est plus sûre que la transmission d’un mot de passe en texte brut.

## <a name="overview"></a>Vue d’ensemble

Vous pouvez stocker des valeurs, comme des mots de passe, en tant que secrets dans un coffre de clés Azure Stack. Après avoir créé un secret, vous pouvez le référencer dans des modèles Azure Resource Manager. L’utilisation de secrets avec Resource Manager offre les avantages suivants :

* Vous n’avez pas à entrer manuellement un secret chaque fois que vous déployez une ressource.
* Vous pouvez spécifier quels utilisateurs ou principaux du service peuvent accéder à un secret.

## <a name="prerequisites"></a>Prérequis

* Les utilisateurs doivent s’abonner à une offre qui inclut le service Key Vault.
* [Installer PowerShell pour Azure Stack.](azure-stack-powershell-install.md)
* [Configurer votre environnement PowerShell.](azure-stack-powershell-configure-user.md)

Les étapes suivantes décrivent le processus nécessaire pour créer une machine virtuelle en récupérant le mot de passe stocké dans un coffre de clés :

1. Créez un secret de coffre de clés.
2. Mettre à jour le fichier azuredeploy.parameters.json.
3. Déployez le modèle.

> [!NOTE]  
> Vous pouvez utiliser ces étapes à partir du Kit de développement Azure Stack, ou à partir d’un client externe si vous êtes connecté via un VPN.

## <a name="create-a-key-vault-secret"></a>Créer un secret Key Vault

Le script suivant crée un coffre de clés et stocke un mot de passe dans le coffre de clés en tant que secret. Utilisez le paramètre `-EnabledForDeployment` quand vous créez le coffre de clés. Ce paramètre garantit que le coffre de clés peut être référencé à partir des modèles Azure Resource Manager.

```PowerShell

$vaultName = "contosovault"
$resourceGroup = "contosovaultrg"
$location = "local"
$secretName = "MySecret"

New-AzureRmResourceGroup `
  -Name $resourceGroup `
  -Location $location

New-AzureRmKeyVault `
  -VaultName $vaultName `
  -ResourceGroupName $resourceGroup `
  -Location $location
  -EnabledForTemplateDeployment

$secretValue = ConvertTo-SecureString -String '<Password for your virtual machine>' -AsPlainText -Force

Set-AzureKeyVaultSecret `
  -VaultName $vaultName `
  -Name $secretName `
  -SecretValue $secretValue

```

À l’exécution du script précédent, la sortie inclut l’URI du secret. Notez cet URI. Vous devez le référencer dans le modèle utilisé dans [Déployer une machine virtuelle Windows avec un mot de passe dans un coffre de clés](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-windows-create-passwordfromkv). Téléchargez le dossier [101-vm-secure-password](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-windows-create-passwordfromkv) sur votre ordinateur de développement. Ce dossier contient les fichiers `azuredeploy.json` et `azuredeploy.parameters.json` dont vous avez besoin dans les étapes suivantes.

Modifiez le fichier `azuredeploy.parameters.json` en fonction des valeurs de votre environnement. Les paramètres les plus intéressants sont le nom du coffre, le groupe de ressources du coffre et l’URI du secret (généré par le script précédent). Le fichier suivant est un exemple de fichier de paramètres :

## <a name="update-the-azuredeployparametersjson-file"></a>Mettre à jour le fichier azuredeploy.parameters.json

Mettez à jour le fichier azuredeploy.parameters.json avec les valeurs de la machine virtuelle KeyVaultURI, secretName, adminUsername correspondant à votre environnement. Le fichier JSON suivant est un exemple de fichier de paramètres du modèle :

```json
{
    "$schema":  "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion":  "1.0.0.0",
    "parameters":  {
       "adminUsername":  {
         "value":  "demouser"
          },
         "adminPassword":  {
           "reference":  {
              "keyVault":  {
                "id":  "/subscriptions/xxxxxx/resourceGroups/RgKvPwd/providers/Microsoft.KeyVault/vaults/KvPwd"
                },
              "secretName":  "MySecret"
           }
         },
       "dnsLabelPrefix":  {
          "value":  "mydns123456"
        },
        "windowsOSVersion":  {
          "value":  "2016-Datacenter"
        }
    }
}

```

## <a name="template-deployment"></a>Déploiement de modèle

Déployez maintenant le modèle avec le script PowerShell suivant :

```PowerShell  
New-AzureRmResourceGroupDeployment `
  -Name KVPwdDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateFile "<Fully qualified path to the azuredeploy.json file>" `
  -TemplateParameterFile "<Fully qualified path to the azuredeploy.parameters.json file>"
```

Une fois le modèle déployé, la sortie suivante est générée :

![Sortie du déploiement](media/azure-stack-key-vault-deploy-vm-with-secret/deployment-output.png)

## <a name="next-steps"></a>Étapes suivantes

* [Déployer un exemple d’application avec Key Vault](azure-stack-key-vault-sample-app.md)
* [Déployer une machine virtuelle avec un certificat Key Vault](azure-stack-key-vault-push-secret-into-vm.md)

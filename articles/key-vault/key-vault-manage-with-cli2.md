---
title: "Gestion de Azure Key Vault à l’aide de l’interface de ligne de commande (CLI) | Microsoft Docs"
description: "Utilisez ce tutoriel pour automatiser les tâches courantes dans Key Vault à l’aide de l’interface de ligne de commande CLI 2.0"
services: key-vault
documentationcenter: 
author: amitbapat
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2017
ms.author: ambapat
ms.openlocfilehash: 5da9f5eceda71ac85259193e0f183c72813e1679
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2017
---
# <a name="manage-key-vault-using-cli-20"></a>Gestion de Key Vault à l’aide de l’interface de ligne de commande (CLI) 2.0
Azure Key Vault est disponible dans la plupart des régions. Pour plus d’informations, consultez la [page de tarification de Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="introduction"></a>Introduction
Ce didacticiel vous aide à démarrer avec Azure Key Vault pour créer un conteneur renforcé (un coffre) dans Azure afin de stocker et gérer les clés de chiffrement et les secrets dans Azure. Il vous guide tout au long du processus d’utilisation de l’interface de ligne de commande interplateforme Azure pour créer un coffre qui contient une clé ou un mot de passe que vous pouvez ensuite utiliser avec une application Azure. Il vous montre également comment une application peut ensuite utiliser cette clé ou ce mot de passe.

**Durée estimée :** 20 minutes

> [!NOTE]
> Ce didacticiel n’inclut pas d’instructions sur l’écriture de l’application Azure abordée dans une des étapes, qui montre comment autoriser une application à utiliser une clé ou un secret dans le coffre de clés.
>
> Ce tutoriel utilise la dernière version d’Azure CLI 2.0.
>
>

Pour plus d’informations générales sur Azure Key Vault, consultez la page [Présentation d’Azure Key Vault](key-vault-whatis.md)

## <a name="prerequisites"></a>Composants requis
Pour suivre ce didacticiel, vous avez besoin des éléments suivants :

* Un abonnement Microsoft Azure. Si vous n’en avez pas, vous pouvez vous inscrire pour bénéficier d’un [essai gratuit](https://azure.microsoft.com/pricing/free-trial)dès aujourd’hui.
* Interface de ligne de commande Azure, version 2.0 ou ultérieure. Pour installer la dernière version et l’associer à votre abonnement Azure, consultez la page [Installation et configuration de l’interface de ligne de commande multiplateforme Azure 2.0](/cli/azure/install-azure-cli).
* Une application configurée pour utiliser la clé ou le mot de passe que vous créez dans ce didacticiel. Un exemple d’application est disponible dans le [Centre de téléchargement Microsoft](http://www.microsoft.com/download/details.aspx?id=45343). Pour obtenir des instructions, consultez le fichier Lisez-moi fourni.

## <a name="getting-help-with-azure-cross-platform-command-line-interface"></a>Obtention d’aide avec l’interface de ligne de commande interplateforme Azure
Ce didacticiel suppose que vous êtes familiarisé avec l’interface de ligne de commande (Bash, Terminal, invite de commandes)

Le paramètre --help ou -h peut être utilisé pour afficher l'aide relative à des commandes particulières. Le format azure help [commande] [options] permet également de renvoyer les mêmes informations. Les commandes suivantes, par exemple, renvoient les mêmes informations :

```
az account set --help
az account set -h
```

Si vous avez des doutes sur les paramètres exigés par une commande, reportez-vous à l'aide en utilisant --help, -h ou az help [commande].

Consultez également les didacticiels suivants afin de vous familiariser avec Azure Resource Manager dans l’interface de ligne de commande interplateforme Azure :

* [Installation de l’interface de ligne de commande Azure](/cli/azure/install-azure-cli)
* [Prise en main d’Azure CLI 2.0](/cli/azure/get-started-with-azure-cli)

## <a name="connect-to-your-subscriptions"></a>Connexion à vos abonnements
Pour vous connecter avec un compte professionnel, utilisez la commande suivante :

```
az login -u username@domain.com -p password
```

ou si vous voulez vous connecter en tapant de façon interactive

```
az login
```

Si vous disposez de plusieurs abonnements et que vous souhaitez spécifier un abonnement spécifique à utiliser pour Azure Key Vault, tapez la commande suivante pour afficher les abonnements de votre compte :

```
az account list
```

Ensuite, pour indiquer l’abonnement, tapez ce qui suit :

```
az account set --subscription <subscription name or ID>
```

Pour plus d’informations sur la configuration de l’interface de ligne de commande multiplateforme Azure, consultez la page [Installation de l’interface de ligne de commande multiplateforme Azure](/cli/azure/install-azure-cli).

## <a name="create-a-new-resource-group"></a>Création d’un groupe de ressources
Lorsque vous utilisez Azure Resource Manager, toutes les ressources associées sont créées au sein d’un groupe de ressources. Nous allons créer un groupe de ressources nommé « ContosoResourceGroup » pour ce didacticiel.

```
az group create -n 'ContosoResourceGroup' -l 'East Asia'
```

Le premier paramètre est le nom de groupe de ressources et le deuxième paramètre est l’emplacement. Pour l’emplacement, utilisez la commande `az account list-locations` pour savoir comment spécifier un autre emplacement que celui de cet exemple. Si vous avez besoin de plus d’informations, tapez : `az account list-locations -h`.

## <a name="register-the-key-vault-resource-provider"></a>Inscription du fournisseur de ressources Key Vault
Assurez-vous que le fournisseur de ressources Key Vault est inscrit dans votre abonnement :

```
az provider register -n Microsoft.KeyVault
```

Cette opération ne doit être effectuée qu'une fois par abonnement.

## <a name="create-a-key-vault"></a>Création d’un coffre de clés
Utilisez la commande `az keyvault create` pour créer un coffre de clés. Ce script a trois paramètres obligatoires : un nom de groupe de ressources, un nom de coffre de clés et l’emplacement géographique.

Par exemple, si vous utilisez le nom de coffre ContosoKeyVault, le nom de groupe de ressources ContosoResourceGroup et l’emplacement Asie de l’Est, tapez :
```
az keyvault create --name 'ContosoKeyVault' --resource-group 'ContosoResourceGroup' --location 'East Asia'
```

La sortie de cette commande affiche les propriétés du coffre de clés que vous venez de créer. Les deux propriétés les plus importantes sont :

* **Nom**: dans l’exemple : ContosoKeyVault. Vous allez utiliser ce nom pour les autres commandes Key Vault.
* **vaultUri** : dans l’exemple, il s’agit de https://contosokeyvault.vault.azure.net/. Les applications qui utilisent votre coffre via son API REST doivent utiliser cet URI.

Votre compte Azure est pour l’instant le seul autorisé à effectuer des opérations sur ce coffre de clés.

## <a name="add-a-key-or-secret-to-the-key-vault"></a>Ajout d’une clé ou d’un secret au coffre de clés
Si vous souhaitez qu’Azure Key Vault crée pour vous une clé protégée par logiciel, utilisez la commande `az key create` et tapez ce qui suit :
```
az keyvault key create --vault-name 'ContosoKeyVault' --name 'ContosoFirstKey' --protection software
```
Toutefois, si vous avez une clé existante dans un fichier .pem enregistré sous la forme d’un fichier local dans un fichier nommé softkey.pem que vous souhaitez télécharger dans Azure Key Vault, tapez la commande suivante pour importer la clé à partir du fichier .PEM qui protège la clé par logiciel dans le service Key Vault :
```
az keyvault key import --vault-name 'ContosoKeyVault' --name 'ContosoFirstKey' --pem-file './softkey.pem' --pem-password 'PaSSWORD' --protection software
```
Vous pouvez maintenant référencer la clé que vous avez créée ou téléchargée dans Azure Key Vault à l’aide de son URI. Utilisez **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey** pour toujours obtenir la version actuelle, et **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87** pour obtenir cette version spécifique.

Pour ajouter un secret dans le coffre, c’est-à-dire un mot de passe nommé SQLPassword avec la valeur Pa$$w0rd dans Azure Key Vault, tapez le code suivant :
```
az keyvault secret set --vault-name 'ContosoKeyVault' --name 'SQLPassword' --value 'Pa$$w0rd'
```
Vous pouvez maintenant référencer ce mot de passe que vous avez ajouté dans Azure Key Vault à l’aide de son URI. Utilisez **https://ContosoVault.vault.azure.net/secrets/SQLPassword** pour toujours obtenir la version actuelle, et **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d** pour obtenir cette version spécifique.

Examinons la clé ou le secret que vous venez de créer :

* Pour afficher votre clé, tapez : `az keyvault key list --vault-name 'ContosoKeyVault'`
* Pour afficher votre secret, tapez : `az keyvault secret list --vault-name 'ContosoKeyVault'`

## <a name="register-an-application-with-azure-active-directory"></a>Inscription d’une application auprès d’Azure Active Directory
Cette étape est généralement effectuée par un développeur et sur un ordinateur distinct. Bien que non spécifique d’Azure Key Vault, elle est incluse ici par souci d’exhaustivité.

> [!IMPORTANT]
> Pour suivre le didacticiel, le compte, le coffre et l’application que vous inscrivez dans cette étape doivent tous se trouver dans le même répertoire Azure.
>
>

Les applications qui utilisent un coffre de clés doivent s’authentifier à l’aide d’un jeton à partir d’Azure Active Directory. Pour ce faire, le propriétaire de l’application doit d’abord inscrire l’application dans Azure Active Directory. À la fin de l’inscription, le propriétaire de l’application obtient les valeurs suivantes :

* Un **ID d’application** (également appelé ID de client) et une **clé d’authentification** (également appelée secret partagé). L’application doit présenter ces deux valeurs à Azure Active Directory afin d’obtenir un jeton. La manière dont l’application est configurée pour cela dépend de l’application en question. Pour l’exemple d’application de coffre de clés, le propriétaire de l’application définit ces valeurs dans le fichier app.config.

Pour inscrire votre application auprès d’Azure Active Directory :

1. Connectez-vous au portail Azure.
2. Dans le volet gauche, cliquez sur **Azure Active Directory**, puis sélectionnez le répertoire dans lequel vous allez inscrire votre application. <br> <br> 

> [!Note] 
> Vous devez sélectionner le répertoire qui contient l’abonnement Azure avec lequel vous avez créé votre coffre de clés. Si vous ne savez pas de quel répertoire il s’agit, cliquez sur **Paramètres**, identifiez l’abonnement avec lequel vous avez créé votre coffre de clés et notez le nom du répertoire affiché dans la dernière colonne.

3. Cliquez sur **APPLICATIONS**. Si aucune application n’a été ajoutée à votre répertoire, cette page affiche uniquement le lien **Ajouter une application**. Cliquez sur le lien. Vous pouvez également cliquer sur **AJOUTER** dans la barre de commandes.
4. Dans l’Assistant **AJOUTER UNE APPLICATION**, dans la page **Que voulez-vous faire ?**, cliquez sur **Ajouter une application développée par mon organisation**.
5. Dans la page **Parlez-nous de votre application**, spécifiez un nom pour votre application et sélectionnez **APPLICATION WEB ET/OU API WEB** (par défaut). Cliquez sur l’icône Suivant.
6. Dans la page **Propriétés de l’application**, spécifiez l’**URL DE CONNEXION** et l’**URI ID D’APPLICATION** pour votre application web. Si votre application n’a pas ces valeurs, vous pouvez les créer pour cette étape (par exemple, vous pouvez spécifier http://test1.contoso.com pour les deux zones). Peu importe si ces sites existent. L’important est que l’URI d’ID d’application est différent pour chaque application dans votre répertoire. Le répertoire utilise cette chaîne pour identifier votre application.
7. Cliquez sur l’icône Terminé pour enregistrer vos modifications dans l’Assistant.
8. Dans la page Démarrage rapide, cliquez sur **CONFIGURER**.
9. Accédez à la section **clés** , sélectionnez la durée, puis cliquez sur **ENREGISTRER**. La page est actualisée et affiche à présent une valeur de clé. Vous devez configurer votre application avec cette valeur de clé et la valeur d’ **ID CLIENT** . (Les instructions relatives à cette configuration sont propres à l’application).
10. Copiez la valeur d’ID client à partir de cette page. Vous l’utiliserez à l’étape suivante pour définir des autorisations sur votre coffre.

## <a name="authorize-the-application-to-use-the-key-or-secret"></a>Autorisation de l’application à utiliser la clé ou le secret
Pour autoriser l’application à accéder à la clé ou au secret dans le coffre, utilisez la commande `az keyvault set-policy` .

Par exemple, si le nom de votre coffre est ContosoKeyVault, que l’application que vous souhaitez autoriser a l’ID client 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed et que vous souhaitez autoriser l’application à déchiffrer et à signer avec des clés dans le coffre, exécutez la commande suivante :
```
az keyvault set-policy --name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --key-permissions decrypt sign
```

Si vous souhaitez autoriser cette même application à lire les éléments secrets de votre coffre, exécutez la commande suivante :
```
az keyvault set-policy --name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --secret-permissions get
```
## <a name="if-you-want-to-use-a-hardware-security-module-hsm"></a>Si vous souhaitez utiliser un module de sécurité matériel (HSM)
Pour une meilleure garantie, vous pouvez importer ou générer des clés dans des modules de sécurité matériels (HSM) qui ne franchissent jamais les limites HSM. Les modules HSM bénéficient d’une validation FIPS 140-2 de niveau 2. Si cette exigence ne s’applique pas à vous, ignorez cette section et accédez à [Supprimer le coffre de clés et les clés et secrets associés](#delete-the-key-vault-and-associated-keys-and-secrets).

Pour créer ces clés protégées par HSM, vous devez disposer d'un abonnement de coffre qui prend en charge les clés protégées par HSM.

Lorsque vous créez le coffre de clés, ajoutez le paramètre « SKU » :

```
az keyvault create --name 'ContosoKeyVaultHSM' --resource-group 'ContosoResourceGroup' --location 'East Asia' --sku 'Premium'
```
Vous pouvez ajouter des clés protégées par logiciel (comme indiqué plus haut) et des clés protégées par HSM dans ce coffre. Pour créer une clé protégée par HSM, définissez le paramètre Destination sur « HSM » :

```
az keyvault key create --vault-name 'ContosoKeyVaultHSM' --name 'ContosoFirstHSMKey' --protection 'hsm'
```

Vous pouvez utiliser la commande suivante pour importer une clé à partir d’un fichier pem sur votre ordinateur. Cette commande importe la clé dans les modules de sécurité matériels dans le service Key Vault :

```
az keyvault key import --vault-name 'ContosoKeyVaultHSM' --name 'ContosoFirstHSMKey' --pem-file '/.softkey.pem' --protection 'hsm' --pem-password 'PaSSWORD'
```
La commande suivante importe un package BYOK (Bring Your Own Key, Apporter votre propre clé). Cela vous permet de générer votre clé dans votre module de sécurité matériel local et de la transférer vers les modules de sécurité matériels du service Key Vault, sans que la clé quitte la limite HSM :

```
az keyvault key import --vault-name 'ContosoKeyVaultHSM' --name 'ContosoFirstHSMKey' --byok-file './ITByok.byok' --protection 'hsm'
```
Pour plus d’instructions sur la génération de ce package BYOK, consultez la page [Génération et transfert de clés protégées par HSM pour le coffre de clés Azure](key-vault-hsm-protected-keys.md).

## <a name="delete-the-key-vault-and-associated-keys-and-secrets"></a>Supprimer le coffre de clés et les clés et secrets associés
Si vous n’avez plus besoin du coffre de clés ni de la clé ou du secret qu’il contient, vous pouvez supprimer le coffre de clés à l’aide de la commande `az keyvault delete` :

```
az keyvault delete --name 'ContosoKeyVault'
```

Vous pouvez également supprimer un groupe de ressources Azure, qui inclut le coffre de clés et les autres ressources incluses dans ce groupe :

```
az group delete --name 'ContosoResourceGroup'
```

## <a name="other-azure-cross-platform-command-line-interface-commands"></a>Autres interfaces de ligne de commande interplateformes Azure
Autres commandes qui peuvent être utiles pour la gestion d’Azure Key Vault.

Cette commande permet d’obtenir un affichage sous forme de tableau de l’ensemble des clés et des propriétés sélectionnées :

az keyvault key list --vault-name 'ContosoKeyVault'

Cette commande affiche la liste complète des propriétés pour la clé spécifiée :

az keyvault key show --vault-name 'ContosoKeyVault' --name 'ContosoFirstKey'

Cette commande permet d’obtenir un affichage sous forme de tableau de l’ensemble des secrets et des propriétés sélectionnées.

az keyvault secret list --vault-name 'ContosoKeyVault'

Voici un exemple montrant comment supprimer une clé spécifique :

az keyvault key delete --vault-name 'ContosoKeyVault' --name 'ContosoFirstKey'

Voici un exemple montrant comment supprimer un secret spécifique :

az keyvault secret delete --vault-name 'ContosoKeyVault' --name 'SQLPassword'


## <a name="next-steps"></a>Étapes suivantes
Pour accéder à une documentation de référence complète sur l’interface Azure CLI pour les commandes Key Vault, consultez le document de [référence sur l’interface de ligne de commande Key Vault](/cli/azure/keyvault).

Pour les références de programmation, consultez le [guide du développeur de coffre de clés Azure](key-vault-developers-guide.md).

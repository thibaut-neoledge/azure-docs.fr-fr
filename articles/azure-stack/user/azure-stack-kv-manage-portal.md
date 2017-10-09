---
title: "Gérer Key Vault dans Azure Stack à l’aide de PowerShell | Microsoft Docs"
description: "Découvrez comment gérer Key Vault dans Azure Stack à l’aide de PowerShell."
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/10/2017
ms.author: sngun
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 41cbe1526368dd88fe98f92937c6ef2b65f24682
ms.contentlocale: fr-fr
ms.lasthandoff: 09/25/2017

---

# <a name="manage-key-vault-in-azure-stack-using-the-portal"></a>Gérer Key Vault dans Azure Stack à l’aide du portail

Vous pouvez gérer Key Vault dans Azure Stack à l’aide du portail Azure Stack. Cet article montre comment créer et gérer Key Vault dans Azure Stack. 

## <a name="prerequisites"></a>Composants requis  

* Vous devez vous abonner à une offre qui inclut le service Key Vault.  
 
## <a name="create-a-key-vault"></a>Création d’un coffre de clés 

1. Connectez-vous au portail utilisateur (https://portal.local.azurestack.external).  

2. Dans le tableau de bord, cliquez sur **Nouveau > Sécurité + identité > Key Vault**.  

    ![Écran de KV](media/azure-stack-kv-manage-portal/image1.png)  

3. Dans le panneau **Créer un coffre de clés**, affectez un **Nom** à votre coffre. Le nom du coffre peut contenir uniquement des caractères alphanumériques, le caractère spécial de trait d’union (-), et il ne doit pas commencer par un chiffre.  

4. Choisissez un **Abonnement** dans la liste des abonnements disponibles. Tous les abonnements qui offrent le service Key Vault sont affichés dans la liste déroulante.  

5. Sélectionnez un **Groupe de ressources** existant ou créez-en un.  

6. Sélectionnez le **Niveau tarifaire**.  
    >[!NOTE]
    > Les coffres de clés dans le Kit de développement Azure Stack prennent uniquement en charge les SKU **Standard**.

7. Sélectionnez une **Stratégie d’accès** existante ou créez-en une. Une stratégie d’accès vous permet d’accorder des autorisations à un utilisateur, une application ou un groupe de sécurité pour effectuer des opérations avec ce coffre.  

8. Si vous le souhaitez, choisissez une **Stratégie d’accès avancée** pour activer des fonctionnalités telles que l’accès aux machines virtuelles pour le déploiement, l’accès à Resource Manager pour le déploiement de modèle et l’accès à Azure Disk Encryption pour le chiffrement de volume. 
  
9.  Après avoir configuré les paramètres, cliquez sur **OK**, puis sur **Créer**. Le déploiement de Key Vault débute. 

## <a name="manage-keys-and-secrets"></a>Gérer les clés et les secrets

Après avoir créé un coffre, effectuez les étapes suivantes pour créer et gérer les clés et les secrets dans le coffre.

## <a name="create-a-key"></a>Créer une clé

1. Connectez-vous au portail utilisateur (https://portal.local.azurestack.external).  

2. Dans le tableau de bord, cliquez sur **Toutes les ressources** > sélectionnez le coffre de clés que vous avez créé > cliquez sur la vignette **Clés**.  

3. Dans le panneau **Clés**, cliquez sur **Ajouter**. 

4. Dans le panneau **Créer une clé**, dans la liste d’**Options**, choisissez la méthode que vous souhaitez utiliser pour créer une clé. Vous pouvez **Générer** une nouvelle clé, **Charger** une clé existante ou **Restaurer la sauvegarde** d’une clé.  

5. Entrez un **Nom** pour votre clé. Le nom de la clé peut contenir uniquement des caractères alphanumériques et le caractère spécial de trait d’union (-).  

6. Éventuellement, configurez les valeurs **Définir la date d’activation** et **Définir la date d’expiration** pour votre clé.  

7. Cliquez sur **Créer** pour commencer le déploiement.  

Une foi la clé créée, vous pouvez la sélectionner dans le panneau **Clés** et afficher ou modifier ses propriétés. La section Propriétés contient l’**Identificateur de clé**, un URI par lequel les applications externes peuvent accéder à cette clé. Pour limiter les opérations sur cette clé, configurez les paramètres sous **Opérations autorisées**.

![URI de clé](media/azure-stack-kv-manage-portal/image4.png)  

## <a name="create-a-secret"></a>Créer un secret 

1. Connectez-vous au portail utilisateur (https://portal.local.azurestack.external).  
2. Dans le tableau de bord, cliquez sur **Toutes les ressources** > sélectionnez le coffre de clés que vous avez créé > cliquez sur la vignette **Secrets**.  

3. Dans le panneau **Secrets**, cliquez sur **Ajouter**.  

4. Dans le panneau **Créer une clé secrète**, dans la liste **Options de chargement**, choisissez une option pour créer une clé secrète. Vous pouvez créer un secret **manuellement** en entrant une valeur pour le secret ou en chargeant un **certificat** à partir de votre ordinateur local.  

5. Entrez un **Nom** pour le secret. Le nom du secret peut contenir uniquement des caractères alphanumériques et le caractère spécial de trait d’union (-).  

6. Éventuellement, spécifiez le **Type de contenu** et configurez les valeurs **Définir la date d’activation** et **Définir la date d’expiration** pour le secret.  

7. Cliquez sur Créer pour commencer le déploiement.  

Une foi le secret créé, vous pouvez le sélectionner dans le panneau **Secrets** et afficher ou modifier ses propriétés. La section Propriétés contient l’**Identificateur de secret**, un URI par lequel les applications externes peuvent accéder à ce secret. 

![URI de secret](media/azure-stack-kv-manage-portal/image5.png) 


## <a name="next-steps"></a>Étapes suivantes
* [Déployer une machine virtuelle en récupérant le mot de passe stocké dans un coffre de clés](azure-stack-kv-deploy-vm-with-secret.md)  
* [Déployer une machine virtuelle avec un certificat stocké dans un coffre de clés](azure-stack-kv-push-secret-into-vm.md)     




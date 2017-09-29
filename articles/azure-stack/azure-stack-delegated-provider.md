---
title: "Délégation des offres dans Azure Stack | Microsoft Docs"
description: "Découvrez comment placer d’autres personnes en charge de la création d’offres et de l’inscription des utilisateurs."
services: azure-stack
documentationcenter: 
author: AlfredoPizzirani
manager: byronr
editor: 
ms.assetid: 157f0207-bddc-42e5-8351-197ec23f9d46
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: alfredop
ms.translationtype: HT
ms.sourcegitcommit: 1c730c65194e169121e3ad1d1423963ee3ced8da
ms.openlocfilehash: af591c51fec3326941980dbe5ffb08ca432d1d51
ms.contentlocale: fr-fr
ms.lasthandoff: 09/15/2017

---
# <a name="delegating-offers-in-azure-stack"></a>Délégation des offres dans Azure Stack

En tant qu’opérateur cloud Azure Stack, vous souhaitez souvent placer d’autres personnes en charge de la création d’offres et de l’inscription des utilisateurs. Par exemple, si vous êtes un fournisseur de services et souhaitez que les revendeurs inscrivent les clients et les gèrent à votre place. Cela peut également être le cas dans une entreprise si vous faites partie d’un groupe informatique centralisé et souhaitez que des départements ou des filiales inscrivent les utilisateurs sans votre intervention.

La délégation vous aide dans ces tâches, ce qui vous permet d’atteindre et de gérer plus d’utilisateurs que vous ne pourriez le faire directement. L’illustration suivante montre un niveau de délégation, mais Azure Stack prend en charge plusieurs niveaux. Les fournisseurs délégués peuvent à leur tour déléguer à d’autres fournisseurs, et ce sur jusqu’à cinq niveaux.

![](media/azure-stack-delegated-provider/image1.png)

Les opérateurs cloud Azure Stack peuvent déléguer la création d’offres et de locataires à d’autres utilisateurs à l’aide de la fonctionnalité de délégation.

## <a name="roles-and-steps-in-delegation"></a>Rôles et étapes de la délégation
Pour comprendre la délégation, gardez à l’esprit qu’elle comprend trois rôles :

* **L’opérateur cloud** gère l’infrastructure Azure Stack, crée un modèle d’offre et délègue à d’autres la responsabilité de proposer l’offre à leurs utilisateurs.
* Les opérateurs Azure Stack délégués sont appelés **fournisseurs délégués**. Ils peuvent appartenir à d’autres organisations (comme d’autres locataires Azure Active Directory).
* Les **utilisateurs** s’inscrivent aux offres et les utilisent pour gérer leurs charges de travail, la création de machines virtuelles, le stockage des données, etc.

Comme indiqué dans l’illustration suivante, la configuration de la délégation inclut deux étapes.

1. **Identifier les fournisseurs délégués** en les abonnant à une offre basée sur un plan qui contient uniquement le service d’abonnements.
   Les utilisateurs qui s’abonnent à cette offre acquièrent certaines des fonctionnalités de l’opérateur Azure Stack, notamment la possibilité d’étendre les offres et d’y inscrire des utilisateurs.
2. **Déléguer une offre au fournisseur délégué**, cette offre fonctionne comme un modèle concernant ce que le fournisseur délégué peut offrir. Le fournisseur délégué est désormais en mesure de prendre l’offre, de choisir un nom pour celle-ci (mais pas d’en modifier les services et les quotas) et de la proposer aux clients.

![](media/azure-stack-delegated-provider/image2.png)

Pour agir en tant que fournisseurs délégués, les utilisateurs doivent établir une relation avec le fournisseur principal ; en d’autres termes, ils doivent créer un abonnement. Dans ce scénario, cet abonnement identifie les fournisseurs délégués comme ayant le droit de présenter des offres pour le compte du fournisseur principal.

Une fois que cette relation est établie, l’opérateur cloud peut déléguer une offre au fournisseur délégué. Le fournisseur délégué est désormais en mesure de prendre l’offre, de la renommer (sans en modifier la substance) et de la proposer à ses clients.

Les sections suivantes expliquent comment établir un fournisseur délégué, déléguer une offre et vérifier que les utilisateurs peuvent s’y inscrire.

## <a name="set-up-roles"></a>Configurer les rôles

Pour voir un fournisseur délégué au travail, vous avez besoin de comptes Azure Active Directory supplémentaires en plus de votre compte d’opérateur cloud. Si vous n’en avez pas, créez les deux comptes. Les comptes peuvent appartenir à n’importe quel client AAD. Nous les appelons le fournisseur délégué (DP) et l’utilisateur.

| **Rôle** | **Droits d’organisation** |
| --- | --- |
| Fournisseur délégué |Utilisateur |
| Utilisateur |Utilisateur |

## <a name="identify-the-delegated-providers"></a>Identifier les fournisseurs délégués
1. Connectez-vous en tant qu’opérateur cloud.
2. Créez l’offre qui permet aux utilisateurs de devenir des fournisseurs délégués. Pour ce faire, vous devez créer un plan et une offre basée sur celui-ci :
   
   a.  [Créer un plan](azure-stack-create-plan.md).
       Ce plan doit inclure uniquement le service d’abonnements. Dans cet article, nous utilisons un plan appelé PlanForDelegation.
   
   b.  [Créer une offre](azure-stack-create-offer.md) basée sur ce plan. Dans cet article, nous utilisons une offre appelée OfferToDP.
   
   c.  Une fois la création de l’offre terminée, ajoutez le fournisseur délégué en tant qu’abonné à cette offre en cliquant sur **Abonnements** &gt; **Ajouter** &gt; **Nouvel abonnement client**.
   
   ![](media/azure-stack-delegated-provider/image3.png)

> [!NOTE]
> Comme avec toutes les offres Azure Stack, vous avez la possibilité de rendre l’offre publique et de laisser les utilisateurs y souscrire, ou de conserver l’offre comme privée et demander à l’opérateur cloud de gérer l’inscription. Les fournisseurs délégués appartiennent généralement à un petit groupe et vous souhaitez contrôler qui y est admis, donc le fait de conserver cette offre privée est logique dans la plupart des cas.
> 
> 

## <a name="cloud-operator-creates-the-delegated-offer"></a>L’opérateur cloud crée l’offre déléguée

Vous avez désormais établi votre fournisseur délégué. L’étape suivante consiste à créer le plan et l’offre que vous allez déléguer et que vos clients utiliseront. Vous devez définir cette offre telle que vous souhaitez que les clients la voient, car le fournisseur délégué ne sera pas en mesure d’en modifier les plans et les quotas.

1. En tant qu’opérateur cloud, [créez un plan](azure-stack-create-plan.md) et [une offre](azure-stack-create-offer.md) basée sur celui-ci. Dans cet article, nous utilisons une offre appelée DelegatedOffer.
   
   > [!NOTE]
   > Cette offre ne doit pas nécessairement être publique. Elle peut être rendue publique si vous le souhaitez, mais, dans la plupart des cas, vous souhaitez que seuls les fournisseurs délégués y aient accès. Une fois que vous déléguez une offre privée comme décrit dans les étapes suivantes, le fournisseur délégué y a accès.
   > 
   > 
1. Déléguez l’offre. Accédez à DelegatedOffer et, dans le volet Paramètres, cliquez sur **Fournisseurs délégués** &gt; **Ajouter**.
2. Sélectionnez l’abonnement du fournisseur délégué dans la zone de liste déroulante et cliquez sur **Déléguer**.

> ![](media/azure-stack-delegated-provider/image4.png)
> 
> 

## <a name="delegated-provider-customizes-the-offer"></a>Le fournisseur délégué personnalise l’offre

Connectez-vous au **portail du locataire** en tant que fournisseur délégué et créez une offre en utilisant l’offre déléguée comme modèle.

1. Cliquez sur **Nouveau** &gt; **Offres + Plans clients** &gt; **Offre**.

    ![](media/azure-stack-delegated-provider/image5.png)


1. Attribuez un nom à l’offre. Ici, nous avons choisi le nom ResellerOffer. Sélectionnez l’offre déléguée sur laquelle baser cette offre, puis cliquez sur **Créer**.
   
   ![](media/azure-stack-delegated-provider/image6.png)

    >[!NOTE] 
    > Notez la différence par rapport à la création d’offre telle qu’elle est vécue par l’opérateur cloud. Le fournisseur délégué ne construit pas l’offre à partir de plans de base et de plans d’extension ; il ne peut choisir que parmi des offres qui lui ont été déléguées et ne peut pas apporter des modifications à ces offres.

1. Rendez l’offre publique en cliquant sur **Parcourir** &gt; **Offres**, en sélectionnant l’offre et en cliquant sur **Modifier l’état**.
2. Le fournisseur délégué expose ces offres via l’URL de son propre portail. Ces offres sont visibles uniquement via le portail délégué. Pour rechercher et modifier cette URL :
   
    a.  Cliquez sur **Parcourir**&gt; **Plus de services**&gt; **Abonnements**&gt; Sélectionnez l’abonnement du fournisseur délégué, dans notre cas il s’agit de *DPSubscription*&gt; **Propriétés**.
   
    b.  Copiez l’URL du portail vers un autre emplacement, comme le Bloc-notes.
   
    ![](media/azure-stack-delegated-provider/dpportaluri.png)  
   
   Vous avez maintenant terminé la création d’une offre déléguée en tant que fournisseur délégué. Déconnectez-vous en tant que fournisseur délégué. Fermez l’onglet de navigateur que vous avez utilisé.

## <a name="sign-up-for-the-offer"></a>S’inscrire à l’offre
1. Dans une nouvelle fenêtre de navigateur, accédez à l’URL du portail délégué que vous avez enregistrée à l’étape précédente. Connectez-vous au portail en tant qu’utilisateur. Remarque : utilisez le portail délégué pour cette étape. Sinon, l’offre déléguée ne s’affiche pas.
2. Dans le tableau de bord, cliquez sur **Prendre un abonnement**. Vous verrez que seules les offres déléguées créés par le fournisseur délégué sont présentées à l’utilisateur :

> ![](media/azure-stack-delegated-provider/image8.png)
> 
> 

Ceci conclut le processus de délégation de l’offre. L’utilisateur peut désormais s’inscrire à cette offre en prenant un abonnement à celle-ci.

## <a name="multiple-tier-delegation"></a>Délégation à plusieurs niveaux

La délégation à plusieurs niveaux permet au fournisseur délégué de déléguer l’offre à d’autres entités. Cela permet, par exemple, la création de réseaux de revendeurs plus approfondis, dans lesquels le fournisseur qui gère Azure Stack délègue une offre à un distributeur, qui à son tour délègue au revendeur.
Azure Stack prend en charge jusqu’à cinq niveaux de délégation.

Pour créer plusieurs niveaux de délégation de l’offre, le fournisseur délégué délègue à son tour l’offre au fournisseur suivant. Le processus pour le fournisseur délégué est similaire à celui pour l’opérateur cloud (consultez [L’opérateur cloud crée l’offre déléguée](#cloud-operator-creates-the-delegated-offer)).

## <a name="next-steps"></a>Étapes suivantes
[Approvisionner une machine virtuelle](azure-stack-provision-vm.md)



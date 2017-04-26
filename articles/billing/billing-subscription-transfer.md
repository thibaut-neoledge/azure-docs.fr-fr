---
title: "Transfert de la propriété de l’abonnement Azure à un autre compte | Microsoft Docs"
description: "Décrit comment transférer un abonnement Azure à un autre utilisateur et Forum Aux Questions (FAQ) concernant la procédure"
keywords: "transférer abonnement azure, azure transfert abonnement, déplacer un abonnement azure vers un autre compte, changer abonnement azure, transférer abonnement azure à un autre compte"
services: 
documentationcenter: 
author: genlin
manager: jlian
editor: 
tags: billing,top-support-issue
ms.assetid: c8ecdc1e-c9c5-468c-a024-94ae41e64702
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2017
ms.author: genli
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 0b53a5ab59779dc16825887b3c970927f1f30821
ms.openlocfilehash: e64c0336578be2c1a1126f7459775f47d73f9d6e
ms.lasthandoff: 04/07/2017

---
# <a name="transfer-ownership-of-an-azure-subscription-to-another-account"></a>Transfert de la propriété d’un abonnement Azure à un autre compte

Vous pouvez transférer votre abonnement à un autre utilisateur pour les abonnements de type paiement à l’utilisation, Visual Studio, Action Pack ou BizSpark dans le centre des comptes. Nous prenons en charge le transfert de services externes Azure également pour ces types d’abonnements. 

Vous pouvez envisager de transférer la propriété d’un abonnement Azure :

* si vous devez transférer la propriété de facturation de votre abonnement Azure à quelqu’un d’autre ;
* si vous souhaitez modifier le compte utilisé pour vous connecter à Azure (vous avez peut-être utilisé votre compte Microsoft au lieu de votre compte professionnel ou scolaire) ;
* si vous voulez déplacer votre abonnement Azure d’un répertoire à un autre ;
* si vous souhaitez consolider les différents clients que vous avez actuellement pour Azure et Office 365.

Pour modifier votre abonnement et changer d’offre, consultez la page [Changer d’offre pour votre abonnement Azure](billing-how-to-switch-azure-offer.md). 

## <a name="transfer-ownership-of-an-azure-subscription"></a>Transfert de la propriété d’un abonnement Azure
> [!VIDEO https://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/Transfer-an-Azure-subscription/player]
>
>

1. Accédez à <https://account.windowsazure.com/Subscriptions>. Vous devez être l’administrateur de compte pour effectuer un transfert de propriété. Pour déterminer qui est l’administrateur de compte de l’abonnement, consultez le [Forum aux questions](#faq).

2. Sélectionnez l’abonnement à transférer.

3. Cliquez sur l’option **Transférer un abonnement** . Si vous ne voyez pas apparaître la commande, cliquez sur [FAQ](#no-button).

   ![Onglet Abonnements du compte Azure](./media/billing-subscription-transfer/image1.png)
4. Spécifiez le destinataire.

   ![Boîte de dialogue Transférer un abonnement](./media/billing-subscription-transfer/image2.PNG)
5. Le destinataire reçoit automatiquement un e-mail contenant un lien d’acceptation.

   ![E-mail de transfert d’abonnement envoyé au destinataire](./media/billing-subscription-transfer/image3.png)
6. Le destinataire clique sur le lien et suit les instructions, notamment pour la saisie des informations de paiement.

   ![Page web de transfert du premier abonnement](./media/billing-subscription-transfer/image4.png)

   ![Page web de transfert du deuxième abonnement](./media/billing-subscription-transfer/image5.png)
7. Vous avez réussi ! L’abonnement est à présent transféré.

## <a name="transfer-subscription-ownership-for-enterprise-agreement-ea-customers"></a>Transférer la propriété de l’abonnement pour les clients Contrat Entreprise (EA)
L’administrateur d’entreprise peut transférer la propriété des abonnements au sein d’une inscription. Pour commencer, consultez la rubrique [Transférer la propriété du compte](https://ea.azure.com/helpdocs/changeAccountOwnerForASubscription) dans le portail EA.

## <a name="next-steps-after-accepting-ownership-of-a-subscription"></a>Étapes suivantes après avoir accepté la propriété d’un abonnement
1. Vous êtes désormais l’administrateur de compte. Examinez et mettez à jour l’administrateur du service et les coadministrateurs. Gérez les administrateurs dans le [portail Azure Classic](https://manage.windowsazure.com) en accédant aux paramètres. [En savoir plus sur les rôles d’administrateur](billing-add-change-azure-subscription-administrator.md).

2. Vous pouvez également utiliser le contrôle d’accès en fonction du rôle (RBAC) pour votre abonnement et vos services. Visitez le [portail Azure](https://portal.azure.com). [Pour en savoir plus sur le contrôle d’accès en fonction du rôle](../active-directory/role-based-access-control-configure.md)

3. Mettez à jour les informations d’identification associées aux services de cet abonnement, notamment :
   
   * Certificats de gestion accordant à l’utilisateur des droits d’administrateur sur les ressources d’abonnement. Pour plus d'informations, consultez [Create and upload a management certificate for Azure](../cloud-services/cloud-services-certs-create.md)
   
   * Touches d’accès rapide pour les services tels que Storage. Pour plus d’informations, consultez [À propos des comptes de stockage Azure](../storage/storage-create-storage-account.md).
   
   * Informations d’identification d’accès à distance pour les services tels que les machines virtuelles Azure. 

4. [Mettez à jour les alertes de facturation pour cet abonnement](billing-set-up-alerts.md) dans le [Centre des comptes Azure](https://account.windowsazure.com/Subscriptions). 

5. Si vous collaborez avec un partenaire, envisagez de mettre à jour son ID de partenaire pour cet abonnement. Vous pouvez mettre à jour l’ID partenaire dans le [Centre des comptes Azure](https://account.windowsazure.com/Subscriptions).

<a id="faq"></a>

## <a name="frequently-asked-questions-faq"></a>Forum Aux Questions (FAQ)
* <a name="whoisaa"></a> **Qui est l’administrateur de compte de l’abonnement ?**

  L’administrateur de compte est la personne qui s’est inscrite ou a acheté l’abonnement Azure. Il est autorisé à accéder au [Centre des comptes](https://account.windowsazure.com/Home/Index) et à effectuer diverses tâches de gestion comme créer des abonnements, annuler des abonnements, modifier la facturation d’un abonnement et modifier l’administrateur de services fédérés. Si vous ne savez pas qui est l’administrateur de compte d’un abonnement, procédez comme suit pour le découvrir.

  1. Connectez-vous au [portail Azure](https://portal.azure.com).
  2. Dans le menu Hub, sélectionnez **Abonnement**.
  3. Sélectionnez l’abonnement que vous souhaitez vérifier, puis regardez sous **Paramètres**.
  4. Sélectionner **Propriétés**. L’administrateur de compte de l’abonnement s’affiche dans la zone **Administrateur de compte** .  

* **Tous les éléments sont-ils transférés ? Notamment les groupes de ressources, les machines virtuelles, les disques et les autres services en cours d’exécution ?**

  Oui, toutes les ressources telles que les machines virtuelles, les disques, et les sites web sont transférées vers le nouveau propriétaire. Cependant, les [rôles d’administrateur](billing-add-change-azure-subscription-administrator.md) et les stratégies de [contrôle d’accès en fonction du rôle (RBAC)](../active-directory/role-based-access-control-configure.md) que vous avez configurés ne sont pas transférés dans les différents annuaires.

* <a id="no-button"></a>**Pourquoi le bouton Transférer un abonnement ne s’affiche-t-il pas ?**

  Si vous ne voyez pas le bouton **Transférer un abonnement**, cela signifie que le transfert d’abonnement n’est pas pris en charge pour votre offre. [Contacter le support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

* **Un transfert d’abonnement entraîne-t-il une interruption de service ?**

  Le transfert n’a aucune incidence sur le service. Le transfert de l’abonnement annule l’abonnement sous l’administrateur de compte actuel et crée un abonnement sous le compte du destinataire. Le nouvel abonnement est associé aux services Azure sous-jacents. L’ID de l’abonnement reste inchangé.

* **Comment utiliser ce processus pour modifier le répertoire d’abonnement ?**

  Un abonnement Azure est créé dans le répertoire auquel appartient l’administrateur de compte. Pour modifier le répertoire, transférez l’abonnement sur un compte d’utilisateur situé dans le répertoire cible. Lorsque cet utilisateur exécute les étapes pour accepter le transfert, l’abonnement transite automatiquement vers le répertoire cible.

* **Si je deviens titulaire d’un abonnement d’une autre organisation, cette dernière a-t-elle toujours accès à mes ressources ?**

  Si l’abonnement est transféré à un autre client, les utilisateurs associés au client précédent perdent l’accès à l’abonnement. Même si un utilisateur n’est plus administrateur ou coadministrateur des services, il pourra toujours accéder à l’abonnement par le biais d’autres mécanismes de sécurité, notamment :

  * Certificats de gestion accordant à l’utilisateur des droits d’administrateur sur les ressources d’abonnement. Pour plus d'informations, consultez la rubrique [Créer et télécharger un certificat de gestion pour Microsoft Azure](../cloud-services/cloud-services-certs-create.md).
  * Touches d’accès rapide pour les services tels que Storage. Pour plus d’informations, consultez la rubrique [À propos des comptes de stockage Azure](../storage/storage-create-storage-account.md).
  * Informations d’identification d’accès à distance pour les services tels que les machines virtuelles Azure.

 Le destinataire doit envisager la mise à jour des clés secrètes associées au service s’il doit restreindre l’accès aux ressources. La plupart des ressources peuvent être mises à jour en procédant comme suit :

    1. Accédez au [portail Azure](https://portal.azure.com).
    2. Dans le menu Hub, sélectionnez **Toutes les ressources**.
    3. Sélectionnez la ressource. 
    4. Dans le panneau des ressources, cliquez sur **Paramètres**. Ici, vous pouvez afficher et mettre à jour les clés secrètes existantes.

* **Si je transfère l’abonnement au milieu d’un cycle de facturation, le destinataire paye-t-il pour l’intégralité du cycle ?**

  L’expéditeur est responsable du paiement pour toute opération déclarée avant l’achèvement du transfert. Le destinataire est responsable des opérations déclarées à compter de la réalisation effective du transfert. Certaines opérations peuvent avoir été effectuées avant le transfert mais déclarées ultérieurement. L’utilisation figure sur la facture du destinataire.

* **Le destinataire a-t-il accès à l’historique des opérations et de facturation ?**

  Les seules informations accessibles au destinataire sont le montant de la dernière facture, ou le solde actuel si l’abonnement a été transféré avant l’émission de la première facture. L’utilisation restante et l’historique de facturation ne sont pas transférés avec l’abonnement.

* **L’offre peut-elle être modifiée durant un transfert ?**

  L’offre doit rester inchangée. Pour modifier votre offre, consultez la page [Changer d’offre pour votre abonnement Azure](billing-how-to-switch-azure-offer.md).

* **Puis-je transférer un abonnement sur un compte d’utilisateur dans un autre pays ?**

  Non, le transfert d’un abonnement sur un compte d’utilisateur dans un autre pays n’est pas pris en charge. Le compte d’utilisateur du destinataire doit se trouver dans le même pays.

* **Le destinataire peut-il utiliser un autre mode de paiement ?**

  Oui. Mais l’historique de facturation de l’abonnement est désormais réparti entre deux comptes.  

* **Le transfert d’un abonnement Azure a-t-il des conséquences sur le mode de paiement ?**

  Afin d’accepter un transfert d’abonnement, une carte de crédit ou un mode de paiement similaire doit être fourni pour le paiement de l’abonnement. Par exemple, si Bob transfère un abonnement à Jane et que Jane accepte le transfert, Jane doit également fournir un mode de paiement qu’elle a utilisé pour payer l’abonnement. Une fois le transfert terminé, Jane reçoit une facture pour l’abonnement, et non Bob.

* **Comment migrer des données et des services de mon abonnement Azure vers un nouvel abonnement ?**

  Si vous ne pouvez pas transférer la propriété de l’abonnement, vous pouvez migrer vos ressources manuellement. Consultez la page [Déplacer des ressources vers un nouveau groupe de ressources ou un nouvel abonnement](../azure-resource-manager/resource-group-move-resources.md).



## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contactez le support technique.
Si vous avez besoin d’aide, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème. 




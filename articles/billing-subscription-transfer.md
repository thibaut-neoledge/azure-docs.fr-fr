<properties
   pageTitle="Transfert de propriété d’un abonnement Azure | Microsoft Azure"
   description="Transfert d’un abonnement Azure à un autre utilisateur et Forum Aux Questions (FAQ) concernant la procédure"
   services=""
   documentationCenter=""
   authors="genlin"
   manager="stevenpo"
   editor="n/a"
   tags="billing,top-support-issue"/>

<tags
   ms.service="billing"
   ms.workload="na"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="07/12/2016"
   ms.author="genli"/>

# Transfert de propriété d’un abonnement Azure

Souhaitez-vous :

- Transférer la facturation de votre abonnement sur quelqu’un d’autre ?
- Modifier le compte utilisé pour vous connecter à Azure ? Vous avez peut-être utilisé votre compte Microsoft au lieu de votre compte professionnel ou scolaire ?
- Déplacer votre abonnement Azure d’un répertoire vers un autre ?
- Consolider les différents clients que vous avez actuellement pour Azure et Office 365 ?

Vous pouvez désormais effectuer aisément ces actions via le Centre des comptes Microsoft Azure pour les abonnements de type paiement à l'utilisation, MSDN, Action Pack ou BizSpark. Nous avons ajouté une fonction permettant de transférer votre abonnement vers un autre utilisateur. En d'autres termes, vous pouvez maintenant modifier l'administrateur de compte pour n'importe quel abonnement de type paiement à l'utilisation, MSDN, Action Pack ou BizSpark dont vous êtes titulaire, quel que soit le pays dans lequel vous travaillez. Nous prenons désormais en charge le transfert d’achats Azure Marketplace pour ces types d’abonnements également.

> [AZURE.NOTE]  Pour modifier votre abonnement à une autre offre, consultez les informations détaillées de la rubrique [Changement d’offre Azure](billing-how-to-switch-azure-offer.md).

> Vous devez être l’administrateur du compte pour effectuer un transfert de propriété. Pour plus d’informations sur la manière de déterminer qui est l’administrateur du compte de l’abonnement, consultez la [Forum aux questions](#faq).

## Transfert de propriété d’un abonnement Azure

> [AZURE.VIDEO transfer-an-azure-subscription]

1.  Connectez-vous à <https://account.windowsazure.com/Subscriptions>.

2.  Sélectionnez l’abonnement à transférer.

3.  Cliquez sur l’option **Transférer l’abonnement**.

    ![Onglet Abonnements du compte Azure](./media/billing-subscription-transfer/image1.png)

4.  Suivez les invites pour spécifier le destinataire.

    ![Boîte de dialogue Transférer l’abonnement](./media/billing-subscription-transfer/image2.PNG)

5.  Le destinataire reçoit automatiquement un e-mail contenant un lien d’acceptation.

    ![E-mail de transfert d’abonnement envoyé au destinataire](./media/billing-subscription-transfer/image3.png)

6.  Le destinataire clique sur le lien et suit les instructions, notamment pour la saisie des informations de paiement.

    ![Page web de transfert du premier abonnement](./media/billing-subscription-transfer/image4.PNG)

    ![Page web de transfert du deuxième abonnement](./media/billing-subscription-transfer/image5.PNG)

7. Vous avez réussi ! L’abonnement est à présent transféré.

<a id="faq"></a>
## Forum Aux Questions (FAQ)

-   **Comment vérifier qui est l’administrateur du compte de l’abonnement ?**

    Vous pouvez vérifier qui est l’administrateur du compte de l’abonnement comme suit :

    1. Connectez-vous au [portail Azure](https://portal.azure.com).
    2. Dans le menu Hub, sélectionnez **Abonnement**.
    3. Sélectionnez l’abonnement que vous souhaitez vérifier, puis sélectionnez **Paramètres**.
    4. Sélectionner **Propriétés**. L’administrateur du compte de l’abonnement s’affiche dans la zone **Administrateur de compte**.

-   **Un transfert d’abonnement entraîne-t-il une interruption de service ?**

    Le transfert n’a aucune incidence sur le service. Il annule l’abonnement de l’administrateur de compte actuel et en crée un nouveau sous le compte du destinataire, mais associe les services Azure sous-jacents avec ce nouvel abonnement. L’ID de l’abonnement reste inchangé.

-   **Comment utiliser ce mécanisme pour modifier le répertoire d'abonnement ?**- Un abonnement Azure est créé dans le répertoire auquel le compte administrateur appartient. Par conséquent, pour modifier le répertoire, il suffit de transférer l'abonnement à un compte d'utilisateur situé dans le répertoire cible. Lorsque cet utilisateur exécute les étapes pour accepter le transfert, l'abonnement se déplace automatiquement vers le répertoire cible.

-   **Si je deviens titulaire d’un abonnement d’une autre organisation, cette dernière aura-t-elle toujours accès à mes ressources ?**

    Si l’abonnement est transféré à un autre client, les utilisateurs associés au client précédent perdront l’accès à l’abonnement. Même si un utilisateur n’est plus administrateur ou coadministrateur des services, il pourra toujours accéder à l’abonnement via d’autres mécanismes de sécurité. Il s’agit des actions suivantes :
    - Certificats de gestion accordant à l’utilisateur des droits d’administrateur pour les ressources d’abonnement. Pour plus d'informations, consultez [Créer et télécharger un certificat de gestion pour Microsoft Azure](https://msdn.microsoft.com/library/azure/gg551722.aspx).
    -	Touches d’accès rapide pour les services tels que Storage. Pour plus d'informations, consultez [Affichage, copie et régénération de clés d'accès de stockage](storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).
    -	Informations d’accès à distance pour les services tels que Azure Virtual Machines

    Cette liste n’est pas exhaustive. Le destinataire doit envisager la mise à jour des clés secrètes associées au service s’il doit restreindre l’accès aux ressources. La plupart des ressources peuvent être mises à jour comme suit :

    1.   Accédez au portail Azure : [*https://portal.azure.com*](https://portal.azure.com)

    2.    Cliquez sur Parcourir tout -&gt; Toutes les ressources.

    3.    Sélectionnez la ressource. Le panneau des ressources s’ouvre.

    4.    Dans le panneau des ressources, cliquez sur **Paramètres**. Ici, vous pouvez afficher et mettre à jour les clés secrètes existantes.


-   **Si je transfère l’abonnement au milieu d’un cycle de facturation, le destinataire paye-t-il pour l’intégralité du cycle de facturation ?**

    L’expéditeur est responsable du paiement pour toute opération déclarée avant l’achèvement du transfert. Le destinataire est responsable des opérations déclarées à compter de la réalisation effective du transfert. Certaines opérations peuvent avoir été effectuées avant le transfert mais déclarées ultérieurement. Elles figureront sur la facture du destinataire.

-   **Le destinataire a-t-il accès à l’historique des opérations et de facturation ?**

    À ce stade, les seules informations divulguées au destinataire sont le montant de la dernière facture (ou le solde actuel si l’abonnement a été transféré avant l’émission de la première facture). Le reste de l’historique des opérations et de facture n’est pas transféré avec l’abonnement.

-   **L’offre peut-elle être modifiée durant un transfert ?**

    L’offre doit rester inchangée. Pour modifier votre offre, vous devez [contacter le support technique](http://go.microsoft.com/fwlink/?LinkID=619338).

-   **Puis-je transférer un abonnement à un compte d’utilisateur dans un autre pays ?**

    Non, à ce stade, ce n’est pas pris en charge. Le compte d’utilisateur du destinataire doit être dans le même pays.

-   **Le destinataire peut-il utiliser un autre mode de paiement ?**

    Oui. Les limites sont les suivantes : l’historique de facturation de l’abonnement est désormais réparti entre deux comptes. L'avantage, c'est que vous pouvez effectuer cette procédure sans avoir à [contacter le support technique](http://go.microsoft.com/fwlink/?LinkID=619338).

-   **Le transfert d’un abonnement Azure peut-il avoir des conséquences sur le mode de paiement ?**

    Afin d'accepter un transfert d'abonnement, une carte de crédit ou un mode de paiement similaire doit être fourni pour le paiement de l'abonnement. Par exemple, si Bob transfère un abonnement à Jane et que Jane accepte le transfert, Jane doit également fournir le mode de paiement qu'elle utilisera pour payer l'abonnement. Une fois le transfert terminé, Bob ne sera plus facturé pour l'abonnement qu'il a transféré à Jane.

## Étapes suivantes après avoir accepté la propriété d’un abonnement

1. Vous êtes désormais l’administrateur de compte. Examinez et mettez à jour l’administrateur du service et les coadministrateurs. Gérez les administrateurs dans le [portail Azure Classic](https://manage.windowsazure.com) en accédant aux paramètres. [En savoir plus](http://go.microsoft.com/fwlink/?LinkID=533293).
2. Vous pouvez également utiliser le contrôle d’accès en fonction du rôle (RBAC) pour vos abonnement et services. Visitez le [portail Azure](https://portal.azure.com) [En savoir plus sur le contrôle RBAC](http://go.microsoft.com/fwlink/?LinkID=544802)
3. Mettez à jour les informations d’identification associées aux services de cet abonnement. Il s’agit des actions suivantes :
    - Certificats de gestion accordant à l’utilisateur des droits d’administrateur pour les ressources d’abonnement. Pour plus d’informations, consultez [Créer et charger un certificat de gestion pour Azure](https://msdn.microsoft.com/library/azure/gg551722.aspx).
    -	Touches d’accès rapide pour les services tels que Storage. Pour plus d'informations, consultez [Affichage, copie et régénération de clés d'accès de stockage](storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).
    -	Informations d’accès à distance pour les services tels que Azure Virtual Machines
4. Mettez à jour les alertes de facturation pour cet abonnement, sur le [Centre des comptes Azure](https://account.windowsazure.com/Subscriptions) [En savoir plus](http://go.microsoft.com/fwlink/?LinkID=533292)
5. 	Si vous collaborez avec un partenaire, envisagez la mise à jour de l’ID de partenaire pour cet abonnement. Vous pouvez effectuer cette opération sur le [Centre des comptes Azure](https://account.windowsazure.com/Subscriptions).

<!---HONumber=AcomDC_0713_2016-->
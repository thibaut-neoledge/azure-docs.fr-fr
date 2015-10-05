<properties
   pageTitle="Création d’une identité professionnelle ou scolaire dans AAD | Microsoft Azure"
   description="Apprenez à créer une identité professionnelle ou scolaire dans Azure Active Directory, à utiliser avec les modèles de déploiement de Gestionnaire de ressources et classique."
   services="virtual-machines"
   documentationCenter=""
   authors="squillace"
   manager="timlt"
   editor=""
   tags="azure-service-management,azure-resource-manager"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure"
   ms.date="09/01/2015"
   ms.author="rasquill"/>

# Création d’une identité professionnelle ou scolaire dans Azure Active Directory

Si vous avez créé un compte Azure personnel ou si vous disposez d'un abonnement MSDN personnel et avez créé le compte Azure pour profiter des crédits Azure MSDN, alors vous avez utilisé une identité de *compte Microsoft* pour le créer. Pour fonctionner correctement, de nombreuses fonctionnalités d'Azure, parmi lesquelles les [modèles de groupes de ressources](../resource-group-overview.md), nécessitent un compte professionnel ou scolaire (une identité gérée par Azure Active Directory).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]Cet article traite de la création d’une identité AAD nécessaire pour le modèle de déploiement du Gestionnaire des ressources et le modèle de déploiement classique.


Heureusement, l’un des atouts de votre compte personnel Azure est qu'il est fourni avec un domaine Azure Active Directory par défaut que vous pouvez utiliser pour créer un nouveau compte professionnel ou scolaire à utiliser avec les fonctionnalités Azure qui le nécessitent.

> [AZURE.NOTE]Si un administrateur vous a donné un nom d’utilisateur et un mot de passe, il est probable que vous disposiez déjà d’un ID professionnel ou scolaire (parfois appelé *ID d’organisation*). Dans ce cas, vous pouvez commencer à utiliser votre compte Azure immédiatement pour accéder aux ressources Azure qui requièrent l'utilisation d'un compte. Si vous constatez que vous ne pouvez pas utiliser ces ressources, vous devrez peut-être revenir à cet article pour obtenir de l’aide. Pour plus d’informations, voir [Comptes que vous pouvez utiliser pour vous connecter](https://msdn.microsoft.com/library/azure/dn629581.aspx#BKMK_SignInAccounts) et [Association d’un abonnement Azure à Azure AD](https://msdn.microsoft.com/library/azure/dn629581.aspx#BKMK_SubRelationToDir).

Les étapes sont simples. Vous devez localiser votre identité de connexion sur le portail Azure, découvrir votre domaine Azure Active Directory par défaut et y ajouter un nouvel utilisateur en tant que coadministrateur Azure.

## Localisez votre répertoire par défaut sur le portail Azure

Commencez par vous connecter au [portail Azure](https://manage.windowsazure.com) avec votre identité de compte Microsoft personnel. Une fois que vous êtes connecté, faites défiler vers le bas le panneau bleu à gauche, puis cliquez sur **ACTIVE DIRECTORY**.

![Azure Active Directory](./media/resource-group-create-work-id-from-personal/azureactivedirectorywidget.png)

Commençons par rechercher des informations sur votre identité dans Azure. Des éléments semblables à ce qui suit doivent s'afficher dans le volet principal pour indiquer que vous avez un répertoire par défaut.

![](./media/resource-group-create-work-id-from-personal/defaultaadlisting.png)

Recherchons quelques informations supplémentaires. Cliquez sur la ligne de répertoire par défaut pour afficher les propriétés de répertoire par défaut.

![](./media/resource-group-create-work-id-from-personal/defaultdirectorypage.png)

Pour afficher le nom de domaine par défaut, cliquez sur **DOMAINES**.

![](./media/resource-group-create-work-id-from-personal/domainclicktoseeyourdefaultdomain.png)

Ici, vous devez être en mesure de voir que, lors de la création du compte Azure, Azure Active Directory a créé un domaine personnel par défaut qui est une valeur de hachage (nombre généré à partir d’une chaîne de texte) de votre ID personnel utilisé en tant que sous-domaine de onmicrosoft.com. C'est à ce domaine que vous allez maintenant ajouter un nouvel utilisateur.

## Création d'un nouvel utilisateur dans le domaine par défaut

Cliquez sur **UTILISATEURS** et recherchez votre compte personnel unique. Vous devez voir dans la colonne **PROVENANCE** qu’il s’agit d’un **compte Microsoft**. Nous voulons créer un utilisateur dans votre domaine Azure Active Directory .onmicrosoft.com par défaut.

![](./media/resource-group-create-work-id-from-personal/defaultdirectoryuserslisting.png)

Nous allons suivre [ces instructions](https://technet.microsoft.com/library/hh967632.aspx#BKMK_1) dans les étapes suivantes, mais utiliser un exemple spécifique.

Au bas de la page, cliquez sur **+AJOUTER UN UTILISATEUR**. Dans la page qui s’affiche, entrez le nouveau nom d’utilisateur et définissez **Type d’utilisateur** sur **Nouvel utilisateur dans votre organisation**. Dans cet exemple, le nouveau nom d'utilisateur est `ahmet`. Sélectionnez le domaine par défaut que vous avez découvert précédemment comme domaine pour l’adresse de messagerie d’ahmet. Lorsque vous avez terminé, cliquez sur la flèche Suivant.

![](./media/resource-group-create-work-id-from-personal/addingauserwithdirectorydropdown.png)

Ajoutez d’autres détails pour Ahmet, mais veillez à sélectionner la valeur **RÔLE** appropriée. Il est facile d'utiliser la valeur **Administrateur global** pour vous assurer que tout fonctionne, mais nous vous conseillons d'utiliser un rôle inférieur. Cet exemple utilise le rôle **Utilisateur**. (Apprenez-en plus sur ces rôles [ici](https://msdn.microsoft.com/library/azure/dn468213.aspx#BKMK_1).) N’activez pas l’authentification multifacteur, sauf si vous souhaitez l’utiliser pour chaque journal de l’opération. Lorsque vous avez terminé, cliquez sur la flèche Suivant.

![](./media/resource-group-create-work-id-from-personal/userprofileuseradmin.png)

Cliquez sur le bouton **Créer** pour générer et afficher un mot de passe temporaire pour Ahmet.

![](./media/resource-group-create-work-id-from-personal/gettemporarypasswordforuser.png)

Copiez l’adresse de messagerie du nom d’utilisateur, ou utilisez **ENVOYER UN MOT DE PASSE PAR MESSAGERIE ÉLECTRONIQUE**. Vous aurez rapidement besoin des informations pour vous connecter.

![](./media/resource-group-create-work-id-from-personal/receivedtemporarypassworddialog.png)

À présent, vous devez voir le nouvel utilisateur, **Ahmet le développeur**, provenant d’Azure Active Directory. Vous avez créé la nouvelle identité professionnelle ou scolaire avec Azure Active Directory. Toutefois, cette identité ne dispose pas encore des autorisations pour utiliser les ressources Azure.

![](./media/resource-group-create-work-id-from-personal/defaultdirectoryusersaftercreate.png)

Si vous utilisez **ENVOYER UN MOT DE PASSE PAR MESSAGERIE ÉLECTRONIQUE**, le type de message électronique suivant est envoyé.

![](./media/resource-group-create-work-id-from-personal/emailreceivedfromnewusercreation.png)

## Ajout de droits de co-administrateur Azure pour les abonnements

Vous devez maintenant ajouter le nouvel utilisateur en tant que co-administrateur de votre abonnement, afin que le nouvel utilisateur puisse se connecter au portail de gestion. Pour ce faire, dans le panneau inférieur gauche, cliquez sur **Paramètres**.

![](./media/resource-group-create-work-id-from-personal/thesettingswidget.png)

Dans la zone principale des paramètres, cliquez sur **ADMINISTRATEURS** en haut et seule votre identité de compte Microsoft personnelle doit s'afficher. Au bas de la page, cliquez sur **+AJOUTER** pour spécifier un co-administrateur. Ici, entrez l'adresse de messagerie du nouvel utilisateur que vous avez créé, y compris votre domaine par défaut. Comme le montre la capture d’écran suivante, une coche verte est affichée en regard de l’utilisateur pour le répertoire par défaut. N’oubliez pas de sélectionner tous les abonnements que vous souhaitez que cet utilisateur puisse gérer.

![](./media/resource-group-create-work-id-from-personal/addingnewuserascoadmin.png)

Lorsque vous avez terminé, vous devriez voir deux utilisateurs, y compris la nouvelle identité de votre co-administrateur. Déconnectez-vous du portail.

![](./media/resource-group-create-work-id-from-personal/newuseraddedascoadministrator.png)

## Connexion et modification du mot de passe du nouvel utilisateur

Connectez-vous en tant que le nouvel utilisateur que vous avez créé.

![](./media/resource-group-create-work-id-from-personal/signinginwithnewuser.png)

Il vous sera immédiatement demandé de créer un mot de passe.

![](./media/resource-group-create-work-id-from-personal/mustupdateyourpassword.png)

Votre réussite doit ressembler à ce qui suit.

![](./media/resource-group-create-work-id-from-personal/successtourdialog.png)


## Étapes suivantes

Vous pouvez maintenant vous servir de votre nouvelle identité Azure Active Directory pour utiliser des [modèles de groupe de ressources Azure](xplat-cli-azure-resource-manager.md).

     azure login
    info:    Executing command login
    warn:    Please note that currently you can login only via Microsoft organizational account or service principal. For instructions on how to set them up, please read http://aka.ms/Dhf67j.
    Username: ahmet@aztrainpassxxxxxoutlook.onmicrosoft.com
    Password: *********
    /info:    Added subscription Azure Pass
    info:    Setting subscription Azure Pass as default
    +
    info:    login command OK
    ralph@local:~$ azure config mode arm
    info:    New mode is arm
    ralph@local:~$ azure group list
    info:    Executing command group list
    + Listing resource groups
    info:    No matched resource groups were found
    info:    group list command OK
    ralph@local:~$ azure group create newgroup westus
    info:    Executing command group create
    + Getting resource group newgroup
    + Creating resource group newgroup
    info:    Created resource group newgroup
    data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/newgroup
    data:    Name:                newgroup
    data:    Location:            westus
    data:    Provisioning State:  Succeeded
    data:    Tags:
    data:
    info:    group create command OK

<!---HONumber=Sept15_HO4-->
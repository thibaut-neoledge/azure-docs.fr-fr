<properties
   pageTitle="Création d’une identité professionnelle ou scolaire dans Azure Active Directory"
   description="Décrit comment créer une identité professionnelle ou scolaire à partir de votre identité personnelle à utiliser avec les modèles de groupes de ressources ou l'accès basé sur les rôles, entre autres fonctionnalités."
   services="virtual-machines"
   documentationCenter=""
   authors="squillace"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure"
   ms.date="05/05/2015"
   ms.author="rasquill"/>

# Création d’une identité professionnelle ou scolaire dans Azure Active Directory

Si vous avez créé un compte Azure personnel ou si vous disposez d'un abonnement MSDN personnel et avez créé le compte Azure pour profiter des crédits Azure MSDN, alors vous avez utilisé une identité de **compte Microsoft** pour le créer. Pour fonctionner correctement, de nombreuses fonctionnalités d'Azure, parmi lesquelles les [modèles de groupes de ressources](../resource-group-overview.md), nécessitent un compte professionnel ou scolaire (une identité gérée par Azure Active Directory).

Heureusement, l’un des atouts de votre compte personnel Azure est qu'il est fourni avec un domaine Azure Active Directory par défaut que vous pouvez utiliser pour créer un nouveau compte professionnel ou scolaire à utiliser avec les fonctionnalités Azure qui le nécessitent.

> [AZURE.NOTE]Si vous un administrateur vous a donné un nom d'utilisateur et un mot de passe, il est probable que vous disposiez déjà d'un ID professionnel ou scolaire (parfois appelé *ID d'organisation*). Dans ce cas, vous pouvez commencer à utiliser votre compte Azure immédiatement pour accéder aux ressources Azure qui requièrent l'utilisation d'un compte. Si vous constatez que vous ne pouvez pas utiliser ces ressources, vous devrez peut-être revenir à cette rubrique. Pour plus d'informations, consultez [Comptes que vous pouvez utiliser pour vous connecter](https://msdn.microsoft.com/library/azure/dn629581.aspx#BKMK_SignInAccounts) et [Relation entre un abonnement Azure et Azure AD](https://msdn.microsoft.com/library/azure/dn629581.aspx#BKMK_SubRelationToDir).

Les étapes sont simples. Vous devez localiser votre identité de connexion sur le portail, découvrir votre domaine Azure Active Directory par défaut et y ajouter un nouvel utilisateur en tant que co-administrateur Azure. Voilà.

## Localisez votre répertoire par défaut sur le portail Azure

Commencez par vous connecter au [portail Azure](https://manage.windowsazure.com) à l'aide de votre identité de compte Microsoft personnelle. Une fois que vous êtes connecté, faites défiler vers le bas le panneau bleu sur le côté gauche et cliquez sur **ACTIVE DIRECTORY**.

![Azure Active Directory](./media/resource-group-create-work-id-from-personal/azureactivedirectorywidget.png)

Commençons par rechercher des informations sur votre identité dans Azure. Des éléments semblables à ce qui suit doivent s'afficher dans le volet principal pour indiquer que vous avez un répertoire par défaut.

![](./media/resource-group-create-work-id-from-personal/defaultaadlisting.png)

Recherchons quelques informations supplémentaires. Cliquez sur la ligne de répertoire par défaut pour afficher les propriétés de répertoire par défaut.

![](./media/resource-group-create-work-id-from-personal/defaultdirectorypage.png)

Parfait ! Maintenant, nous allons voir quel est notre domaine « par défaut »... Cliquez sur **DOMAINES**.

![](./media/resource-group-create-work-id-from-personal/domainclicktoseeyourdefaultdomain.png)

Ici, vous devez être en mesure de voir que, lors de la création du compte Azure, Azure Active Directory a créé un domaine personnel par défaut qui est un hachage de votre ID personnel utilisé en tant que sous-domaine de **onmicrosoft.com**. C'est à ce domaine que vous allez maintenant ajouter un nouvel utilisateur.

## Création d'un nouvel utilisateur dans le domaine par défaut

Cliquez sur **UTILISATEURS** et recherchez votre compte personnel unique. Vous devriez voir dans la colonne **PROVENANCE** qu'il s'agit d'un `Microsoft account`. Nous voulons créer un utilisateur dans votre domaine Azure Active Directory **.onmicrosoft.com** par défaut.

![](./media/resource-group-create-work-id-from-personal/defaultdirectoryuserslisting.png)

Nous allons suivre [ces instructions](https://technet.microsoft.com/library/hh967632.aspx#BKMK_1) dans les étapes suivantes, mais en utilisant un exemple spécifique.

Au bas de la page, cliquez sur **+AJOUTER UN UTILISATEUR**. Dans la boîte de dialogue qui s'affiche, entrez le nouveau nom d'utilisateur et définissez **Type d'utilisateur** sur **Nouvel utilisateur dans votre organisation**. Dans cet exemple, le nouveau nom d'utilisateur est `ahmet`. Veillez à sélectionner le domaine par défaut que vous avez découvert ci-dessus comme domaine pour l'adresse de messagerie de `ahmet`. Lorsque vous avez terminé, cliquez sur la flèche Suivant.

![](./media/resource-group-create-work-id-from-personal/addingauserwithdirectorydropdown.png)

Ici, entrez plus de détails pour `ahmet`, mais veillez à sélectionner la valeur **RÔLE** appropriée. Il est facile d'utiliser la valeur **Administrateur global** pour vous assurer que tout fonctionne, mais nous vous conseillons d'utiliser un rôle inférieur. Cet exemple utilise le rôle **Utilisateur**. (Apprenez-en plus sur ces rôles [ici](https://msdn.microsoft.com/library/azure/dn468213.aspx#BKMK_1).) N'activez PAS l'authentification multifacteur, sauf si vous souhaitez utiliser l'authentification multifacteur pour chaque journal de l'opération. Lorsque vous avez terminé, cliquez sur la flèche Suivant.

![](./media/resource-group-create-work-id-from-personal/userprofileuseradmin.png)

Cliquez sur le bouton **créer** pour générer et afficher un mot de passe temporaire pour `ahmet`.

![](./media/resource-group-create-work-id-from-personal/gettemporarypasswordforuser.png)

Copiez l'adresse de messagerie du nom d'utilisateur ou entrez une adresse de messagerie à laquelle envoyer les informations. Dans les deux cas, vous aurez rapidement besoin des informations pour vous connecter.

![](./media/resource-group-create-work-id-from-personal/receivedtemporarypassworddialog.png)

À présent, le nouvel utilisateur doit s'afficher. Dans le cas présent `Ahmet the Developer`, provenant d'Azure Active Directory. Vous avez créé la nouvelle identité professionnelle ou scolaire avec Azure Active Directory. Toutefois, cette identité ne dispose pas encore des autorisations pour utiliser les ressources Azure.

![](./media/resource-group-create-work-id-from-personal/defaultdirectoryusersaftercreate.png)

Si vous envoyez un courrier électronique avec les informations, il pourrait ressembler à ce qui suit :

![](./media/resource-group-create-work-id-from-personal/emailreceivedfromnewusercreation.png)

## Ajout de droits de co-administrateur Azure pour les abonnements

Vous devez maintenant ajouter le nouvel utilisateur en tant que co-administrateur de votre abonnement, afin que le nouvel utilisateur puisse se connecter au portail de gestion. Pour cela, recherchez l'icône **Paramètres** dans le volet inférieur gauche et cliquez dessus.

![](./media/resource-group-create-work-id-from-personal/thesettingswidget.png)

Dans la zone principale des paramètres, cliquez sur **ADMINISTRATEURS** en haut et seule votre identité de compte Microsoft personnelle doit s'afficher. Au bas de la page, cliquez sur **+AJOUTER** pour spécifier un co-administrateur. Ici, entrez l'adresse de messagerie du nouvel utilisateur que vous avez créé, y compris votre domaine par défaut. Comme indiqué ci-dessous, une coche verte doit s'afficher en regard de l'utilisateur pour le répertoire par défaut. N'oubliez pas de sélectionner tous les abonnements que vous souhaitez que cet utilisateur puisse gérer.

![](./media/resource-group-create-work-id-from-personal/addingnewuserascoadmin.png)

Lorsque vous avez terminé, vous devriez voir deux utilisateurs, y compris la nouvelle identité de votre co-administrateur. Déconnectez-vous du portail.

![](./media/resource-group-create-work-id-from-personal/newuseraddedascoadministrator.png)

## Connexion et modification du mot de passe du nouvel utilisateur

Connectez-vous en tant que le nouvel utilisateur que vous avez créé.

![](./media/resource-group-create-work-id-from-personal/signinginwithnewuser.png)

Il vous sera immédiatement demandé de créer un mot de passe. Toutefois, une fois que vous avez terminé...

![](./media/resource-group-create-work-id-from-personal/mustupdateyourpassword.png)

Votre réussite doit ressembler à ce qui suit.

![](./media/resource-group-create-work-id-from-personal/successtourdialog.png)


## Étapes suivantes

Maintenant, vous pouvez vous amuser. Par exemple, vous pouvez maintenant vous servir de votre nouvelle identité Azure Active Directory pour utiliser les [modèles de groupes de ressources Azure](xplat-cli-azure-resource-manager.md).

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


 

<!---HONumber=58_postMigration-->
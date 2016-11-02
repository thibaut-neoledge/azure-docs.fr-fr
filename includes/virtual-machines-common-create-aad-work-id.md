
<br>

> [AZURE.NOTE] Si un administrateur vous a donné un nom d’utilisateur et un mot de passe, il est probable que vous disposiez déjà d’un ID professionnel ou scolaire (parfois appelé *ID d’organisation*). Dans ce cas, vous pouvez commencer à utiliser votre compte Azure immédiatement pour accéder aux ressources Azure qui requièrent l'utilisation d'un compte. Si vous constatez que vous ne pouvez pas utiliser ces ressources, vous devrez peut-être revenir à cet article pour obtenir de l’aide. Pour plus d’informations, voir [Comptes que vous pouvez utiliser pour vous connecter](https://msdn.microsoft.com/library/azure/dn629581.aspx#BKMK_SignInAccounts) et [Association d’un abonnement Azure à Azure AD](https://msdn.microsoft.com/library/azure/dn629581.aspx#BKMK_SubRelationToDir).

Les étapes sont simples. Vous devez localiser votre identité de connexion sur le portail Azure Classic, découvrir votre domaine Azure Active Directory par défaut et y ajouter un nouvel utilisateur en tant que coadministrateur Azure.

## Localisez votre répertoire par défaut sur le portail Azure Classic

Commencez par vous connecter au [portail Azure Classic](https://manage.windowsazure.com) avec votre identité de compte Microsoft personnel. Une fois que vous êtes connecté, faites défiler vers le bas le panneau bleu à gauche, puis cliquez sur **ACTIVE DIRECTORY**.

![Azure Active Directory](./media/virtual-machines-common-create-aad-work-id/azureactivedirectorywidget.png)

Commençons par rechercher des informations sur votre identité dans Azure. Des éléments semblables à ce qui suit doivent s'afficher dans le volet principal pour indiquer que vous avez un répertoire par défaut.

![](./media/virtual-machines-common-create-aad-work-id/defaultaadlisting.png)

Recherchons quelques informations supplémentaires. Cliquez sur la ligne de répertoire par défaut pour afficher les propriétés de répertoire par défaut.

![](./media/virtual-machines-common-create-aad-work-id/defaultdirectorypage.png)

Pour afficher le nom de domaine par défaut, cliquez sur **DOMAINES**.

![](./media/virtual-machines-common-create-aad-work-id/domainclicktoseeyourdefaultdomain.png)

Ici, vous devez être en mesure de voir que, lors de la création du compte Azure, Azure Active Directory a créé un domaine personnel par défaut qui est une valeur de hachage (nombre généré à partir d’une chaîne de texte) de votre ID personnel utilisé en tant que sous-domaine de onmicrosoft.com. C'est à ce domaine que vous allez maintenant ajouter un nouvel utilisateur.

## Création d'un nouvel utilisateur dans le domaine par défaut

Cliquez sur **UTILISATEURS** et recherchez votre compte personnel unique. Vous devez voir dans la colonne **PROVENANCE** qu’il s’agit d’un **compte Microsoft**. Nous voulons créer un utilisateur dans votre domaine Azure Active Directory .onmicrosoft.com par défaut.

![](./media/virtual-machines-common-create-aad-work-id/defaultdirectoryuserslisting.png)

Nous allons suivre [ces instructions](https://technet.microsoft.com/library/hh967632.aspx#BKMK_1) dans les étapes suivantes, mais utiliser un exemple spécifique.

Au bas de la page, cliquez sur **+AJOUTER UN UTILISATEUR**. Dans la page qui s’affiche, entrez le nouveau nom d’utilisateur et définissez **Type d’utilisateur** sur **Nouvel utilisateur dans votre organisation**. Dans cet exemple, le nouveau nom d'utilisateur est `ahmet`. Sélectionnez le domaine par défaut que vous avez découvert précédemment comme domaine pour l’adresse de messagerie d’ahmet. Lorsque vous avez terminé, cliquez sur la flèche Suivant.

![](./media/virtual-machines-common-create-aad-work-id/addingauserwithdirectorydropdown.png)

Ajoutez d’autres détails pour Ahmet, mais veillez à sélectionner la valeur **RÔLE** appropriée. Il est facile d'utiliser la valeur **Administrateur global** pour vous assurer que tout fonctionne, mais nous vous conseillons d'utiliser un rôle inférieur. Cet exemple utilise le rôle **Utilisateur**. (En savoir plus les [autorisations des administrateurs par rôle](https://msdn.microsoft.com/library/azure/dn468213.aspx#BKMK_1).) N’activez pas l’authentification multifacteur, sauf si vous souhaitez l’utiliser pour chaque journal de l’opération. Lorsque vous avez terminé, cliquez sur la flèche Suivant.

![](./media/virtual-machines-common-create-aad-work-id/userprofileuseradmin.png)

Cliquez sur le bouton **Créer** pour générer et afficher un mot de passe temporaire pour Ahmet.

![](./media/virtual-machines-common-create-aad-work-id/gettemporarypasswordforuser.png)

Copiez l’adresse de messagerie du nom d’utilisateur, ou utilisez **ENVOYER UN MOT DE PASSE PAR MESSAGERIE ÉLECTRONIQUE**. Vous aurez rapidement besoin des informations pour vous connecter.

![](./media/virtual-machines-common-create-aad-work-id/receivedtemporarypassworddialog.png)

À présent, vous devez voir le nouvel utilisateur, **Ahmet le développeur**, provenant d’Azure Active Directory. Vous avez créé la nouvelle identité professionnelle ou scolaire avec Azure Active Directory. Toutefois, cette identité ne dispose pas encore des autorisations pour utiliser les ressources Azure.

![](./media/virtual-machines-common-create-aad-work-id/defaultdirectoryusersaftercreate.png)

Si vous utilisez **ENVOYER UN MOT DE PASSE PAR MESSAGERIE ÉLECTRONIQUE**, le type de message électronique suivant est envoyé.

![](./media/virtual-machines-common-create-aad-work-id/emailreceivedfromnewusercreation.png)

## Ajout de droits de co-administrateur Azure pour les abonnements

Vous devez maintenant ajouter le nouvel utilisateur en tant que co-administrateur de votre abonnement, afin que le nouvel utilisateur puisse se connecter au portail de gestion. Pour ce faire, dans le panneau inférieur gauche, cliquez sur **Paramètres**.

![](./media/virtual-machines-common-create-aad-work-id/thesettingswidget.png)

Dans la zone principale des paramètres, cliquez sur **ADMINISTRATEURS** en haut et seule votre identité de compte Microsoft personnelle doit s'afficher. Au bas de la page, cliquez sur **+AJOUTER** pour spécifier un co-administrateur. Ici, entrez l'adresse de messagerie du nouvel utilisateur que vous avez créé, y compris votre domaine par défaut. Comme le montre la capture d’écran suivante, une coche verte est affichée en regard de l’utilisateur pour le répertoire par défaut. N’oubliez pas de sélectionner tous les abonnements que vous souhaitez que cet utilisateur puisse gérer.

![](./media/virtual-machines-common-create-aad-work-id/addingnewuserascoadmin.png)

Lorsque vous avez terminé, vous devriez voir deux utilisateurs, y compris la nouvelle identité de votre co-administrateur. Déconnectez-vous du portail.

![](./media/virtual-machines-common-create-aad-work-id/newuseraddedascoadministrator.png)

## Connexion et modification du mot de passe du nouvel utilisateur

Connectez-vous en tant que le nouvel utilisateur que vous avez créé.

![](./media/virtual-machines-common-create-aad-work-id/signinginwithnewuser.png)

Il vous sera immédiatement demandé de créer un mot de passe.

![](./media/virtual-machines-common-create-aad-work-id/mustupdateyourpassword.png)

Votre réussite doit ressembler à ce qui suit.

![](./media/virtual-machines-common-create-aad-work-id/successtourdialog.png)


## Étapes suivantes

Vous pouvez maintenant vous servir de votre nouvelle identité Azure Active Directory pour utiliser des [modèles de groupe de ressources Azure](../articles/xplat-cli-azure-resource-manager.md).

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

<!---HONumber=AcomDC_0330_2016-->
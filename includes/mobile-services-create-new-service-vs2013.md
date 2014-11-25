La procédure suivante permet de créer un service mobile dans Azure et d’ajouter du code à votre projet pour activer l’accès au nouveau service. Avant de créer le service mobile, vous devez importer le fichier publishsettings de votre abonnement Azure dans Visual Studio. Cela permet à Visual Studio de se connecter à Azure en votre nom. Lors de la création d’un service mobile, vous devez spécifier une base de données SQL Azure qui est utilisée par celui-ci pour stocker les données d’application.

1.  Dans Visual Studio 2013, ouvrez l'Explorateur de solutions, cliquez avec le bouton droit sur le projet, cliquez sur **Ajouter**, puis sur **Service connecté...**.

    ![ajouter un service connecté][ajouter un service connecté]

2.  Dans la boîte de dialogue Gestionnaire des services, cliquez sur **Créer un service...**, puis sélectionnez **\<Importer...\>** sous **Abonnement** dans la boîte de dialogue Créer un service mobile.

    ![créer un service mobile à partir de VS 2013][créer un service mobile à partir de VS 2013]

3.  Dans Import Azure Subscriptions, cliquez sur **Télécharger le fichier d'abonnement**, connectez-vous à votre compte Azure (si nécessaire), puis cliquez sur **Enregistrer** lorsque le navigateur vous invite à enregistrer le fichier.

    ![télécharger le fichier d'abonnement dans VS][télécharger le fichier d'abonnement dans VS]

    <div class="dev-callout"><strong>Remarque</strong> <p>La fen&ecirc;tre de connexion s'affiche dans le navigateur qui peut se trouver derri&egrave;re la fen&ecirc;tre Visual Studio. N'oubliez pas de noter l'emplacement o&ugrave; vous avez enregistr&eacute; le fichier .publishsettings t&eacute;l&eacute;charg&eacute;. Vous pouvez ignorer cette &eacute;tape si votre projet est d&eacute;j&agrave; connect&eacute; &agrave; votre abonnement Azure.</p></div>

4.  Cliquez sur **Parcourir**, recherchez l'emplacement où vous avez enregistré le fichier .publishsettings, sélectionnez ce dernier, puis cliquez sur **Ouvrir** et sur **Importer**.

    ![importer l'abonnement dans VS][importer l'abonnement dans VS]

    Visual Studio importe les données nécessaires pour se connecter à votre abonnement Azure. Si votre abonnement dispose déjà d'un ou plusieurs services mobiles, leur nom s'affiche.

    <div class="dev-callout"><strong>Remarque relative &agrave; la s&eacute;curit&eacute;</strong> <p>Apr&egrave;s l'importation des param&egrave;tres de publication, pensez &agrave; supprimer le fichier .publishsettings t&eacute;l&eacute;charg&eacute;, car il contient des informations pouvant &ecirc;tre utilis&eacute;es par d'autres personnes pour acc&eacute;der &agrave; votre compte. Mettez le fichier en lieu s&ucirc;r si vous souhaitez le conserver afin de l'utiliser pour d'autres projets d'applications connect&eacute;es.</p></div>

5.  De retour dans la boîte de dialogue **Créer un service mobile**, sélectionnez votre **Abonnement** et la **Région** souhaitée pour votre service mobile, puis tapez un **Nom**.

    <div class="dev-callout"><strong>Remarque</strong> <p>Les noms des services mobiles doivent &ecirc;tre uniques. Une croix rouge s&rsquo;affiche en regard du <strong>Nom</strong> lorsqu&rsquo;il n&rsquo;est pas disponible. </p></div>

6.  Dans **Base de données**, sélectionnez **\<Create a free SQL Database\>**, indiquez le **Server user name**, le **Server password** et la **Server password confirmation**, puis cliquez sur **Créer**.

    ![créer le service à partir de VS 2013 partie 2][créer le service à partir de VS 2013 partie 2]

	> [WACOM.NOTE]
	> Dans ce didacticiel, vous créez une instance et un serveur de base de données SQL gratuits. Vous pouvez réutiliser cette nouvelle base de données et la gérer comme une autre instance de base de données SQL. Vous ne pouvez disposer que d’une seule instance de base de données gratuite. Si vous disposez déjà d’une base de données dans la même région que le nouveau service mobile, vous pouvez alors choisir la base de données existante. Lorsque vous choisissez une base de données existante, veillez à fournir les informations d’identification correctes. Sinon, le service mobile est créé dans un état défectueux.

    Une fois le service mobile créé, une référence à la bibliothèque cliente Mobile Services est ajoutée au projet et son code source est mis à jour.

  [ajouter un service connecté]: ./media/mobile-services-create-new-service-vs2013/mobile-add-connected-service.png
  [créer un service mobile à partir de VS 2013]: ./media/mobile-services-create-new-service-vs2013/mobile-create-service-from-vs2013.png
  [télécharger le fichier d'abonnement dans VS]: ./media/mobile-services-create-new-service-vs2013/mobile-import-azure-subscription.png
  [importer l'abonnement dans VS]: ./media/mobile-services-create-new-service-vs2013/mobile-import-azure-subscription-2.png
  [créer le service à partir de VS 2013 partie 2]: ./media/mobile-services-create-new-service-vs2013/mobile-create-service-from-vs2013-2.png

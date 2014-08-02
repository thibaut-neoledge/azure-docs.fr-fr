<properties linkid="develop-mobile-how-to-guides-import-publish-settings" urlDisplayName="Import your subscription publish settings file in Visual Studio 2013" pageTitle="Import your publish settings file in Visual Studio 2013 | Mobile Services" metaKeywords="Azure import publishsettings, mobile services" description="Learn how to import a subscription publish settings file for your Azure Mobile Services application in Visual Studio 2013." title="Import your subscription publish settings file in Visual Studio 2013" documentationCenter="Mobile" services="" solutions="" manager="" editor="" videoId="" scriptId="" authors="" />

Importation du fichier de paramètres de publication de votre abonnement dans Visual Studio 2013
===============================================================================================

Avant de créer le service mobile, vous devez importer le fichier de paramètres de publication de votre abonnement Azure dans Visual Studio. Cela permet à Visual Studio de se connecter à Azure en votre nom.

1.  Dans Visual Studio 2013, ouvrez l'Explorateur de solutions, cliquez avec le bouton droit sur le projet, cliquez sur **Ajouter**, puis sur **Service connecté...**.

    ![ajouter un service connecté](./media/mobile-services-create-new-service-vs2013/mobile-add-connected-service.png)

2.  Dans la boîte de dialogue Gestionnaire des services, cliquez sur **Créer un service...**, puis sélectionnez **&lt;Importer...\>** sous **Abonnement** dans la boîte de dialogue Créer un service mobile.

    ![créer un service mobile à partir de VS 2013](./media/mobile-services-create-new-service-vs2013/mobile-create-service-from-vs2013.png)

3.  Dans Import Azure Subscriptions, cliquez sur **Télécharger le fichier d'abonnement**, connectez-vous à votre compte Azure (si nécessaire), puis cliquez sur **Enregistrer** lorsque le navigateur vous invite à enregistrer le fichier.

    ![télécharger le fichier d'abonnement dans VS](./media/mobile-services-create-new-service-vs2013/mobile-import-azure-subscription.png)

    **Remarque**

    La fenêtre de connexion s'affiche dans le navigateur qui peut se trouver derrière la fenêtre Visual Studio. N'oubliez pas de noter l'emplacement où vous avez enregistré le fichier .publishsettings téléchargé. Vous pouvez ignorer cette étape si votre projet est déjà connecté à votre abonnement Azure.

4.  Cliquez sur **Parcourir**, recherchez l'emplacement où vous avez enregistré le fichier .publishsettings, sélectionnez ce dernier, puis cliquez sur **Ouvrir** et sur **Importer**.

    ![importer l'abonnement dans VS](./media/mobile-services-create-new-service-vs2013/mobile-import-azure-subscription-2.png)

    Visual Studio importe les données nécessaires pour se connecter à votre abonnement Azure. Si votre abonnement dispose déjà d'un ou plusieurs services mobiles, leur nom s'affiche.

    **Remarque relative à la sécurité**

    Après l'importation des paramètres de publication, pensez à supprimer le fichier .publishsettings téléchargé, car il contient des informations pouvant être utilisées par d'autres personnes pour accéder à votre compte. Mettez le fichier en lieu sûr si vous souhaitez le conserver afin de l'utiliser pour d'autres projets d'applications connectées.



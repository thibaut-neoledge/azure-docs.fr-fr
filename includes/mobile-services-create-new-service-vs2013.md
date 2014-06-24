La procédure suivante permet de créer un service mobile dans Azure et
d'ajouter du code à votre projet pour activer l'accès au nouveau
service. Avant de créer le service mobile, vous devez importer le
fichier publishsettings de votre abonnement Azure dans Visual Studio.
Cela permet à Visual Studio de se connecter à Azure en votre nom. Lors
de la création d'un service mobile, vous devez spécifier une base de
données SQL Azure qui est utilisée par celui-ci pour stocker les données
d'application.

1.  Dans Visual Studio 2013, ouvrez l'Explorateur de solutions, cliquez
    avec le bouton droit sur le projet, cliquez sur **Ajouter**, puis
    sur **Service connecté...**.
    
    ![ajouter un service
    connecté](./media/mobile-services-create-new-service-vs2013/mobile-add-connected-service.png)

2.  Dans la boîte de dialogue Gestionnaire des services, cliquez sur
    **Créer un service...**, puis sélectionnez **<Importer...>**
    sous **Abonnement** dans la boîte de dialogue Créer un service
    mobile.
    
    ![créer un service mobile à partir de
    VS 2013](./media/mobile-services-create-new-service-vs2013/mobile-create-service-from-vs2013.png)

3.  Dans Import Azure Subscriptions, cliquez sur **Télécharger le
    fichier d'abonnement**, connectez-vous à votre compte Azure (si
    nécessaire), puis cliquez sur **Enregistrer** lorsque le navigateur
    vous invite à enregistrer le fichier.
    
    ![télécharger le fichier d’abonnement dans
    VS](./media/mobile-services-create-new-service-vs2013/mobile-import-azure-subscription.png)


**Remarque**

La fenêtre de connexion s'affiche dans le navigateur qui peut se
trouver derrière la fenêtre Visual Studio. N'oubliez pas de noter
l'emplacement où vous avez enregistré le fichier .publishsettings
téléchargé. Vous pouvez ignorer cette étape si votre projet est déjà
connecté à votre abonnement Azure.



4.  Cliquez sur **Parcourir**, recherchez l'emplacement où vous avez
    enregistré le fichier .publishsettings, sélectionnez ce dernier,
    puis cliquez sur **Ouvrir** et sur **Importer**.
    
    ![importer l’abonnement dans
    VS](./media/mobile-services-create-new-service-vs2013/mobile-import-azure-subscription-2.png)
    
    Visual Studio importe les données nécessaires pour se connecter à
    votre abonnement Azure. Si votre abonnement dispose déjà d'un ou
    plusieurs services mobiles, leurs noms s'affichent.


**Remarque relative à la sécurité**

Après l'importation des paramètres de publication, pensez à
supprimer le fichier .publishsettings téléchargé, car il contient
des informations pouvant être utilisées par d'autres personnes pour
accéder à votre compte. Mettez le fichier en lieu sûr si vous
souhaitez le conserver afin de l'utiliser pour d'autres projets
d'applications connectées.
</div>


5.  De retour dans la boîte de dialogue **Créer un service mobile**,
    sélectionnez votre **Abonnement** et la **Région** souhaitée pour
    votre service mobile, puis tapez un **Nom**.


**Remarque**

Les noms des services mobiles doivent être uniques. Une croix rouge
s'affiche en regard du **Nom** lorsqu'il
n'est pas disponible.



6.  Dans **Base de données**, sélectionnez **<Create a free SQL
    Database>**, indiquez le **Server user name**, le **Server
    password** et la **Server password confirmation**, puis cliquez sur
    **Créer**.

 ![créer le service à partir de VS 2013 partie 2](./media/mobile-services-create-new-service-vs2013/mobile-create-service-from-vs2013-2.png)     

>WACOM.NOTE]
    > Dans ce didacticiel, vous créez une instance et un serveur de base de données SQL gratuits. Vous pouvez réutiliser cette nouvelle base de données et la gérer comme une autre instance de base de données SQL. Vous ne pouvez disposer que d’une seule instance de base de données gratuite. Si vous disposez déjà d’une base de données dans la même région que le nouveau service mobile, vous pouvez alors choisir la base de données existante. Lorsque vous choisissez une base de données existante, veillez à fournir les informations d’identification correctes. Sinon, le service mobile est créé dans un état défectueux.

 Une fois le service mobile créé, une référence à la bibliothèque cliente Mobile Services est ajoutée au projet et son code source est mis à jour. 
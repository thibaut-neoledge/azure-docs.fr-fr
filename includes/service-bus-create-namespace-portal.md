Pour commencer à utiliser des files d’attente Service Bus dans Azure, vous devez d’abord créer un espace de noms avec un nom unique dans Azure. Ce dernier fournit un conteneur d'étendue pour l'adressage des ressources Service Bus au sein de votre application.

Pour créer un espace de noms :

1. Connectez-vous au [portail Azure][Azure portal].
2. Dans le volet de navigation gauche du portail, cliquez sur **Nouveau**, puis sur **Enterprise Integration** et sur **Service Bus**.
3. Dans la boîte de dialogue **Créer un espace de noms** , entrez un nom d’espace de noms. Le système vérifie immédiatement si le nom est disponible.
4. Lorsque vous avez vérifié la disponibilité de l’espace de noms, sélectionnez le niveau tarifaire (Basique, Standard ou Premium).
5. Dans le champ **Abonnement** , sélectionnez un abonnement Azure dans lequel créer l’espace de noms.
6. Dans le champ **Groupe de ressources** , choisissez un groupe de ressources existant dans lequel l’espace de noms sera utilisé, ou créez-en un nouveau.      
7. Dans **Emplacement**, sélectionnez le pays ou la région où votre espace de noms doit être hébergé.
   
    ![Créer un espace de noms][create-namespace]
8. Cliquez sur **Create**. Le système crée l'espace de noms de service et l'active. Vous devrez peut-être attendre plusieurs minutes afin que le système approvisionne des ressources pour votre compte.

### <a name="obtain-the-management-credentials"></a>Obtenir les informations d’identification de gestion

1. Dans la liste des espaces de noms, cliquez sur le nom de l’espace de noms que vous venez de créer.
2. Dans le panneau Espace de noms, cliquez sur **Stratégies d’accès partagé**.
3. Dans le panneau **Stratégies d’accès partagé**, cliquez sur **RootManageSharedAccessKey**.
   
    ![informations de connexion][connection-info]
4. Dans le panneau **Policy: RootManageSharedAccessKey (Stratégie : RootManageSharedAccessKey)**, cliquez sur le bouton de copie situé en regard de **Clé primaire de la chaîne de connexion**, pour copier la chaîne de connexion dans le presse-papiers pour une utilisation ultérieure. Copiez cette valeur dans le Bloc-notes ou un autre emplacement temporaire.
   
    ![connection-string][connection-string]

5. Répétez l’étape précédente, en copiant et collant la valeur de **Clé primaire** dans un emplacement temporaire pour l’utiliser ultérieurement.

<!--Image references-->

[create-namespace]: ./media/service-bus-create-namespace-portal/create-namespace.png
[connection-info]: ./media/service-bus-create-namespace-portal/connection-info.png
[connection-string]: ./media/service-bus-create-namespace-portal/connection-string.png
[Azure portal]: https://portal.azure.com

## Configuration requise
Avant de pouvoir écrire le code de gestion CDN, nous devons effectuer certaines tâches de préparation pour permettre à ce code d’interagir avec Azure Resource Manager. Pour ce faire, procédez comme suit :

* Créez le groupe de ressources qui va héberger le profil CDN que nous allons créer dans ce didacticiel.
* Configurez Azure Active Directory pour authentifier notre application.
* Octroyez des autorisations au groupe de ressources afin que seuls les utilisateurs autorisés par notre client Azure AD puissent interagir avec notre profil CDN.

### Création du groupe de ressources
1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Cliquez sur le bouton **Nouveau** dans l’angle supérieur gauche, puis sur **Gestion** et enfin sur **Groupe de ressources**.
   
    ![Création d’un groupe de ressources](./media/cdn-app-dev-prep/cdn-new-rg-1-include.png)
3. Attribuez à votre groupe de ressources le nom *CdnConsoleTutorial*. Sélectionnez votre abonnement et choisissez un emplacement près de chez vous. Si vous le souhaitez, vous pouvez cocher la case **Épingler au tableau de bord** pour épingler le groupe de ressources au tableau de bord dans le portail. Il sera ainsi plus facile à repérer. Une fois vos sélections effectuées, cliquez sur **Créer**.
   
    ![Attribution d’un nom au groupe de ressources](./media/cdn-app-dev-prep/cdn-new-rg-2-include.png)
4. Si vous n’avez pas épinglé le groupe de ressources à votre tableau de bord, cliquez sur **Parcourir**, puis sur **Groupes de ressources** pour le rechercher. Cliquez sur le groupe de ressources pour l’ouvrir. Notez votre **ID d’abonnement**. Vous en aurez besoin ultérieurement.
   
    ![Attribution d’un nom au groupe de ressources](./media/cdn-app-dev-prep/cdn-subscription-id-include.png)

### Création d’une application Azure AD et application des autorisations
Il existe deux approches pour authentifier une application avec Azure Active Directory : les utilisateurs individuels ou un principal de service. Un principal de service est similaire à un compte de service dans Windows. Au lieu d’octroyer à un utilisateur des autorisations d’interagir avec les profils CDN, nous accordons les autorisations au principal du service. Les principaux de service s’utilisent généralement pour des processus automatisés et non interactifs. Bien que le but de ce didacticiel soit de créer une application console interactive, nous allons nous adopter l’approche du principal de service.

La création d’un principal de service comprend plusieurs étapes, dont la création d’une application Azure Active Directory. Pour ce faire, nous allons [suivre ce didacticiel](../articles/resource-group-create-service-principal-portal.md).

> [!IMPORTANT]
> Veillez à respecter toutes les étapes du [didacticiel associé](../articles/resource-group-create-service-principal-portal.md). Il est *primordial* de suivre la procédure à la lettre. N’oubliez pas de noter votre **ID de locataire**, **nom de domaine de client** (généralement un domaine *.onmicrosoft.com*, sauf si vous avez spécifié un domaine personnalisé), **ID de client** et **clé d’authentification**, car nous en aurons besoin ultérieurement. Veillez à protéger votre **ID de client** et votre **clé d’authentification**, car ces informations d’identification peuvent être utilisées par tout le monde pour exécuter des opérations en tant que principal du service.
> 
> Lorsque vous arrivez à l’étape intitulée [Configurer une application multi-locataires](../articles/resource-group-create-service-principal-portal.md#configure-multi-tenant-application), sélectionnez **Non**.
> 
> Lorsque vous arrivez à l’étape [d’affectation de l’application à un rôle](../articles/resource-group-create-service-principal-portal.md#assign-application-to-role), utilisez le groupe de ressources créé, *CdnConsoleTutorial*, mais, au lieu du rôle **Lecteur**, affectez-lui le rôle **CDN Profile Contributor (Contributeur du profil CDN)**. Après avoir affecté à l’application le rôle **Contributeur du profil CDN** dans votre groupe de ressources, revenez dans ce didacticiel.
> 
> 

Une fois que vous avez créé le principal du service et affecté le rôle **Contributeur du profil CDN**, le panneau **Utilisateurs** de votre groupe de ressources doit se présenter comme suit.

![Panneau Utilisateurs](./media/cdn-app-dev-prep/cdn-service-principal-include.png)

### Authentification interactive des utilisateurs
Si, au lieu d’un principal de service, vous préférez que l’authentification des utilisateurs soit interactive, le processus est très similaire à celui d’un principal de service. En fait, la procédure est identique, à quelques modifications près.

> [!IMPORTANT]
> Ne suivez ces étapes que si vous optez pour l’authentification des utilisateurs au lieu d’un principal de service.
> 
> 

1. Lors de la création de votre application, au lieu **d’Application web**, choisissez **Application native**.
   
    ![Application native](./media/cdn-app-dev-prep/cdn-native-application-include.png)
2. Sur la page suivante, vous êtes invité à spécifier un **URI de redirection**. Cette URI ne sera pas validée mais notez-la. Vous en aurez besoin ultérieurement.
3. Il est inutile de créer une **clé d’authentification de client**.
4. Au lieu d’affecter le rôle **Contributeur du profil CDN** à un principal de service, nous allons l’attribuer à des utilisateurs ou des groupes. Dans cet exemple, vous pouvez voir que j’ai attribué le rôle **Contributeur du profil CDN** à l’utilisateur *CDN Demo User*.
   
    ![Accès d’utilisateurs individuels](./media/cdn-app-dev-prep/cdn-aad-user-include.png)

<!---HONumber=AcomDC_0706_2016-->
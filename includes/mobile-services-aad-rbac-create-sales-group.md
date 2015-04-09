Dans cette section, vous allez ajouter à votre annuaire deux nouveaux utilisateurs ainsi que le nouveau groupe Ventes. Un des deux utilisateurs sera membre du groupe Ventes. L'autre utilisateur ne sera pas membre du groupe. 

### Création des utilisateurs


1. Dans le [portail de gestion Azure], accédez à l'annuaire que vous avez configuré précédemment pour l'authentification lorsque vous avez suivi le didacticiel pour ajouter l'authentification à votre application.
2. Cliquez sur **utilisateurs** en haut de la page. Cliquez ensuite sur le bouton **Ajouter un utilisateur** en bas de la page. 
3. Dans les boîtes de dialogue de création de nouveaux utilisateurs, créez un utilisateur nommé **Bob**. Notez le mot de passe temporaire de cet utilisateur. 
4. Créez un autre utilisateur nommé **Dave**. Notez le mot de passe temporaire de cet utilisateur.
5. Les nouveaux utilisateurs doivent apparaître comme suit :

    ![](./media/mobile-services-aad-rbac-create-sales-group/users.png)    


### Création du groupe Ventes


1. En haut de la page de l'annuaire, cliquez sur **Groupes**. Cliquez ensuite sur le bouton **Ajouter un groupe** en bas de la page. 
2. Attribuez le nom **Ventes** au groupe et cliquez sur le bouton Terminer de la boîte de dialogue pour créer le groupe. 

    ![](./media/mobile-services-aad-rbac-create-sales-group/sales-group.png)

### Ajoutez l'utilisateur au groupe Ventes.


1. Cliquez sur **Groupes** en haut de la page de l'annuaire. Cliquez ensuite sur le groupe **Ventes** pour accéder à la page correspondante. 
2. Sur la page du groupe Ventes, cliquez sur **Ajouter des membres**. Ajoutez l'utilisateur nommé **Bob** au groupe Ventes. L'utilisateur nommé **Dave** ne doit pas être un membre du groupe.

    ![](./media/mobile-services-aad-rbac-create-sales-group/group-membership.png)

3. Dans la page du groupe Ventes, cliquez sur **Propriétés**, puis copiez l'**ID objet** du groupe Ventes au bas de la page. 

   
    ![](./media/mobile-services-aad-rbac-create-sales-group/sales-group-id.png)

4. Revenez à la page de configuration du service mobile et ajoutez l'ID objet en tant que paramètre d'application nommé **AAD\_SALES\_GROUP\_ID**. Ce didacticiel utilise l'ID objet de groupe en tant que paramètre d'application au lieu de rechercher l'ID en fonction du nom du groupe. Le nom de groupe peut en effet changer, contrairement à l'ID.

    ![](./media/mobile-services-aad-rbac-create-sales-group/sales-group-id-app-setting.png)

<!--HONumber=49-->
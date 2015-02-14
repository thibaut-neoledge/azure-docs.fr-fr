Dans cette section, vous allez ajouter à votre annuaire deux nouveaux utilisateurs ainsi que le nouveau groupe Ventes. Un des deux utilisateurs sera membre du groupe Ventes. L'autre utilisateur ne sera pas membre du groupe. 

### Création des utilisateurs


1. Dans le [portail de gestion Azure] accédez au répertoire que vous avez précédemment configuré pour l'authentification lorsque vous avez suivi le didacticiel [prise en main d'authentification].
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

3. Sur la page du groupe Ventes, cliquez sur **Configurer**, puis notez l'**ID objet** du groupe Ventes. Ce didacticiel recherche l'ID objet du groupe à l'aide des API Graph. Vous n'avez donc pas besoin de l'ID. Cela dit, dans certains cas, il est préférable de ne pas utiliser le nom du groupe car celui-ci peut changer alors que l'ID reste toujours le même. Si vous souhaitez stocker l'ID du groupe en tant que paramètre d'application sur le service mobile ou le coder en dur dans votre code, vous le trouverez ici.

    ![](./media/mobile-services-aad-rbac-create-sales-group/sales-group-id.png)


<!--HONumber=42-->

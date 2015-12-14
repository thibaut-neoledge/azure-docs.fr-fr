1. Cliquez sur l’onglet **Applications** sur la page de votre annuaire du [portail Azure Classic](https://manage.windowsazure.com/).
  
2. Cliquez sur l'inscription d'application intégrée.

3. Cliquez sur **Configurer** dans la page d'application et accédez à la section **clés** de la page.
4. Cliquez sur la durée **1 an** pour une nouvelle clé. Cliquez ensuite sur **Enregistrer**. Le portail affiche alors la nouvelle valeur de clé.
5. Copiez les valeurs **ID client** et **Clé** affichées après l'enregistrement. Notez que la valeur de clé n'est affichée qu'une seule fois après l'enregistrement. 

    ![](./media/mobile-services-generate-aad-app-registration-access-key/client-id-and-key.png)

6. Accédez au bas de la page de configuration d'application intégrée, activez l'autorisation **Lire les données d'annuaire** pour l'application et cliquez sur **Enregistrer**.

    ![](./media/mobile-services-generate-aad-app-registration-access-key/app-perms.png)


7. Dans le [portail Azure Classic](https://manage.windowsazure.com/), revenez à votre service mobile, puis cliquez sur l’onglet **Configurer**. Accédez à la section **paramètres d'application**, ajoutez les paramètres d'application suivants et cliquez sur **Enregistrer**.

    <table border="1">
    <tr>
    <th>App Setting Name</th><th>Description</th>
    </tr>
    <tr>
    <td>AAD_CLIENT_ID</td><td>ID client que vous avez copié à partir de votre application intégrée pendant les étapes ci-dessus.</td>
    </tr>
    <tr> <td>AAD_CLIENT_KEY</td><td>Clé d'application que vous avez générée dans votre application intégrée AAD pendant les étapes ci-dessus.</td>
    </tr>
    <tr> <td>AAD_TENANT_DOMAIN</td><td>Nom de domaine AAD. Doit être semblable à mydomain.onmicrosoft.com</td>
    </tr>
    </table><br/>

 
    ![](./media/mobile-services-generate-aad-app-registration-access-key/aad-app-settings.png)
  

<!---HONumber=AcomDC_1203_2015-->
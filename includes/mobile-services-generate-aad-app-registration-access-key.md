1. Cliquez sur l'onglet **Applications** sur la page active dans le [portail de gestion Azure](https://manage.windowsazure.com/).
  
2. Cliquez sur l'inscription d'application intégrée.

3. Cliquez sur **configurer** sur la page d'application et faites défiler la section **clés** de la page. 
4. Cliquez sur **1 an** durée pour une nouvelle clé. Cliquez sur **enregistrer** et le portail affiche votre nouvelle valeur de clé.
5. Copie le **ID Client** et **clé** indiqué après l'avoir enregistrée. Notez que la valeur de clé n'est affichée qu'une seule fois après l'enregistrement. 

    ![](./media/mobile-services-generate-aad-app-registration-access-key/client-id-and-key.png)

6. Faites défiler jusqu'au bas de la page de configuration d'application intégrée et activer la **lire les données d'annuaire** l'autorisation de l'application et cliquez sur **enregistrer**.

    ![](./media/mobile-services-generate-aad-app-registration-access-key/app-perms.png)


7. Dans le [portail de gestion Azure](https://manage.windowsazure.com/) revenir à votre service mobile et cliquez sur le **configurer** onglet. Faites défiler jusqu'à la **paramètres de l'application** et ajoutez les paramètres d'application suivants et cliquez sur **enregistrer**. 

    <table border="1">
    <tr>
    <th>Nom de paramètre d'application</th><th>Description</th>
    </tr>
    <tr>
    <td>AAD_CLIENT_ID</td><td>ID client que vous avez copié à partir de votre application intégrée lors des étapes ci-dessus.</td>
    </tr>
    <tr>
    <td>AAD_CLIENT_KEY</td><td>Clé d'application que vous avez générée dans votre application intégrée AAD lors des étapes ci-dessus.</td>
    </tr>
    <tr>
    <td>AAD_TENANT_DOMAIN</td><td>Nom de domaine AAD. Doit être semblable à mydomain.onmicrosoft.com</td>
    </tr>
    </table><br/>

 
    ![](./media/mobile-services-generate-aad-app-registration-access-key/aad-app-settings.png)
  <!--HONumber=41-->

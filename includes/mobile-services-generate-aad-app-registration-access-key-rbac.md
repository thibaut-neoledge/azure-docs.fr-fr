1.  Cliquez sur l'onglet **Applications** dans la page d'annuaire du [portail de gestion Azure][portail de gestion Azure].

2.  Cliquez sur l'inscription d'application intégrée.

3.  Cliquez sur **Configurer** dans la page d'application et accédez à la section **clés** de la page.
4.  Cliquez sur la durée **1 an** pour une nouvelle clé. Cliquez ensuite sur **Enregistrer**. Le portail affiche alors la nouvelle valeur de clé.
5.  Copiez les valeurs **ID client** et **Clé** affichées après l'enregistrement. Notez que la valeur de clé n'est affichée qu'une seule fois après l'enregistrement.

    ![][0]

6.  Accédez au bas de la page de configuration d'application intégrée, activez l'autorisation **Lire les données d'annuaire** pour l'application et cliquez sur **Enregistrer**.

    ![][1]

7.  Dans le [portail de gestion Azure][portail de gestion Azure], revenez à votre service mobile, puis cliquez sur l'onglet **Configurer**. Accédez à la section **paramètres d'application**, ajoutez les paramètres d'application suivants et cliquez sur **Enregistrer**.

    | Nom de paramètre d'application | Description                                                                                            |
    |--------------------------------|--------------------------------------------------------------------------------------------------------|
    | AAD\_CLIENT\_ID                | ID client que vous avez copié à partir de votre application intégrée lors des étapes ci-dessus.        |
    | AAD\_CLIENT\_KEY               | Clé d'application que vous avez générée dans votre application intégrée AAD lors des étapes ci-dessus. |
    | AAD\_TENANT\_DOMAIN            | Nom de domaine AAD. Doit être semblable à mydomain.onmicrosoft.com                                     |
    | AAD\_GROUP\_ID                 | ID de groupe que vous avez noté pour le groupe Ventes dans la section précédente                       |

    ![][2]

  [portail de gestion Azure]: https://manage.windowsazure.com/
  [0]: ./media/mobile-services-generate-aad-app-registration-access-key-rbac/client-id-and-key.png
  [1]: ./media/mobile-services-generate-aad-app-registration-access-key-rbac/app-perms.png
  [2]: ./media/mobile-services-generate-aad-app-registration-access-key-rbac/aad-app-settings.png

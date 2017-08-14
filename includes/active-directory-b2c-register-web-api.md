[!INCLUDE [active-directory-b2c-portal-add-application](active-directory-b2c-portal-add-application.md)]

Pour inscrire votre API web, utilisez les paramètres spécifiés dans la table.

![Exemples de paramètres d’inscription pour les nouvelles API web](./media/active-directory-b2c-register-web-api/b2c-new-web-api-settings.png)

| Paramètre      | Exemple de valeur  | Description                                        |
| ------------ | ------- | -------------------------------------------------- |
| **Nom** | API de Contoso B2C | Entrez un **Nom** pour l’application qui décrira votre API aux consommateurs. | 
| **Application/API web** | Oui | Sélectionnez **Oui** pour une API web. |
| **Autoriser le flux implicite** | Oui | Choisissez **Oui** si votre application doit utiliser une [connexion OpenID Connect](../articles/active-directory-b2c/active-directory-b2c-reference-oidc.md). |
| **URL de réponse** | `https://localhost:44316/` | Les URL de réponse sont des points de terminaison auxquels Azure AD B2C renvoie les jetons demandés par votre application. Entrez une URL [appropriée](../articles/active-directory-b2c/active-directory-b2c-app-registration.md#choosing-a-web-app-or-api-reply-url) comme **URL de réponse**. Dans cet exemple, votre API web est locale et à l’écoute sur le port 44316. |
| **URI ID d’application** | api | L’URI ID d’application est l’identificateur utilisé pour votre API web. L’identificateur complet URI, y compris le domaine, est généré pour vous. |

Cliquez sur **Créer** pour inscrire votre application.

Votre application qui vient d’être enregistrée s’affiche dans la liste des applications du client B2C. Sélectionnez votre API web dans la liste. Le volet de propriétés de l’API s’affiche.

![Propriétés de l’API Web](./media/active-directory-b2c-register-web-api/b2c-web-api-properties.png)

Prenez note de l’**ID client de l’application** global unique. Vous utilisez l’ID dans le code de votre application.
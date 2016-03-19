4. Accédez au panneau de la passerelle de votre application API.

	![Cliquez sur la passerelle](./media/app-service-api-gateway-config-auth/gateway.png)

7. Dans le panneau **Passerelle**, cliquez sur **Paramètres**, puis sur **Identité**.

	![Cliquez sur Paramètres](./media/app-service-api-gateway-config-auth/clicksettingsingateway.png)

	![Cliquez sur Identité](./media/app-service-api-gateway-config-auth/clickidentity.png)

	Dans le panneau **Identité**, vous pouvez accéder à différents panneaux pour configurer l’authentification avec Azure Active Directory et plusieurs autres fournisseurs.

	![Panneau Identité](./media/app-service-api-gateway-config-auth/identityblade.png)
  
3. Choisissez le fournisseur d’identité à utiliser et suivez les étapes de l’article correspondant pour configurer votre application API avec ce fournisseur. Ces articles ont été écrits pour des applications mobiles, mais les procédures sont les mêmes pour les applications API. Certaines des procédures vous demandent d’utiliser le [portail Azure].

 - [Compte Microsoft](../articles/app-service-mobile/app-service-mobile-how-to-configure-microsoft-authentication.md)
 - [Connexion Facebook](../articles/app-service-mobile/app-service-mobile-how-to-configure-facebook-authentication.md)
 - [Connexion Twitter](../articles/app-service-mobile/app-service-mobile-how-to-configure-twitter-authentication.md)
 - [Connexion Google](../articles/app-service-mobile/app-service-mobile-how-to-configure-google-authentication.md)
 - [Azure Active Directory](../articles/app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md)

À titre d’exemple, les captures d’écran suivantes montrent ce que vous devriez voir dans les pages du [portail Azure] et les panneaux du [portail Azure en version préliminaire] une fois que vous avez configuré l’authentification Azure Active Directory.

Dans le portail Azure en version préliminaire, le panneau **Azure Active Directory** a un **ID client** qui provient de l’application que vous avez créée sous l’onglet Azure Active Directory du portail Azure et **Clients autorisés** contient votre client Azure Active Directory (par exemple, « contoso.onmicrosoft.com »).

![Panneau Azure Active Directory](./media/app-service-api-gateway-config-auth/tdinaadblade.png)

Dans le portail Azure, l’onglet **Configurer** de l’application créée sous l’onglet **Azure Active Directory** contient l’**URL de connexion**, l’**URI ID d’application** et l’**URL de réponse** qui proviennent du panneau **Azure Active Directory** du portail Azure en version préliminaire.

![](./media/app-service-api-gateway-config-auth/oldportal1.png)

![](./media/app-service-api-gateway-config-auth/oldportal2.png)

![](./media/app-service-api-gateway-config-auth/oldportal3.png)

![](./media/app-service-api-gateway-config-auth/oldportal4.png)

(L’URL de réponse dans l’image montre deux fois la même URL, une fois avec `http:` et une fois avec `https:`.)

<!---HONumber=Nov15_HO1-->
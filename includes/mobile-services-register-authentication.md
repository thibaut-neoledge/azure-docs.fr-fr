Pour pouvoir authentifier les utilisateurs, vous devez inscrire votre application avec un fournisseur d'identité. Vous devez ensuite inscrire la clé secrète cliente générée par le fournisseur avec Mobile Services.

1.  Connectez-vous au [portail de gestion Azure][portail de gestion Azure], cliquez sur **Mobile Services**, puis sur le service mobile.

    ![][0]

2.  Cliquez sur l'onglet **Tableau de bord**, puis notez la valeur **Mobile Service URL**.

    ![][1]

    Il se peut que le fournisseur d'identité vous demande cette valeur lorsque vous inscrivez votre application.

3.  Choisissez un fournisseur d'identité pris en charge dans la liste ci-dessous et suivez la procédure pour inscrire votre application auprès de ce fournisseur :

 -   [Compte Microsoft][Compte Microsoft]
 -   [Connexion Facebook][Connexion Facebook]
 -   [Connexion Twitter][Connexion Twitter]
 -   [Connexion Google][Connexion Google]
 -   [Azure Active Directory][Azure Active Directory]

    N'oubliez pas de noter les valeurs de l'identité du client et de la clé secrète cliente générées par le fournisseur.

    <div class="dev-callout"><b>Remarque relative &agrave; la s&eacute;curit&eacute;</b>
<p>La cl&eacute; secr&egrave;te g&eacute;n&eacute;r&eacute;e par le fournisseur est une information d'identification de s&eacute;curit&eacute; importante. Ne partagez pas cette cl&eacute; secr&egrave;te avec quiconque et ne la distribuez pas avec votre application.</p>
</div>

1.  De retour dans le portail de gestion, cliquez sur l'onglet **Identité**, entrez les valeurs de l'identificateur d'application et de la clé secrète partagée fournies par votre fournisseur d'identité, puis cliquez sur **Enregistrer**.

    ![][2]

    Votre service mobile et votre application sont désormais configurés pour utiliser le fournisseur d'authentification choisi.



  [portail de gestion Azure]: https://manage.windowsazure.com/
  [0]: ./media/mobile-services-register-authentication/mobile-services-selection.png
  [1]: ./media/mobile-services-register-authentication/mobile-service-uri.png
  [Compte Microsoft]: /fr-fr/documentation/articles/mobile-services-how-to-register-microsoft-authentication/
  [Connexion Facebook]: /fr-fr/documentation/articles/mobile-services-how-to-register-facebook-authentication/
  [Connexion Twitter]: /fr-fr/documentation/articles/mobile-services-how-to-register-twitter-authentication/
  [Connexion Google]: /fr-fr/documentation/articles/mobile-services-how-to-register-google-authentication/
  [Azure Active Directory]: /fr-fr/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
  [2]: ./media/mobile-services-register-authentication/mobile-identity-tab.png

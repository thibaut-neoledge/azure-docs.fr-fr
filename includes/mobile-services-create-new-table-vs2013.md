Avant de pouvoir stocker des données dans le nouveau service mobile, vous devez créer une table de stockage dans le service. Cette procédure utilise Visual Studio 2013 pour créer une table dans le service mobile. Ensuite, vous mettez à jour l’application pour utiliser Mobile Services afin de stocker les éléments dans Azure et non dans la collection locale.

1.  Dans l’Explorateur de serveurs, développez **Azure Mobile Services**, cliquez avec le bouton droit sur le service mobile, cliquez sur **Créer une table**, puis tapez `TodoItem` dans **Nom de la table**.

    ![créer une table dans VS 2013][créer une table dans VS 2013]

2.  Développez **Avancé**, vérifiez que les autorisations par défaut sur la table sont **Anybody with the Application Key**, puis cliquez sur **Créer**.

    ![créer une table dans VS 2013 partie 2][créer une table dans VS 2013 partie 2]

    La table TodoItem est créée sur le serveur et quiconque possédant la clé de l’application peut accéder aux données de la table et les modifier sans devoir être authentifié au préalable.

    <div class="dev-callout"><strong>Remarque</strong><p>La cl&eacute; de l&rsquo;application est distribu&eacute;e avec l&rsquo;application. &Eacute;tant donn&eacute; que cette cl&eacute; n&rsquo;est pas distribu&eacute;e de fa&ccedil;on s&eacute;curis&eacute;e, elle ne peut pas &ecirc;tre consid&eacute;r&eacute;e comme un jeton de s&eacute;curit&eacute;. Pour s&eacute;curiser l&rsquo;acc&egrave;s aux donn&eacute;es du service mobile, les utilisateurs doivent s&rsquo;authentifier dans un premier temps. Pour plus d&rsquo;informations, consultez la page <a href="http://msdn.microsoft.com/fr-fr/library/windowsazure/jj193161.aspx">Autorisations</a>.</p></div>

  [créer une table dans VS 2013]: ./media/mobile-services-create-new-table-vs2013/mobile-create-table-vs2013.png
  [créer une table dans VS 2013 partie 2]: ./media/mobile-services-create-new-table-vs2013/mobile-create-table-vs2013-2.png

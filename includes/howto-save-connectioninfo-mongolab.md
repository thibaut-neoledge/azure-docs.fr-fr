Même s’il est possible de coller un URI MongoLab dans votre code, il est recommandé de le configurer dans l’environnement pour faciliter la gestion. De cette façon, si l’URI change, vous pouvez le modifier via le portail de gestion Azure sans devoir modifier le code.

1.  Dans le portail Azure, sélectionnez **Sites Web**.
2.  Cliquez sur le nom du site web dans la liste.
    ![WebSiteEntry][]
    Le tableau de bord du site web s'affiche.

3.  Cliquez sur **Configurer** dans la barre de menus.
    ![WebSiteDashboardConfig][]

4.  Faites défiler jusqu’à la section Chaînes de connexion.
    ![WebSiteConnectionStrings][]

5.  Dans **Name**, entrez MONGOLAB\_URI.
6.  Dans **Value**, collez la chaîne de connexion obtenue dans la section précédente.
7.  Sélectionnez **Custom** dans la liste déroulante **Type** (à la place de la valeur par défaut **SQLAzure**).
8.  Cliquez sur **Enregistrer** dans la barre d’outils.  
    ![SaveWebSite][]

**Remarque :**Azure ajoute le préfixe **CUSTOMCONNSTR\_** à cette variable, ce qui explique que le code ci-dessus fasse référence à **CUSTOMCONNSTR\_MONGOLAB\_URI.**

  [WebSiteEntry]: ./media/howto-save-connectioninfo-mongolab/entry-website.png
  [WebSiteDashboardConfig]: ./media/howto-save-connectioninfo-mongolab/focus-mongolab-websitedashboard-config.png
  [WebSiteConnectionStrings]: ./media/howto-save-connectioninfo-mongolab/focus-mongolab-websiteconnectionstring.png
  [SaveWebSite]: ./media/howto-save-connectioninfo-mongolab/button-website-save.png

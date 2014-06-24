Même s'il est possible de coller un URI MongoLab dans votre code, il est
recommandé de le configurer dans l'environnement pour faciliter la
gestion. De cette façon, si l'URI change, vous pouvez le modifier via le
portail de gestion Azure sans devoir modifier le code.

1.  Dans le portail Azure, sélectionnez **Web Sites**.
2.  Cliquez sur le nom du site Web dans la liste.  
     ![EntréeSiteWeb](./media/howto-save-connectioninfo-mongolab/entry-website.png)
    
     Le tableau de bord du site Web s'affiche.

3.  Cliquez sur **Configure** dans la barre de menus.  
     ![ConfigurerTableaudebordSiteWeb](./media/howto-save-connectioninfo-mongolab/focus-mongolab-websitedashboard-config.png)

4.  Faites défiler jusqu'à la section Connection Strings.  
     ![ChaînesConnexionSiteWeb](./media/howto-save-connectioninfo-mongolab/focus-mongolab-websiteconnectionstring.png)

5.  Dans **Name**, entrez MONGOLAB\_URI.
6.  Dans **Value**, collez la chaîne de connexion obtenue dans la
    section précédente.
7.  Sélectionnez **Custom** dans la liste déroulante **Type** (à la
    place de la valeur par défaut **SQLAzure**).
8.  Cliquez sur **Save** dans la barre d'outils.  
     ![EnregistrerSiteWeb](./media/howto-save-connectioninfo-mongolab/button-website-save.png)

**Remarque :**Azure ajoute le préfixe **CUSTOMCONNSTR\_** à cette
variable, ce qui explique que le code ci-dessus fasse référence à
**CUSTOMCONNSTR\_MONGOLAB\_URI.**


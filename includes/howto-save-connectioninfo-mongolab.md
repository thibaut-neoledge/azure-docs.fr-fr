Même s’il est possible de coller un URI MongoLab dans votre code, il est recommandé de le configurer dans l’environnement pour faciliter la gestion. De cette façon, si l’URI change, vous pouvez le modifier via le portail de gestion Azure sans devoir modifier le code.

1. Dans le portail Azure, sélectionnez **Applications web**.
2. Cliquez sur le nom de l'application web dans la liste Applications web. ![WebAppEntry][entry-website] Le tableau de bord de l'application web s'affiche.
3. Cliquez sur **Configurer** dans la barre de menus. ![WebAppDashboardConfig][focus-mongolab-websitedashboard-config]
4. Faites défiler jusqu'à la section Connection Strings. ![WebAppConnectionStrings][focus-mongolab-websiteconnectionstring]
5. Dans **Name**, entrez MONGOLAB\_URI.
6. Dans **Value**, collez la chaîne de connexion obtenue dans la section précédente.
7. Sélectionnez **Custom** dans la liste déroulante **Type** (à la place de la valeur par défaut **SQLAzure**).
8. Cliquez sur **Save** dans la barre d'outils. ![SaveWebApp][button-website-save]

**Remarque :** Azure ajoute le préfixe **CUSTOMCONNSTR\_** à cette variable, ce qui explique que le code ci-dessus référence **CUSTOMCONNSTR\_MONGOLAB\_URI.**

[entry-website]: ./media/howto-save-connectioninfo-mongolab/entry-website.png
[focus-mongolab-websitedashboard-config]: ./media/howto-save-connectioninfo-mongolab/focus-mongolab-websitedashboard-config.png
[focus-mongolab-websiteconnectionstring]: ./media/howto-save-connectioninfo-mongolab/focus-mongolab-websiteconnectionstring.png
[button-website-save]: ./media/howto-save-connectioninfo-mongolab/button-website-save.png

<!---HONumber=Oct15_HO3-->
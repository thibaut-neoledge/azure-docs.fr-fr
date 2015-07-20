Même s’il est possible de coller un URI MongoLab dans votre code, il est recommandé de le configurer dans l’environnement pour faciliter la gestion. De cette façon, si l’URI change, vous pouvez le modifier via le portail de gestion Azure sans devoir modifier le code.


1. Dans le portail Azure, sélectionnez **Applications web**.
1. Cliquez sur le nom de l'application web dans la liste Applications web. ![WebAppEntry][entry-website] Le tableau de bord de l'application web s'affiche.

1. Cliquez sur **Configurer** dans la barre de menus. ![WebAppDashboardConfig][focus-mongolab-websitedashboard-config]

1. Faites défiler jusqu'à la section Connection Strings. ![WebAppConnectionStrings][focus-mongolab-websiteconnectionstring]

1. Dans **Name**, entrez MONGOLAB_URI.
1. Dans **Value**, collez la chaîne de connexion obtenue dans la section précédente.
1. Sélectionnez **Custom** dans la liste déroulante **Type** (à la place de la valeur par défaut **SQLAzure**).
1. Cliquez sur **Save** dans la barre d'outils. ![SaveWebApp][button-website-save]

**Remarque :** Azure ajoute le préfixe **CUSTOMCONNSTR_** à cette variable, ce qui explique que le code ci-dessus référence **CUSTOMCONNSTR_MONGOLAB_URI.**

[entry-website]: ./media/howto-save-connectioninfo-mongolab/entry-website.png
[focus-mongolab-websitedashboard-config]: ./media/howto-save-connectioninfo-mongolab/focus-mongolab-websitedashboard-config.png
[focus-mongolab-websiteconnectionstring]: ./media/howto-save-connectioninfo-mongolab/focus-mongolab-websiteconnectionstring.png
[button-website-save]: ./media/howto-save-connectioninfo-mongolab/button-website-save.png

<!---HONumber=July15_HO2-->
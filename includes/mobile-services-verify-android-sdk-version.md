En raison des développements en cours, la version du Kit de développement logiciel (SDK) Android installée dans Android Studio peut être différente de celle du code. Le Kit de développement logiciel (SDK) Android utilisé dans ce didacticiel correspond à la version 21, dernière version disponible au moment de la rédaction de ce document. Le numéro de la version peut être supérieur à mesure que de nouvelles versions du Kit de développement logiciel (SDK) apparaissent, et nous vous recommandons d'utiliser la dernière version disponible.

Deux symptômes permettent d'identifier des versions différentes :

1. Lorsque vous générez ou régénérez le projet, vous pouvez obtenir des messages d'erreur Gradle comme " **Impossible de trouver la cible Google Inc.:Google APIs:n** ".

2. Les objets Android standard du code dont la résolution doit reposer sur les instructions `import` peuvent générer des messages d'erreur.

Dans ce cas, la version du Kit de développement logiciel (SDK) Android installée dans Android Studio peut être différente de celle du Kit de développement logiciel (SDK) cible du projet téléchargé.  Pour vérifier la version, apportez les modifications suivantes :


1. Android Studio, cliquez sur **Outils** => **Android** => **Gestionnaire de Kit de développement (SDK)**. Si vous n'avez pas installé la dernière version de la plateforme de Kit de développement logiciel (SDK), cliquez pour l'installer. Prenez note du numéro de la version.

2. Dans l'onglet Explorateur de projets, sous **Scripts Gradle**, ouvrez le fichier **gradle.build (modeule: app)**. Vérifiez que **compileSdkVersion** et **buildToolsVersion** sont définis sur la dernière version installée du Kit de développement logiciel (SDK). Les balises peuvent se présenter comme suit :
 
	 	    compileSdkVersion 'Google Inc.:Google APIs:21'
    		buildToolsVersion "21.1.2"
	
3. Dans l'Explorateur de projets d'Android Studio, cliquez avec le bouton droit sur le nœud de projet, choisissez **Propriétés**, puis dans la colonne de gauche, choisissez **Android**. Vérifiez que la version du Kit de développement logiciel (SDK) définie pour **Cible de la génération du projet** est identique à celle de **targetSdkVersion**.

4. Dans Android Studio, le fichier manifeste ne permet plus de spécifier le Kit de développement (SDK) cible et la version minimale du Kit de développement logiciel (SDK), contrairement à Eclipse.
<!--HONumber=47-->

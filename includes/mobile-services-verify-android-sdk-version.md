En raison des développements en cours, la version du Kit de développement logiciel (SDK) Android installée dans Eclipse peut être différente de celle du code. Le Kit de développement logiciel (SDK) Android utilisé dans ce didacticiel correspond à la version 19, dernière version disponible au moment de la rédaction de ce document. Le numéro de la version peut être supérieur à mesure que de nouvelles versions du Kit de développement logiciel (SDK) apparaissent, et nous vous recommandons d'utiliser la dernière version disponible.

Deux symptômes permettent d'identifier des versions différentes :

1.  Consultez le volet inférieur de la console Eclipse. Des messages d'erreur de type «  **Impossible de résoudre la cible 'android-n'** » peuvent apparaître.

2.  Les objets Android standard du code dont la résolution doit reposer sur les instructions `import` peuvent générer des messages d'erreur.

	Dans ce cas, la version du Kit de développement logiciel (SDK) Android installée dans Eclipse peut être différente de celle du Kit de développement logiciel (SDK) cible du projet téléchargé. Pour vérifier la version, apportez les modifications suivantes :

1.  Dans Eclipse, cliquez sur **Window**, puis sur **Android SDK Manager**. Si vous n'avez pas installé la dernière version de la plateforme de Kit de développement logiciel (SDK), cliquez pour l'installer. Prenez note du numéro de la version.

2.  Ouvrez le fichier projet **AndroidManifest.xml**. Dans l'élément **uses-sdk**, vérifiez que **targetSdkVersion** est défini sur la dernière version installée. La balise **uses-sdk** peut se présenter comme suit :

    &lt;uses-sdk android:minSdkVersion="8" android:targetSdkVersion="19" /\>

3.  Dans l'Explorateur de package d'Eclipse, cliquez avec le bouton droit sur le nœud de projet, choisissez **Properties**, puis dans la colonne de gauche choisissez **Android**. Vérifiez que la version du Kit de développement logiciel (SDK) définie pour **Project Build Target** est identique à celle de **targetSdkVersion**.



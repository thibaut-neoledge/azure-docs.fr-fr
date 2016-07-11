<properties
    pageTitle="Points de terminaison de service Azure"
    description="Décrit les paramètres de point de terminaison de Service Azure dans la boîte à outils Azure pour Eclipse."
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="06/24/2016" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/dn268600.aspx -->

# Points de terminaison de service Azure #

Les points de terminaison de service Azure déterminent si votre application est déployée et gérée par la plateforme globale Azure, si elle fonctionne sur Azure géré par 21Vianet en Chine ou sur une plateforme Azure privée. La boîte de dialogue **Points de terminaison de service** vous permet de spécifier les points de terminaison du service à utiliser. Pour ouvrir la boîte de dialogue **Points de terminaison de service** dans Eclipse, cliquez sur **Fenêtre**, sur **Préférences**, développez **Azure**, puis cliquez sur **Points de terminaison de Service**. La définition du champ **Ensemble actif** détermine les points de terminaison de service Azure qui seront utilisés pour les projets Azure dans votre espace de travail actuel.

Le schéma qui suit représente la boîte de dialogue **Points de terminaison de Service**.

![][ic719493]

## Pour définir les points de terminaison de service Azure ##

Dans la boîte de dialogue **Points de terminaison de service**, effectuez l’une des actions suivantes :

* Si vous souhaitez utiliser la plateforme Azure globale, à partir de la liste déroulante **Ensemble actif**, sélectionnez **windowsazure.com**, puis cliquez sur **OK**.
* Si vous souhaitez utiliser Azure sous 21Vianet en Chine, dans la liste déroulante **Ensemble actif**, sélectionnez **windowsazure.cn** (Chine), puis cliquez sur **OK**.
* Si vous souhaitez utiliser une plateforme Azure privée :
    1. Cliquez sur **Modifier**.
    2. Une boîte de dialogue s’ouvre, vous informant que la boîte de dialogue **Points de terminaison de service** va être fermée et le fichier d’ensemble de préférences s’ouvre. Cliquez sur **OK**.
    3. Dans le fichier preferencesets.xml, créez un nouvel élément `preferenceset`. Pour ce nouvel élément, créez les attributs `name`, `blob`, `management`, `portalURL` et `publishsettings`, puis ajoutez à ces dernières des valeurs qui correspondent à votre plateforme Azure privée. Vous pouvez utiliser les valeurs fournies pour les éléments `preferenceset` existants en tant que modèles. **Remarque** : la valeur utilisée pour l’attribut `blob`doit contenir le texte « blob » dans l’URL.
    4. Enregistrez et fermez preferencesets.xml.
    5. Rouvrez la boîte de dialogue **Points de terminaison de service**.
    6. À partir de la liste déroulante d’**ensembles d’actifs**, sélectionnez l’ensemble actif que vous avez créé, cliquez sur **OK**.
    7. Une fois que vous avez créé votre élément de plateforme Azure privée `preferenceset`, vous pouvez modifier les valeurs qui lui sont affectées en cliquant sur le bouton **Modifier** situé dans la boîte de dialogue **Point de terminaison des services**. Vous pouvez également créer plusieurs éléments de plateforme Azure privée `preferenceset` si vous le souhaitez.

## Voir aussi ##

[Kit de ressources Azure pour Eclipse][]

[Installation du kit de ressources Azure pour Eclipse][]

[Création d'une application Hello World pour Azure dans Eclipse][]

Pour plus d'informations sur l'utilisation d'Azure avec Java, consultez le [Centre de développement Java][].

<!-- URL List -->

[Centre de développement Java]: http://go.microsoft.com/fwlink/?LinkID=699547
[Kit de ressources Azure pour Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Création d'une application Hello World pour Azure dans Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Installation du kit de ressources Azure pour Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546

<!-- IMG List -->

[ic719493]: ./media/azure-toolkit-for-eclipse-azure-service-endpoints/ic719493.png

<!---HONumber=AcomDC_0629_2016-->
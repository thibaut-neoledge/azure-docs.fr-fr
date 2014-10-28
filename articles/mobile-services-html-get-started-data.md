<properties linkid="develop-mobile-tutorials-get-started-with-data-html" urlDisplayName="Get Started with Data (HTML5)" pageTitle="Get started with data (HTML 5) | Mobile Dev Center" metaKeywords="" description="Learn how to get started using Mobile Services to leverage data in your HTML app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-html" ms.devlang="javascript" ms.topic="article" ms.date="09/24/2014" ms.author="glenga"></tags>

# Prise en main des données dans Mobile Services

[WACOM.INCLUDE [mobile-services-selector-get-started-data][]]

Cette rubrique montre comment utiliser Azure Mobile Services pour tirer parti des données dans une application HTML. Dans ce didacticiel, vous allez télécharger une application qui stocke les données en mémoire, créer un service mobile, intégrer le service mobile à l'application, puis vous connecter au portail de gestion Azure pour afficher les modifications apportées aux données lors de l'exécution de l'application.

> [WACOM.NOTE]Ce didacticiel vise à mieux vous faire comprendre en quoi Mobile Services vous permet de stocker et de récupérer des données à partir d'une application HTML à l'aide d'Azure. À cet effet, il vous guide tout au long des nombreuses étapes exécutées automatiquement dans le démarrage rapide Mobile Services. Si vous n'avez aucune expérience de Mobile Services, nous vous invitons à suivre d'abord le didacticiel [Prise en main de Mobile Services][].

Ce didacticiel vous familiarise avec ces étapes de base :

1.  [Téléchargement du projet d'application HTML][]
2.  [Création du service mobile][]
3.  [Ajout d'une table de données pour le stockage][]
4.  [Mise à jour de l'application pour utiliser Mobile Services][]
5.  [Test de l'application avec Mobile Services][]

<div class="dev-callout"><strong>Remarque</strong> <p>Pour effectuer ce didacticiel, vous avez besoin d&rsquo;un compte Azure. Si vous ne poss&eacute;dez pas de compte, vous pouvez cr&eacute;er un compte d'&eacute;valuation gratuit en quelques minutes. Pour plus d'informations, consultez la page <a href="http://www.windowsazure.com/fr-fr/pricing/free-trial/?WT.mc_id=A756A2826&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Ffr-fr%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-html%2F" target="_blank">Version d'&eacute;valuation gratuite d'Azure</a>.</p></div>

### Conditions supplémentaires

Vous pouvez héberger l'application GetStartedWithData sur n'importe quel serveur Web. Cependant, des scripts sont fournis afin de vous permettre d'exécuter l'application sur `http://localhost:8000`.

-   Pour utiliser localhost, l'un des serveurs Web suivants doit être exécuté sur votre ordinateur local pour pouvoir suivre ce didacticiel :

    -   **Sur Windows** : IIS Express. IIS Express est installé par [Microsoft Web Platform Installer].
    -   **Sur Mac OS X** : Python, qui doit déjà être installé.
    -   **Sur Linux** : Python. Vous devez installer [la dernière version de Python].

    Vous pouvez utiliser n'importe quel serveur Web pour héberger l'application, mais les serveurs précédents sont les seuls pris en charge par les scripts téléchargés.

-   Un navigateur Web qui prend en charge le HTML5

## <a name="download-app"></a><span class="short-header">Téléchargement du projet</span>Téléchargement du projet GetStartedWithData

Ce didacticiel est basé sur l'application [GetStartedWithData][], qui est une application HTML5. L'interface utilisateur de cette application est identique à l'application générée par le démarrage rapide de Mobile Services, sauf que les éléments ajoutés sont stockés en local dans la mémoire.

1.  [Téléchargement des fichiers du projet d'application HTML][GetStartedWithData].

2.  Dans un éditeur HTML, ouvrez le projet téléchargé et étudiez le fichier app.js.

    Les éléments ajoutés sont stockés dans un objet **Array** en mémoire (**staticItems**). Actualisez la page pour afficher les données. Elles ne persistent pas.

3.  Lancez un des fichiers de commande depuis le sous-dossier **server**.

    -   **launch-windows** (pour les ordinateurs Windows)
    -   **launch-mac.command** (pour les ordinateurs Mac OS X)
    -   **launch-linux.sh** (pour les ordinateurs Linux)

    <div class="dev-callout"><b>Remarque</b>
    <p>Sur un ordinateur Windows, appuyez sur la touche &laquo;&nbsp;R&nbsp;&raquo; lorsque PowerShell vous demande de confirmer l'ex&eacute;cution du script. Vous pouvez recevoir un avertissement de votre navigateur Web vous recommandant de ne pas ex&eacute;cuter le script, car il a &eacute;t&eacute; t&eacute;l&eacute;charg&eacute; depuis Internet. Lorsque cela se produit, vous devez demander au navigateur de continuer &agrave; charger le script.</p>
</div>

    Ceci démarre un serveur Web sur votre ordinateur local pour pouvoir héberger la nouvelle application.

4.  Ouvrez l'URL <http://localhost:8000/> dans un navigateur Web pour démarrer l'application.

5.  Dans l'application, tapez un texte explicite, comme *Suivre le didacticiel*, dans **Entrer une nouvelle tâche**, puis cliquez sur **Ajouter**.

    ![][]

    Le texte enregistré est ajouté au tableau **staticItems** et la page est mise à jour pour afficher le nouvel élément.

## <a name="create-service"></a><span class="short-header">Création d'un service mobile</span>Création d'un service mobile dans le portail de gestion

[WACOM.INCLUDE [mobile-services-create-new-service-data][]]

## <a name="add-table"></a><span class="short-header">Ajout d'une nouvelle table</span>Ajout d'une nouvelle table au service mobile

Pour pouvoir stocker des données d'application dans le nouveau service mobile, vous devez d'abord créer une table dans l'instance de base de données SQL associée.

1.  Dans le portail de gestion, cliquez sur **Mobile Services**, puis sur le service mobile que vous venez de créer.

2.  Cliquez sur l'onglet **Données**, puis sur **+Create**.

    ![][1]

    La boîte de dialogue **Create new table** s’affiche.

3.  Dans **Nom de la table**, tapez *TodoItem*, puis cliquez sur le bouton de vérification.

    ![][2]

    Une nouvelle table de stockage **TodoItem** est créée avec les autorisations par défaut. Cela signifie que quiconque possédant la clé de l'application, qui est distribuée avec l'application, peut accéder aux données de la table et les modifier.

    <div class="dev-callout"> 
<b>Remarque</b> 
<p>Le m&ecirc;me nom de table est utilis&eacute; dans le d&eacute;marrage rapide avec Mobile Services. Toutefois, chaque table est cr&eacute;&eacute;e dans un sch&eacute;ma sp&eacute;cifique pour un service mobile donn&eacute;. Cela vise &agrave; &eacute;viter les collisions de donn&eacute;es lorsque plusieurs services mobiles utilisent la m&ecirc;me base de donn&eacute;es.</p> 
</div>

4.  Cliquez sur la nouvelle table **TodoItem** et vérifiez qu'aucune ligne de données n'est présente.

5.  Cliquez sur l'onglet **Colonnes**. Vérifiez si les colonnes par défaut suivantes ont été automatiquement créées pour vous :

	<table>
    <tr><td> <strong>Nom de la colonne</strong> </td><td> <strong>Type</strong> </td><td> <strong>Index</strong> </td></tr>
    <tr><td> id                </td><td> string            </td><td> Indexé                             </td></tr>
    <tr><td> __createdAt     </td><td> date              </td><td> Indexé                             </td></tr>
    <tr><td> __updatedAt     </td><td> date              </td><td> <font color="transparent">-</font> </td></tr>
    <tr><td> __version       </td><td> timestamp (MSSQL) </td><td> <font color="transparent">-</font> </td></tr>
	</table>

    Cela correspond à la configuration minimale requise pour une table dans Mobile Services.

    <div class="dev-callout"><b>Remarque</b>
<p>Lorsque le sch&eacute;ma dynamique est activ&eacute; sur votre service mobile, de nouvelles colonnes sont cr&eacute;&eacute;es automatiquement lorsque des objets JSON sont envoy&eacute;s au service mobile par une op&eacute;ration d'insertion ou de mise &agrave; jour.</p>
</div>

6.  Sous l'onglet **Configurer** vérifiez que `localhost` figure déjà dans la liste **Autoriser les demandes à partir des noms d'hôte** sous **Partage des ressources cross-origin (CORS)**. Si cela n'est pas le cas, entrez `localhost` dans le champ **Nom d'hôte**, puis cliquez sur **Enregistrer**.

    ![][3]

    <div class="dev-callout"><b>Remarque</b>
    <p>Si vous d&eacute;ployez l'application de d&eacute;marrage rapide sur un autre serveur que localhost, vous devez ajouter le nom d'h&ocirc;te du serveur Web &agrave; la liste <strong>Autoriser les demandes &agrave; partir des noms d'h&ocirc;te</strong>. Pour plus d'informations, consultez la page <a href="http://msdn.microsoft.com/fr-fr/library/windowsazure/dn155871.aspx" target="_blank">Partage des ressources cross-origin</a>.</p>
</div>

Vous pouvez maintenant utiliser le nouveau service mobile en tant que stockage des données pour l'application.

## <a name="update-app"></a><span class="short-header">Mise à jour de l'application</span>Mise à jour de l'application afin d'utiliser le service mobile pour l'accès aux données

Votre service mobile étant prêt, vous pouvez mettre à jour l'application pour stocker les éléments dans Mobile Services et non dans la collection locale.

1.  Dans le portail de gestion, cliquez sur **Mobile Services**, puis sur le service mobile que vous venez de créer.

2.  Cliquez sur l'onglet **Tableau de bord** et notez la valeur **URL du site**, puis cliquez sur **Gérer les clés** et notez la valeur de **Clé de l'application**.

    ![][4]

    Ces valeurs sont nécessaires pour accéder au service mobile à partir de votre code d'application.

3.  Dans un éditeur Web, ouvrez le fichier projet index.html et ajoutez ce qui suit aux références de script de la page :

        <script src='http://ajax.aspnetcdn.com/ajax/mobileservices/MobileServices.Web-1.1.2.min.js'></script>

4.  Dans l'éditeur, ouvrez le fichier JavaScript, supprimez les marques de commentaire du code suivant qui définit la variable **MobileServiceClient** et fournissez l'URL et la clé d'application du service mobile dans le constructeur **MobileServiceClient**, dans cet ordre :

        var MobileServiceClient = WindowsAzure.MobileServiceClient,
            client = new MobileServiceClient('AppUrl', 'AppKey'),               

    Cela crée une instance de MobileServiceClient utilisée pour accéder au service mobile.

5.  Supprimez les marques de commentaire sur les lignes de code suivantes :

        var itemCount = 0;
        var staticItems = [];

    Les données seront stockées dans le service mobile et non dans le tableau en mémoire.

6.  Supprimez les marques de commentaire sur la ligne de code suivante :

        todoItemTable = client.getTable('todoitem');

    Ce code crée un objet proxy (**todoItemTable**) pour la base de données SQL **TodoItem**.

7.  Remplacez le gestionnaire d'événements **$('\#add-item').submit** par le code suivant :

        $('#add-item').submit(function(evt) {
            var textbox = $('#new-item-text'),
                itemText = textbox.val();
            if (itemText !== '') {
                todoItemTable.insert({ text: itemText, complete: false })
                    .then(refreshTodoItems);
            }
            textbox.val('').focus();
            evt.preventDefault();
        });

    Ce code permet d'insérer un nouvel élément dans la table.

8.  Remplacez la méthode **refreshTodoItems** par le code suivant :

        function refreshTodoItems() {

            var query = todoItemTable;

            query.read().then(function(todoItems) {
                listItems = $.map(todoItems, function(item) {
                    return $('<li>')
                        .attr('data-todoitem-id', item.id)
                        .append($('<button class="item-delete">Delete</button>'))
                        .append($('<input type="checkbox" class="item-complete">').prop('checked', item.complete))
                        .append($('


????????????

<div class="dev-callout">

<b>Remarque</b>

<p>Si vous devez red&eacute;marrer le serveur Web, r&eacute;p&eacute;tez les &eacute;tapes de la premi&egrave;re section.</p>
</div>

2.  Comme précédemment, tapez du texte dans **Entrer une nouvelle tâche**, puis cliquez sur **Ajouter**.

    Cela permet d'envoyer un nouvel élément en tant qu'insertion au service mobile.

3.  Dans le [portail de gestion][], cliquez sur **Mobile Services**, puis sur le service mobile.

4.  Cliquez sur l'onglet **Données**, puis sur **Parcourir**.

    ![][5]

    La table **TodoItem** contient à présent des données, dont les valeurs d'ID ont été générées par Mobile Services, et les colonnes ont été automatiquement ajoutées à la table de manière à correspondre à la classe TodoItem au sein de l'application.

5.  Dans l'application, cochez un des éléments dans la liste, puis revenez à l'onglet Parcourir dans le portail et cliquez sur **Actualiser**.

    Notez que la valeur terminée est passée de **false** à **true**.

6.  Dans le fichier projet app.js, recherchez la méthode **RefreshTodoItems** et remplacez la ligne de code qui définit `query` par ce qui suit :

        var query = todoItemTable.where({ complete: false });

7.  Rechargez la page, vérifiez un des autres éléments de la liste.

    Notez que l'élément coché n'est plus affiché dans la liste. Chaque mise à jour provoque un aller-retour vers le serveur mobile, qui renvoie les données filtrées.

Vous voici parvenu à la fin du didacticiel **Prise en main des données**.

## <a name="next-steps"> </a>Étapes suivantes

Ce didacticiel a présenté les bases de l'activation d'une application HTML pour utiliser les données dans Mobile Services. Ensuite, pensez à suivre l'un des didacticiels suivants basés sur l'application GetStartedWithData que vous avez créée dans ce didacticiel :

-   [Validation et modification des données avec des scripts][]

    En savoir plus sur l'utilisation des scripts serveur dans Mobile Services pour valider et modifier les données envoyées à partir de votre application.

-   [Affinage des requêtes à la pagination][]

    En savoir plus sur l'utilisation de la pagination dans les requêtes pour contrôler la quantité de données traitées dans une seule requête.

Une fois que vous avez terminé la série de données, découvrez comment authentifier les utilisateurs de votre application en suivant le didacticiel [Prise en main de l'authentification][].

  [mobile-services-selector-get-started-data]: ../includes/mobile-services-selector-get-started-data.md
  [Prise en main de Mobile Services]: /fr-fr/develop/mobile/tutorials/get-started-html
  [Téléchargement du projet d'application HTML]: #download-app
  [Création du service mobile]: #create-service
  [Ajout d'une table de données pour le stockage]: #add-table
  [Mise à jour de l'application pour utiliser Mobile Services]: #update-app
  [Test de l'application avec Mobile Services]: #test-app
  [Version d'évaluation gratuite d'Azure]: http://www.windowsazure.com/fr-fr/pricing/free-trial/?WT.mc_id=A756A2826&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Ffr-fr%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-html%2F
  [GetStartedWithData]: http://go.microsoft.com/fwlink/?LinkID=286345
  []: ./media/mobile-services-html-get-started-data/mobile-quickstart-startup-html.png
  [mobile-services-create-new-service-data]: ../includes/mobile-services-create-new-service-data.md
  [1]: ./media/mobile-services-html-get-started-data/mobile-data-tab-empty.png
  [2]: ./media/mobile-services-html-get-started-data/mobile-create-todoitem-table.png
  [3]: ./media/mobile-services-html-get-started-data/mobile-services-set-cors-localhost.png
  [Partage des ressources cross-origin]: http://msdn.microsoft.com/fr-fr/library/windowsazure/dn155871.aspx
  [4]: ./media/mobile-services-html-get-started-data/mobile-dashboard-tab.png
  [portail de gestion]: https://manage.windowsazure.com/
  [5]: ./media/mobile-services-html-get-started-data/mobile-todoitem-data-browse.png
  [Validation et modification des données avec des scripts]: /fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-html
  [Affinage des requêtes à la pagination]: /fr-fr/develop/mobile/tutorials/add-paging-to-data-html
  [Prise en main de l'authentification]: /fr-fr/develop/mobile/tutorials/get-started-with-users-html

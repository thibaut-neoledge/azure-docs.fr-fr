<properties linkid="develop-net-tutorials-multi-tier-web-site-1-overview" urlDisplayName="Step 1: Overview" pageTitle="ASP.NET Multi-tier Web Application with Azure - Step 1: Overview" metaKeywords="Azure tutorial, email list service app, email service architecture, Azure tutorial overview, Azure multi-tier, Azure storage, Azure blobs, Azure tables, Azure queues" description="Learn about the five-part multi-tier Azure web application tutorial." metaCanonical="" services="cloud-services,storage" documentationCenter=".NET" title="Multi-tier ASP.NET MVC Web Site Tutorial - Step 1: Overview" authors="tdykstra,riande" solutions="" manager="wpickett" editor="mollybos" />

Application Web ASP.NET multiniveau avec tables, files d'attente et objets blob Azure Storage - 1 sur 5
=======================================================================================================

Cette série de didacticiels vous présente la création d'une application Web ASP.NET MVC multiniveau qui utilise des tables, des files d'attente et des objets blob Azure Storage, et vous présente comment déployer l'application dans Azure Cloud Services. Ces didacticiels partent du principe que vous n'avez pas d'expérience en tant qu'utilisateur d'Azure. À la fin de cette série, vous saurez comment concevoir une application Web pilotée par les données, robuste et extensible, et comment la déployer dans le cloud.

Ce contenu est disponible sous forme de livre électronique gratuit dans la [galerie de livres électroniques TechNet](http://social.technet.microsoft.com/wiki/contents/articles/11608.e-book-gallery-for-microsoft-technologies.aspx#ASPNETMultiTierWindowsAzureApplicationUsingStorageTablesQueuesandBlobs).

ContenuContenu
--------------

Dans cette série de didacticiels, vous allez apprendre les procédures suivantes :

-   configurer votre machine pour le développement Azure en installant le Kit de développement logiciel (SDK) Azure ;
-   créer un projet cloud Visual Studio avec un rôle Web ASP.NET MVC et deux rôles de travail ;
-   publier le projet cloud dans Azure Cloud Services ;
-   publier le projet MVC dans un site Web Azure si vous préférez et utiliser les rôles de travail dans un service cloud ;
-   utiliser le service de stockage de file d'attente Azure pour la communication entre des niveaux ou rôles de travail ;
-   utiliser le service de stockage de tables Azure comme magasin de données hautement extensible pour les données structurées non relationnelles ;
-   utiliser le service d'objets blob Azure pour stocker les fichiers dans le cloud ;
-   consulter et modifier des tables, des files d'attente et des objets blob Azure avec Visual Studio ou l'Explorateur de stockage Azure ;
-   utiliser SendGrid pour envoyer des messages électroniques ;
-   configurer le suivi et afficher les données associées ;
-   étendre une application par augmentation du nombre d'instances de rôle de travail.

Présentation du composant frontalPrésentation du composant frontal
------------------------------------------------------------------

L'application que vous concevez est un service de liste de diffusion. Le composant frontal de l'application multiniveau comprend des pages Web que les administrateurs du service utilisent pour gérer les listes de diffusion.

![Page d'index de la liste de diffusion](./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-mailing-list-index-page.png)

![Page d'index de l'abonné](./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-subscribers-index-page.png)

Il existe également un jeu de pages que les administrateurs utilisent pour créer les messages à envoyer à une liste.

![Page d'index du message](./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-message-index-page.png)

![Page de création du message](./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-message-create-page.png)

Les clients du service sont des sociétés qui offrent à leurs clients la possibilité de s'abonner à une liste de diffusion sur leur site Web. Par exemple, l'administrateur configure une liste pour les annonces du département d'histoire de l'université Contoso. Quand un étudiant intéressé par les annonces du département d'histoire clique sur le lien sur le site Web de l'université Contoso, l'université passe un appel de service Web à l'application Azure Email Service. Cette méthode de service provoque l'envoi d'un courrier électronique au client. Ce courrier contient un lien hypertexte, et lorsque le destinataire clique dessus, une page de bienvenue dans la liste des annonces du département d'histoire s'affiche.

![Message de confirmation](./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-subscribe-email.png)

![Page de bienvenue dans la liste](./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-subscribe-confirmation-page.png)

Tous les messages envoyés par le service (à l'exception de la confirmation d'abonnement) comportent un lien hypertexte qui permet de se désabonner. Si le destinataire clique sur ce lien, une page Web lui demande de confirmer s'il a bien l'intention de se désabonner.

![Page de confirmation du désabonnement](./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-unsubscribe-query-page.png)

Si le destinataire clique sur le bouton **Confirm**, la page qui s'affiche confirme que la personne a été supprimée de la liste.

![Page de désabonnement confirmé](./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-unsubscribe-confirmation-page.png)

DidacticielsDidacticiels de la série
------------------------------------

Voici une liste des didacticiels avec un résumé de leur contenu :

1.  **Présentation de l'application Azure Email Service** (ce didacticiel). Présentation de l'application et de son architecture.
2.  [Configuration et déploiement de l'application Azure Email Service](/en-us/develop/net/tutorials/multi-tier-web-site/2-download-and-run/). Déploiement de l'exemple d'application, configuration, test local, déploiement et test dans le cloud.
3.  [Création du rôle Web pour l'application Azure Email Service](/en-us/develop/net/tutorials/multi-tier-web-site/3-web-role/). Création des composants MVC 4 de l'application et test de ces composants en local.
4.  [Création du rôle de travail A (planificateur de messages) pour l'application Azure Email Service](/en-us/develop/net/tutorials/multi-tier-web-site/4-worker-role-a/). Création du composant principal qui crée les éléments de travail de la file d'attente pour envoyer les messages, et test en local.
5.  [Création du rôle de travail B (expéditeur de messages) pour l'application Azure Email Service](/en-us/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/). Création du composant principal qui traite les éléments de travail de la file d'attente pour envoyer les messages, et test en local.

Si vous voulez simplement télécharger l'application et l'essayer, vous n'avez besoin que des deux premiers didacticiels. Si vous souhaitez découvrir toutes les étapes de la création de ce genre d'application, regardez les trois derniers didacticiels après avoir terminé les deux premiers.

Pourquoi cette applicationPourquoi une application de service de liste de diffusion
-----------------------------------------------------------------------------------

Nous avons choisi un service de liste de diffusion pour cet exemple d'application, car c'est le genre d'application qui doit être robuste et extensible, deux caractéristiques particulièrement importantes d'Azure.

### Robustesse

Si un serveur échoue lors de l'envoi de messages à une liste volumineuse, ce que vous voulez, c'est pouvoir démarrer rapidement et facilement un nouveau serveur et que l'application puisse reprendre son travail là où elle s'est arrêtée, sans perdre ni dupliquer aucun des messages. L'instance de rôle Web ou de rôle de travail Azure Cloud Services (machine virtuelle) est automatiquement remplacée en cas d'échec. Les files d'attente et les tables Azure Storage assurent une communication de serveur à serveur qui peut survivre à un échec, sans perte du travail.

### Extensibilité

Le service de messagerie doit également être capable de gérer les pics de charge de travail, car vous envoyez des messages aussi bien à des petites listes qu'à de très grandes listes. Dans de nombreux environnements d'hébergement, vous devez acheter et assurer la maintenance de suffisamment de matériel pour gérer les pics de charge de travail, et vous payez pour cette capacité 100 % du temps, même si vous ne l'utilisez que 5 % du temps. Avec Azure, vous ne payez que pour la quantité de puissance de traitement dont vous avez réellement besoin, aussi longtemps que vous en avez besoin. Pour passer à la gestion d'une grande liste de diffusion, vous modifiez simplement un paramètre de configuration pour augmenter le nombre de serveurs disponibles pour traiter la charge de travail. Cette opération peut se faire de façon programmée. Par exemple, vous pouvez configurer l'application pour que, si le nombre d'éléments de travail attendant dans la file d'attente dépasse un certain nombre, Azure lance automatiquement des instances supplémentaires du rôle de travail qui traite ces éléments.

Présentation du composant principalPrésentation du composant principal
----------------------------------------------------------------------

Le composant frontal stocke les listes de courrier électronique et les messages qui doivent leur être envoyés dans des tables Azure. Lorsque l'administrateur prévoit l'envoi d'un message, la ligne d'une table contenant la date prévue et d'autres données telles que l'objet sont ajoutées à la table `message`. Un rôle de travail recherche régulièrement dans la table `message` les messages qui doivent être envoyés (nous appellerons ce rôle de travail A).

Lorsque le rôle de travail A trouve un message qui doit être envoyé, il exécute les tâches suivantes :

-   Il place toutes les adresses électroniques dans la liste de diffusion de destination.
-   Il place les informations nécessaires à l'envoi de chaque message dans la table `message`.
-   Il crée un élément de travail dans la file d'attente pour chaque message qui doit être envoyé.

Un second rôle de travail (rôle de travail B) interroge la file d'attente à la recherche d'éléments de travail. Lorsque le rôle de travail B rencontre un élément de travail, il le traite en envoyant le message, puis il le supprime de la file d'attente. Le schéma qui suit présente ces relations.

![Traitement du message](./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-worker-roles-a-and-b.png)

Aucun message n'est oublié si le rôle de travail B s'arrête et doit être redémarré, car l'élément de travail de la file d'attente pour un message n'est pas supprimé tant que le message n'a pas été envoyé. Le composant principal met également en œuvre un traitement de la table qui empêche que plusieurs messages soient envoyés au cas où le rôle de travail A s'arrête et doit être redémarré. Dans ce cas, plusieurs éléments de travail de file d'attente peuvent être générés pour une adresse électronique de destination donnée. Mais pour chaque adresse de destination, une ligne de la table `message` note si le message a été envoyé. En fonction du moment du redémarrage et du traitement des messages, le rôle de travail A utilise cette ligne pour éviter de créer un deuxième élément de travail de file d'attente, ou le rôle de travail B utilise cette ligne pour éviter d'envoyer un second message.

![Empêcher l'envoi de messages en double](./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-message-processing.png)

Le rôle de travail B interroge également une file d'attente d'abonnement à la recherche d'éléments de travail placés par la méthode de service de l'API Web pour les nouveaux abonnements. Lorsqu'il en trouve un, il envoie le message de confirmation.

![Traitement des messages de la file d'attente d'abonnement](./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-subscribe-diagram.png)

TablesTables Azure
------------------

L'application Azure Email Service stocke les données dans des tables Azure Storage. Les tables Azure sont un magasin de données NoSQL, et non une base de données relationnelle comme la [base de données SQL Azure](http://msdn.microsoft.com/en-us/library/windowsazure/ee336279.aspx). Cela en fait un choix intéressant lorsque l'efficacité et l'extensibilité sont plus importantes que la normalisation des données et l'intégrité des relations. Par exemple, dans cette application, un rôle de travail crée une ligne à chaque fois qu'un élément de travail de file d'attente est créé. Un autre rôle de travail récupère et met à jour une ligne chaque fois qu'un message est envoyé, ce qui peut constituer un goulot d'étranglement si vous utilisez une base de données relationnelle. En outre, les tables Azure sont moins chères que les tables Azure SQL. Pour plus d'informations sur les tables Azure, consultez les ressources répertoriées à la fin du [dernier didacticiel de cette série](/en-us/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/).

Les sections qui suivent décrivent le contenu des tables Azure qui sont utilisées par l'application Azure Email Service. Consultez la section [Schéma des données Azure Email Service](#datadiagram) plus bas sur cette page pour voir un schéma présentant les tables et leurs relations.

### Table mailinglist

La table `mailinglist` stocke les informations relatives aux listes de diffusion et aux abonnés de ces listes. (La meilleure pratique pour la convention des noms de tables Azure est d'utiliser uniquement des lettres minuscules.) Les administrateurs utilisent des pages Web pour créer et modifier des listes de diffusion et les clients et abonnés utilisent un ensemble de pages Web et une méthode de service pour l'abonnement et le désabonnement.

Dans les tables NoSQL, différentes lignes peuvent avoir différents schémas, et cette souplesse est régulièrement mise à profit pour créer des données de magasin de tables qui nécessiteraient plusieurs tables dans une base de données relationnelle. Par exemple, pour stocker les données de la liste de diffusion dans une base de données SQL, vous pouvez utiliser trois tables : une table `mailinglist` qui stocke les informations relatives à la liste, une table `subscriber` qui stocke les informations relatives aux abonnés et une table `mailinglistsubscriber` qui associe les listes de diffusion aux abonnés et vice-versa. Dans la table NoSQL de cette application, toutes ces fonctions sont regroupées dans une seule table appelée `mailinglist`.

Dans une table Azure, toutes les lignes comportent une *clé de partition* et une *clé de ligne* qui identifient la ligne de façon unique. La clé de partition divise la table en partitions de façon logique. Dans une partition, la clé de ligne identifie la ligne de façon unique. Il n'existe pas d'index secondaire. Pour vous assurer que l'application est extensible, il est donc important de concevoir vos tables de façon à ce que vous puissiez toujours spécifier des valeurs de clés de partition et de clés de ligne dans la clause Where des requêtes.

La clé de partition de la table `mailinglist` est le nom de la liste de diffusion.

La clé de ligne de la table `mailinglist` peut avoir une des deux valeurs suivantes : la constante « mailinglist » ou l'adresse électronique de l'abonné. Les lignes qui comportent la clé de ligne « mailinglist » contiennent des informations relatives à la liste de diffusion. Les lignes qui contiennent l'adresse électronique en tant que clé de ligne comportent des informations relatives aux abonnés de la liste.

En d'autres termes, les lignes qui comportent la clé de ligne « mailinglist » sont équivalentes à la table `mailinglist` dans une base de données relationnelle. Les lignes dont la clé de ligne est l'adresse électronique sont équivalentes à la table `subscriber` et à la table d'association `mailinglistsubscriber` dans une base de données relationnelle.

Le fait d'avoir une seule table pour plusieurs objectifs contribue à l'amélioration des performances. Dans une base de données relationnelle, il faudrait lire trois tables, et trois jeux de lignes devraient être triés et comparés, ce qui prend du temps. Ici, la lecture se fait dans une seule table et les lignes de cette table sont automatiquement renvoyées dans l'ordre de la clé de partition et de la clé de ligne.

La grille suivante présente les propriétés des lignes qui contiennent les informations de liste de diffusion (clé de ligne = « MailingList »).

<table  border="1">

<tr  bgcolor="lightgray">
<th>Propriété</th>

<th>Type de données</th>

<th>Description</th>

</tr>


<tr>
<td>PartitionKey</td>

<td>String</td>

<td>ListName :  identificateur unique de la liste de diffusion, par exemple : contoso1. Une utilisation régulière de la table est de pouvoir récupérer toutes les informations pour une liste de diffusion donnée ; l'utilisation du nom de la liste est donc un moyen d'efficace de partitionner la table.</td>

</tr>


<tr>
<td>RowKey</td>

<td>String</td>

<td>Constante mailinglist.</td>

</tr>


<tr>
<td>Description</td>

<td>String</td>

<td>Description de la liste de diffusion, par exemple : « Annonces du département d'histoire de l'université Contoso ».</td>

</tr>


<tr>
<td>FromEmailAddress</td>

<td>String</td>

<td>L'adresse « De » dans les messages envoyés à cette liste, par exemple : donotreply@contoso.edu.</td>

</tr>


</table>

La grille suivante présente les propriétés des lignes qui contiennent les informations relatives aux abonnés de la liste (clé de ligne = adresse électronique).

<table  border="1">

<tr  bgcolor="lightgray">
<th>Propriété</th>

<th>Type de données</th>

<th>Description</th>

</tr>


<tr>
<td>PartitionKey</td>

<td>String</td>

<td>ListName :  Nom (identificateur unique) de la liste de diffusion, par exemple : contoso1.</td>

</tr>


<tr>
<td>RowKey</td>

<td>String</td>

<td>EmailAddress :  adresse électronique de l'abonné, par exemple : student1@contoso.edu.</td>

</tr>


<tr>
<td>SubscriberGUID</td>

<td>String</td>

<td>Générée lors de l'ajout de l'adresse électronique à une liste. Utilisée dans les liens d'abonnement et de désabonnement, afin de rendre plus difficile l'abonnement ou le désabonnement de l'adresse électronique de quelqu'un d'autre. <br  />
<br  />
 Certaines requêtes des pages Web d'abonnement et de désabonnement spécifient uniquement PartitionKey et cette propriété. Le fait d'interroger une partition sans RowKey limite l'extensibilité de l'application, car les requêtes prennent plus de temps à mesure que la taille de la liste de diffusion augmente. Une possibilité pour améliorer l'extensibilité est d'ajouter des lignes de recherche qui contiennent le SubscriberGUID dans la propriété RowKey. Par exemple, pour chaque adresse électronique, une ligne peut contenir « email:student1@domain.com » dans RowKey et une autre ligne du même abonné « guid:6f32b03b-90ed-41a9-b8ac-c1310c67b66a » dans RowKey. Ceci est simple à mettre en œuvre, car les transactions atomiques par lots sur des lignes dans une partition sont simples à coder. Nous espérons mettre en œuvre cette fonction dans la prochaine version de l'exemple d'application. Pour plus d'informations, consultez la page <a  href="http://msdn.microsoft.com/en-us/library/windowsazure/hh508997.aspx">Environnement réel : Conception d'une stratégie de partition extensible pour le stockage de tables Azure</a>

</td>

</tr>


<tr>
<td>Verified</td>

<td>Boolean</td>

<td>Lors de la création initiale de la ligne pour le nouvel abonné, cette valeur est false. Elle passe à true uniquement lorsque le nouvel abonné clique sur le lien de confirmation dans le courrier électronique de bienvenue ou lorsqu'un administrateur la définit sur true. Si un message est envoyé à la liste alors que la valeur Verified d'un des abonnés est false, aucun message n'est envoyé à cet abonné.</td>

</tr>


</table>

La liste qui suit présente un exemple de ce à quoi peuvent ressembler les données dans la table.

<table  border="1">
<tr>
<th  width="200" align="right" bgcolor="lightgray">Partition Key</th>

<td>contoso1</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">Row Key</th>

<td>mailinglist</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">Description</th>

<td>Annonces du département d'histoire de l'université Contoso</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">FromEmailAddress</th>

<td>donotreply@contoso.edu</td>

</tr>


</table>

* * *


<table  border="1">

<tr>
<th  width="200" align="right" bgcolor="lightgray">Partition Key</th>

<td>contoso1</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">Row Key</th>

<td>student1@domain.com</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">SubscriberGUID</th>

<td>6f32b03b-90ed-41a9-b8ac-c1310c67b66a</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">Verified</th>

<td>true</td>

</tr>


</table>

* * *


<table  border="1">

<tr>
<th  width="200" align="right" bgcolor="lightgray">Partition Key</th>

<td>contoso1</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">Row Key</th>

<td>student2@domain.com</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">SubscriberGUID</th>

<td>01234567-90ed-41a9-b8ac-c1310c67b66a</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">Verified</th>

<td>false</td>

</tr>


</table>

* * *


<table  border="1">

<tr>
<th  width="200" align="right" bgcolor="lightgray">Partition Key</th>

<td>fabrikam1</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">Row Key</th>

<td>mailinglist</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">Description</th>

<td>Offres d'emploi Fabrikam Engineering</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">FromEmailAddress</th>

<td>donotreply@fabrikam.com</td>

</tr>


</table>

* * *


<table  border="1">

<tr>
<th  width="200" align="right" bgcolor="lightgray">Partition Key</th>

<td>fabrikam1</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">Row Key</th>

<td>applicant1@domain.com</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">SubscriberGUID</th>

<td>76543210-90ed-41a9-b8ac-c1310c67b66a</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">Verified</th>

<td>true</td>

</tr>


</table>

### Table message

La table `message` stocke des informations relatives aux messages dont l'envoi à une liste de diffusion est prévu. Les administrateurs créent et modifient des lignes dans cette table à l'aide de pages Web, et les rôles de travail les utilisent pour transmettre des informations sur chaque message du rôle de travail A au rôle de travail B.

La clé de partition de la table message est la date à laquelle le message doit être envoyé, au format aaaa-mm-jj. Ceci permet d'optimiser la table pour la requête qui est exécutée le plus fréquemment sur cette table, qui sélectionne les lignes qui ont une date `ScheduledDate` définie sur aujourd'hui ou avant. Cependant, cela crée un goulot d'étranglement possible, car les tables Azure Storage ont un débit maximal de 500 entités par seconde pour une partition. Pour chaque message envoyé, l'application écrit une ligne dans la table `message`, lit une ligne, puis supprime une ligne. Ainsi, le temps de traitement minimal possible pour 1 000 000 de messages prévus pour une journée est de près de deux heures, quel que soit le nombre de rôles de travail ajoutés pour gérer l'augmentation de la charge de travail.

La clé de ligne de la table `message` peut avoir une des deux valeurs suivantes : la constante « message » plus une clé unique pour le message appelée `MessageRef` ou la valeur `MessageRef` plus l'adresse électronique de l'abonné. Les lignes dont la clé de ligne commence par « message » contiennent des informations relatives au message, telles que la liste de diffusion à laquelle il doit être envoyé et quand il doit être envoyé. Les lignes dont la clé de ligne est constituée de `MessageRef` et de l'adresse électronique contiennent toutes les informations nécessaires à l'envoi d'un message électronique à cette adresse.

En termes de bases de données relationnelles, les lignes dont la clé de ligne commence par « message » sont équivalentes à la table `message`. Les lignes qui contiennent la clé de ligne `MessageRef` plus l'adresse électronique sont équivalentes à une requête de jonction qui contient les informations `mailinglist`, `message` et `subscriber`.

Le tableau qui suit présente les propriétés de ligne des lignes de la table `message` qui comportent des informations sur le message.

<table  border="1">

<tr  bgcolor="lightgray">
<th>Propriété</th>

<th>Type de données</th>

<th>Description</th>

</tr>


<tr>
<td>PartitionKey</td>

<td>String</td>

<td>Date à laquelle le message doit être envoyé, au format aaaa-mm-jj.</td>

</tr>


<tr>
<td>RowKey</td>

<td>String</td>

<td>Constante « message» concaténée avec la valeur <code>MessageRef</code>
. <code>MessageRef</code>
 est une valeur unique créée en récupérant la valeur <code>Ticks</code>
 dans <code>DateTime.Now</code>
 lors de la création de la ligne. <br  />
<br  />
Remarque : les applications multithread et multi-instance traitant des volumes élevés doivent être prêtes à gérer les exceptions RowKey en double lors de l'utilisation de Ticks. Les Ticks ne sont pas forcément uniques.</td>

</tr>


<tr>
<td>ScheduledDate</td>

<td>Date</td>

<td>Date à laquelle le message doit être envoyé. (Identique à <code>PartitionKey</code>
, mais au format Date.)</td>

</tr>


<tr>
<td>SubjectLine</td>

<td>String</td>

<td>Ligne d'objet du message.</td>

</tr>


<tr>
<td>ListName</td>

<td>String</td>

<td>Liste à laquelle ce message est envoyé.</td>

</tr>


<tr>
<td>Status</td>

<td>String</td>

<td><ul>
<li>« Pending » : le rôle de travail A n'a pas encore commencé à créer la file d'attente de messages pour planifier les messages.</li>

<li>« Queuing » : le rôle de travail A a commencé à créer la file d'attente de messages pour planifier les messages.</li>

<li>« Processing » : le rôle de travail A a créé des éléments de travail de file d'attente pour tous les messages de la liste, mais tous les messages n'ont pas encore été envoyés.</li>

<li>« Completed » : le rôle de travail B a terminé le traitement de tous les éléments de travail de file d'attente (tous les messages ont été envoyés). Les lignes terminées sont archivées dans la table <code>messagearchive</code>
, comme expliqué ultérieurement. Nous espérons faire de cette propriété un élément <code>enum</code>
 dans la prochaine version.</li>
</ul>
</td>

</tr>


</table>

Lorsque le rôle de travail A crée un message de file d 'attente pour un message à envoyer à une liste, il crée une ligne de message dans la table `message`. Lorsque le rôle de travail B envoie le message, il déplace la ligne du message dans la table `messagearchive` et met à jour la propriété `EmailSent` sur `true`. Une fois que toutes les lignes de messages d 'un message dont l 'état est Processing ont été archivées, le rôle de travail A définit le statut sur Completed et déplace la ligne
`message` dans la table `messagearchive`.

La grille suivante présente les propriétés des lignes de message dans la table `message`.

<table  border="1">

<tr  bgcolor="lightgray">
<th>Propriété</th>

<th>Type de données</th>

<th>Description</th>

</tr>


<tr>
<td>PartitionKey</td>

<td>String</td>

<td>Date à laquelle le message doit être envoyé, au format aaaa-mm-jj.</td>

</tr>


<tr>
<td>RowKey</td>

<td>String</td>

<td>Valeur <code>MessageRef</code>
 et adresse électronique de destination de la ligne <code>subscriber</code>
 de la table <code>mailinglist</code>
.
</td>

</tr>


<tr>
<td>MessageRef</td>

<td>Long</td>

<td>Identique au composant <code>MessageRef</code>
 de <code>RowKey</code>
.</td>

</tr>


<tr>
<td>ScheduledDate</td>

<td>Date</td>

<td>Date prévue pour la ligne <code>message</code>
 de la table <code>message</code>
. (Identique à <code>PartitionKey</code>
, mais au format Date.)</td>

</tr>


<tr>
<td>SubjectLine</td>

<td>String</td>

<td>Ligne d'objet de la ligne <code>message</code>
 de la table <code>message</code>
.</td>

</tr>


<tr>
<td>ListName</td>

<td>String</td>

<td>Nom de la liste de diffusion de la table <code>mailinglist</code>
.</td>

</tr>


<tr>
<td>From EmailAddress</td>

<td>String</td>

<td>Adresse électronique « De » de la ligne <code>mailinglist</code>
 de la table <code>mailinglist</code>
.</td>

</tr>


<tr>
<td>EmailAddress</td>

<td>String</td>

<td>Adresse électronique de la ligne <code>subscriber</code>
 de la table <code>mailinglist</code>
.</td>

</tr>


<tr>
<td>SubscriberGUID</td>

<td>String</td>

<td>GUID d'abonné de la ligne <code>subscriber</code>
 de la table <code>mailinglist</code>
.</td>

</tr>


<tr>
<td>EmailSent</td>

<td>Boolean</td>

<td>False signifie que le message n'a pas encore été envoyé. True signifie que le message a été envoyé.</td>

</tr>


</table>

Ces lignes contiennent des données redondantes, ce que vous éviteriez normalement dans une base de données relationnelle. Mais dans ce cas, vous échangez les inconvénients des données redondantes contre une meilleure efficacité de traitement et une plus grande extensibilité. Comme toutes les données nécessaires à un message sont présentes dans une de ces lignes, le rôle de travail B ne doit lire qu'une seule ligne pour envoyer le message lorsqu'il extrait un élément de travail de la file d'attente.

Vous vous demandez peut-être d 'où vient le corps du message. Ces lignes ne comportent pas de référence d 'objet blob pour les fichiers qui contiennent le corps du message, car cette valeur est dérivée de la valeur `MessageRef`. Par exemple, si `MessageRef` est 634852858215726983, les objets blob sont appelés 634852858215726983.htm et 634852858215726983.txt.

La liste qui suit présente un exemple de ce à quoi peuvent ressembler les données dans la table.

<table  border="1">

<tr>
<th  width="200" align="right" bgcolor="lightgray">Partition Key</th>

<td>2012-10-15</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">Row Key</th>

<td>message634852858215726983</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">MessageRef</th>

<td>634852858215726983</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">ScheduledDate</th>

<td>2012-10-15</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">SubjectLine</th>

<td>Nouvelle série de cours</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">ListName</th>

<td>contoso1</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">Status</th>

<td>Processing</td>

</tr>


</table>

* * *


<table  border="1">

<tr>
<th  width="200" align="right" bgcolor="lightgray">Partition Key</th>

<td>2012-10-15</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">Row Key</th>

<td>634852858215726983student1@contoso.edu</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">MessageRef</th>

<td>634852858215726983</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">ScheduledDate</th>

<td>2012-10-15</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">SubjectLine</th>

<td>Nouvelle série de cours</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">ListName</th>

<td>contoso1</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">FromEmailAddress</th>

<td>donotreply@contoso.edu</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">EmailAddress</th>

<td>student1@contoso.edu</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">SubscriberGUID</th>

<td>76543210-90ed-41a9-b8ac-c1310c67b66a</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">EmailSent</th>

<td>true</td>

</tr>


</table>

* * *


<table  border="1">

<tr>
<th  width="200" align="right" bgcolor="lightgray">Partition Key</th>

<td>2012-10-15</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">Row Key</th>

<td>634852858215726983student2@contoso.edu</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">MessageRef</th>

<td>634852858215726983</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">ScheduledDate</th>

<td>2012-10-15</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">SubjectLine</th>

<td>Nouvelle série de cours</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">ListName</th>

<td>contoso1</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">FromEmailAddress</th>

<td>donotreply@contoso.edu</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">EmailAddress</th>

<td>student2@contoso.edu</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">SubscriberGUID</th>

<td>12345678-90ed-41a9-b8ac-c1310c679876</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">EmailSent</th>

<td>true</td>

</tr>


</table>

* * *


<table  border="1">

<tr>
<th  width="200" align="right" bgcolor="lightgray">Partition Key</th>

<td>2012-11-15</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">Row Key</th>

<td>message124852858215726999</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">MessageRef</th>

<td>124852858215726999</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">ScheduledDate</th>

<td>2012-11-15</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">SubjectLine</th>

<td>Nouvelles offres d'emploi</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">ListName</th>

<td>fabrikam</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">Status</th>

<td>Pending</td>

</tr>


</table>

### table messagearchive

Une stratégie permettant de s 'assurer que les requêtes sont exécutées de façon efficace, en particulier si vous devez effectuer des recherches sur les champs autres que `PartitionKey` et `RowKey`, est de limiter la taille de la table. La requête dans le rôle de travail A qui vérifie si tous les messages ont bien été envoyés doit rechercher les lignes de messages de la table `message` dont la valeur de `EmailSent` est définie sur false. La valeur `EmailSent` ne se trouve pas dans PartitionKey ni RowKey, donc la requête ne serait pas efficace pour un message avec un grand nombre de lignes de message. Ainsi, l 'application déplace les lignes de message vers la table `messagearchive` au fur et à mesure de l 'envoi des messages. Ainsi, la requête permettant de vérifier si tous les messages ont bien été envoyés doit simplement porter sur
`PartitionKey` et `RowKey` dans la table des messages, car si elle
trouve des lignes de messages pour un message, cela signifie que des messages n 'ont pas été envoyés et n 'ont pas pu être marqués comme
`Complete`.

Le schéma des lignes de la table `messagearchive` est identique à celui de la table `message`. En fonction de ce que vous voulez faire de ces données archivées, vous pouvez en limiter la taille et le coût en réduisant le nombre de propriétés stockées pour chaque ligne et en supprimant les lignes après une certaine date.

Files d'attenteFiles d'attente Azure
------------------------------------

Les files d'attente Azure facilitent la communication entre les niveaux de cette application multiniveau et entre les rôles de travail dans le composant principal. Les files d'attente sont utilisées pour la communication entre le rôle de travail A et le rôle de travail B afin de permettre l'extensibilité de l'application. Le rôle de travail A peut créer une ligne dans la table Message pour chaque message, et le rôle de travail B peut rechercher dans la table les lignes représentant les messages qui n'ont pas été envoyés, mais vous ne seriez pas alors en mesure d'ajouter d'autres instances du rôle de travail B pour mieux répartir le travail. Le problème de l'utilisation de lignes de table pour coordonner le travail entre le rôle de travail A et le rôle de travail B est que vous n'avez aucun moyen de vous assurer qu'une seule instance du rôle de travail choisit une ligne spécifique pour la traiter. Les files d'attente vous offrent cette possibilité. Lorsqu'une instance de rôle de travail récupère un élément de travail dans la file d'attente, le service de file d'attente s'assure qu'aucune instance de rôle de travail ne peut récupérer le même élément de travail. Cette fonction exclusive des files d'attente Azure facilite le partage de la charge de travail entre plusieurs instances d'un rôle de travail.

Azure comporte également le service de file d'attente Service Bus. Pour plus d'informations sur les files d'attente Azure Storage et les files d'attente Service Bus, consultez les ressources répertoriées à la fin du [dernier didacticiel de cette série](/en-us/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/).

L 'application Azure Email Service utilise deux files d 'attente, appelées `AzureMailQueue` et `AzureMailSubscribeQueue`.

### AzureMailQueue

La file d 'attente `AzureMailQueue` coordonne l 'envoi des courriers électroniques aux listes de diffusion. Le rôle de travail A place un élément de travail dans la file d 'attente pour chaque message à envoyer et le rôle de travail B prend un élément de travail dans la file d 'attente et envoie le message.

L 'élément de travail de file d 'attente contient une chaîne délimitée par des virgules qui comprend la date prévue du message (clé de partition de la table `message`) et les valeurs `MessageRef` et `EmailAddress` (clé de ligne de la table `message`), plus un indicateur qui précise si l 'élément a été créé après l 'échec et le redémarrage du rôle de travail, par exemple :

      2012-10-15,634852858215726983,student1@contoso.edu,0

Le rôle de travail B utilise ces valeurs pour rechercher dans la table `message` la ligne qui contient toutes les informations nécessaires à l 'envoi du message. Si l 'indicateur de redémarrage indique un redémarrage, le rôle de travail B s 'assure avant de l 'envoyer que le message n 'a pas déjà été envoyé.

En cas d 'augmentation brutale du trafic, le service cloud peut être reconfiguré de façon à ce que plusieurs instances du rôle de travail B soient instanciées. Chacune de ces instances peut récupérer indépendamment des éléments de travail dans la file d 'attente.

### AzureMailSubscribeQueue

La file d 'attente `AzureMailSubscribeQueue` coordonne l 'envoi des messages de confirmation d 'abonnement. En réponse à un appel de la méthode du service, cette dernière place un élément de travail dans la file d 'attente. Le rôle de travail B récupère l 'élément de travail dans la file d 'attente et envoie le message de confirmation d 'abonnement.

Les éléments de travail de file d 'attente contiennent le GUID de l 'abonné. Cette valeur identifie une adresse électronique de façon unique, ainsi que la liste à laquelle elle doit être abonnée. C 'est tout ce dont le rôle de travail B a besoin pour envoyer un message de confirmation. Comme expliqué précédemment, ceci nécessite une requête sur un champ qui ne se trouve pas dans `PartitionKey` ni `RowKey`, ce qui n 'est pas efficace. Pour rendre l 'application plus extensible, la table `mailinglist` doit être restructurée pour inclure le GUID d 'abonné dans `RowKey`.

<h2><a name="datadiagram"></a><span  class="short-header">Schéma des données</span>Schéma des données Azure Email Service</h2>


Le schéma qui suit présente les tables et les files d 'attente, ainsi que leurs relations.

![Schéma des données de l'application Azure Email Service](./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-datadiagram.png)

<h2><a name="blobs"></a><span  class="short-header">Objets blob</span>Objets blob Azure</h2>


Les objets blob sont des objets binaires de grande taille. Le service d 'objets blob Azure permet de charger et de stocker des fichiers dans le cloud. Pour plus d 'informations sur les objets blob Azure, consultez les ressources répertoriées à la fin du [dernier didacticiel de cette série](/en-us/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/).

Les administrateurs Azure Mail Service placent le corps du message au format HTML dans un fichier *.htm* et au format texte dans un fichier
*.txt*. Lorsqu 'ils planifient un message, ils chargent ces fichiers dans la page Web **Create Message**, et le contrôleur ASP.NET MVC de cette page stocke les fichiers chargés dans un objet blob Azure.

Les objets blob sont stockés dans des conteneurs d 'objets blob, un peu comme les fichiers dans des dossiers. L 'application Azure Mail Service utilise un seul conteneur d 'objets blob appelé
**azuremailblobcontainer**. Le nom des objets blob du conteneur est
dérivé en concaténant la valeur MessageRef avec l 'extension de fichier, par exemple : 634852858215726983.htm et 634852858215726983.txt.

Comme les messages au format HTML ou texte brut sont essentiellement composés de chaînes, nous aurions pu concevoir l 'application pour qu 'elle stocke le corps du message dans les propriétés de chaîne dans la table `Message`, plutôt que dans des objets blob. Mais la taille des propriétés dans une ligne de table est limitée à 64 Ko et l 'utilisation d 'un objet blob permet de contourner cette limitation pour le corps des messages. (64 Ko correspond à la taille totale maximale de la propriété. En prenant en compte la surcharge d 'encodage, la taille maximale de la chaîne que vous pouvez stocker dans une propriété est en réalité plus proche de 48 Ko.)

<h2><a name="wawsvswacs"></a><span  class="short-header">Azure Cloud Services et Sites Web Azure</span>Azure Cloud Services et Sites Web Azure</h2>


Lorsque vous téléchargez Azure Email Service, l 'application est configurée de façon à ce que le composant principal et le composant frontal s 'exécutent dans un seul service cloud Azure.

![Présentation de l'architecture de
l'application](./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-architecture-overview.png)

Une autre architecture possible est d 'exécuter le composant frontal dans un site Web Azure.

![Autre architecture d'application](./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-alternative-architecture.png)

Le fait de conserver tous les composants dans un service cloud simplifie la configuration et le déploiement. Si vous créez l 'application avec le composant frontal ASP.NET MVC dans un site Web Azure, vous avez deux déploiements : un dans Sites Web Azure et un dans Azure Cloud Services. De plus, les rôles Web Azure Cloud Services offrent les fonctions suivantes, qui ne sont pas disponibles dans Sites Web Azure :

-   Prise en charge des certificats personnalisés et des certificats génériques.
-   Contrôle complet de la configuration du gestionnaire des services Internet. De nombreuses fonctionnalités du gestionnaire des services Internet ne peuvent pas être activées dans Sites Web Azure. Avec les rôles Web Azure, vous pouvez définir une commande de démarrage qui exécute le programme [AppCmd](http://www.iis.net/learn/get-started/getting-started-with-iis/getting-started-with-appcmdexe "appCmd") pour modifier les paramètres du gestionnaire des services Internet qui ne peuvent pas être configurés dans votre fichier *Web.config*. Pour plus d'informations, consultez les pages [Configuration des composants du gestionnaire des services Internet dans Azure](http://msdn.microsoft.com/en-us/library/windowsazure/gg433059.aspx) et [Empêcher des adresses IP spécifiques d'accéder à un rôle Web](http://msdn.microsoft.com/en-us/library/windowsazure/jj154098.aspx).
-   Prise en charge de la mise à l'échelle automatique de votre application Web à l'aide du [bloc applicatif de mise à l'échelle automatique](/en-us/develop/net/how-to-guides/autoscaling/).
-   Possibilité d'exécuter des scripts de démarrage élevés, de modifier les paramètres du Registre, d'installer des compteurs de performances, etc.
-   Isolation du réseau pour utilisation avec [Azure Connect](http://msdn.microsoft.com/en-us/library/windowsazure/gg433122.aspx) et [Réseau virtuel Azure](http://msdn.microsoft.com/en-us/library/windowsazure/jj156007.aspx).
-   Accès Bureau à distance pour le débogage et le diagnostic avancé.
-   Mises à niveau propagées avec [Échange d'adresses IP virtuelles](http://msdn.microsoft.com/en-us/library/windowsazure/ee517253.aspx "échange d'adresses IP virtuelles"). Cette fonction échange le contenu de votre déploiement intermédiaire et de votre déploiement de production.

Cette architecture alternative peut offrir certains avantages en termes de coûts, car un site Web Azure peut être moins cher pour une capacité similaire par rapport à un rôle Web s'exécutant dans un service cloud. D'autres didacticiels de la série expliquent en détail les différences de mise en œuvre entre les deux architectures.

Pour plus d'informations sur le choix entre Sites Web Azure et Azure Cloud Services, consultez la page [Modèles d'exécution Azure](http://www.windowsazure.com/en-us/manage/windows/fundamentals/compute/).

CoûtCoût
--------

Cette section offre un aperçu des coûts d'exécution de l'exemple d'application dans Azure, selon les tarifs en vigueur au moment de la publication de ce didacticiel, en décembre 2012. Avant de prendre des décisions sur les coûts, consultez les tarifs dans les pages suivantes :

-   [Calcul des coûts Azure](http://www.windowsazure.com/en-us/pricing/calculator/)
-   [Azure SendGrid](http://sendgrid.com/windowsazure.html)

Les coûts dépendent du nombre d'instances de rôle de travail et de rôle Web que vous décidez d'utiliser. Afin de pouvoir répondre aux critères du [contrat de niveau de service Azure Cloud Service 99,95 %](https://www.windowsazure.com/en-us/support/legal/sla/ "SLA"), vous devez déployer au moins deux instances de chaque rôle. Une des raisons pour lesquelles vous devez exécuter au moins deux instances de rôle est que les machines virtuelles qui exécutent votre application doivent être redémarrées environ deux fois par mois en raison de mises à niveau du système d'exploitation. (Pour plus d'informations sur les mises à jour du système d'exploitation, consultez la page [Redémarrages d'instances de rôle pour cause de mise à jour du système d'exploitation](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx).)

Le travail effectué par les deux rôles de travail dans notre exemple n'est pas critique, et n'a donc pas besoin du niveau de service à 99,5 %. Ainsi, il est possible de n'utiliser qu'une seule instance de rôle de travail, tant que celle-ci peut absorber la charge de travail. L'instance de rôle Web est un élément sensible, c'est-à-dire que les utilisateurs s'attendent à ce que le site Web soit toujours en ligne. Une application de production doit donc comporter au moins deux instances du rôle Web.

Le tableau qui suit présente les coûts de l'architecture par défaut pour l'exemple d'application Azure Email Service, selon une charge de travail minimale. Les coûts présentés sont basés sur une machine virtuelle (partagée) de très petite taille. Lorsque vous créez un projet cloud Visual Studio, la taille de machine virtuelle par défaut est petite, ce qui représente un coût six fois plus élevé que la très petite taille.

<table  border="1">

<tr  bgcolor="lightgray">
<th>Composant ou service</th>

<th>Tarif</th>

<th>Coût par mois</th>

</tr>


<tr>
<td>Rôle Web</td>

<td>2 instances à 0,02 $/heure pour les très petites instances</td>

<td>29,00 $</td>

</tr>


<tr>
<td>Rôle de travail A (planifie l'envoi des messages)</td>

<td>1 instance à 0,02 $/heure pour une très petite instance</td>

<td>14,50 $
</td>

</tr>


<tr>
<td>Rôle de travail B (envoie les messages)</td>

<td>1 instance à 0,02 $/heure pour une très petite instance</td>

<td>14,50 $</td>

</tr>


<tr>
<td>Transactions de stockage Azure</td>

<td>1 million de transactions par mois à 0,10 $/million (Chaque requête compte comme une transaction ; le rôle de travail A lance continuellement des requêtes sur les tables à la recherche de messages à envoyer. L'application est également configurée pour écrire les données de diagnostic dans Azure Storage, et chaque écriture compte comme une transaction.)</td>

<td>0,10 $</td>

</tr>


<tr>
<td>Stockage redondant local Azure</td>

<td>2,33 $ pour 25 Go (comprend le stockage pour les tables d'applications et les données de diagnostic.)</td>

<td>2,33 $</td>

</tr>


<tr>
<td>Bande passante</td>

<td>5 Go décroissants gratuits</td>

<td>Gratuit</td>

</tr>


<tr>
<td>SendGrid</td>

<td>Les clients Azure peuvent envoyer gratuitement 25 000 courriers électroniques par mois</td>

<td>Gratuit</td>

</tr>


<tr>
<td  colspan="2">Total</td>

<td>60,43 $</td>

</tr>


</table>

Comme vous pouvez le constater, les instances de rôle sont un élément majeur du coût total. Elles génèrent des frais même si elles sont arrêtées. Vous devez supprimer une instance de rôle pour qu'elle n'entraîne plus de frais. Une façon de réaliser des économies serait de déplacer tout le code du rôle de travail A et du rôle de travail B dans un seul rôle de travail. Pour ces didacticiels, nous avons délibérément choisi d'utiliser deux instances de rôle de travail afin de simplifier l'extensibilité. Le travail qu'effectue le rôle de travail B est coordonné par le service de file d'attente Azure, ce qui signifie que vous pouvez étendre le rôle de travail B en augmentant simplement le nombre d'instances de rôle. (Le rôle de travail B est le facteur limitant en cas de charge de travail élevée.) Le travail effectué par le rôle de travail A n'étant pas coordonné par les files d'attente, vous ne pouvez pas exécuter plusieurs instances du rôle de travail A. Si les deux rôles de travail sont combinés et que vous voulez permettre l'extensibilité, vous devez mettre en place un mécanisme pour vous assurer que les tâches du rôle de travail A ne s'exécutent que dans une seule instance. (Ce mécanisme est fourni par [CloudFx](http://nuget.org/packages/Microsoft.Experience.CloudFx "CloudFX"). Consultez également l'[exemple WorkerRole.cs](http://code.msdn.microsoft.com/windowsazure/CloudFx-Samples-60c3a852/sourcecode?fileId=57087&pathId=528472169).)

Il est également possible de déplacer tout le code des deux rôles de travail dans le rôle Web, afin que tout soit exécuté dans ce dernier. Toutefois, l'exécution de tâches en arrière-plan dans ASP.NET n'est pas prise en charge ou considérée comme robuste. Cette architecture complique l'extensibilité. Pour plus d'informations, consultez la page [Dangers de la mise en œuvre de tâches en arrière-plan récurrentes dans ASP.NET](http://haacked.com/archive/2011/10/16/the-dangers-of-implementing-recurring-background-tasks-in-asp-net.aspx). Consultez également les pages [Combinaison d'un rôle de travail et d'un rôle Web dans Azure](http://www.31a2ba2a-b718-11dc-8314-0800200c9a66.com/2010/12/how-to-combine-worker-and-web-role-in.html) et [Combinaison de plusieurs rôles de travail Azure dans un rôle Web Azure](http://www.31a2ba2a-b718-11dc-8314-0800200c9a66.com/2012/02/combining-multiple-azure-worker-roles.html).

Une autre possibilité d'architecture pour réduire les coûts est d'utiliser le [bloc applicatif de mise à l'échelle automatique](/en-us/develop/net/how-to-guides/autoscaling/) pour déployer automatiquement les rôles de travail uniquement lors de périodes définies et les supprimer une fois le travail terminé. Pour plus d'informations sur la mise à l'échelle automatique, consultez les liens à la fin du [dernier didacticiel de cette série](/en-us/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/).

À l'avenir, Azure pourrait proposer un mécanisme de notification pour les redémarrages programmés, ce qui vous permettrait de ne lancer une instance de rôle Web supplémentaire que pour la période de redémarrage prévue. Vous ne seriez pas conforme au contrat SLA 99,95 %, mais vous pourriez réduire vos coûts de moitié environ et assurer la disponibilité de votre application Web pendant la période de redémarrage.

Authentification et autorisationAuthentification et autorisation
----------------------------------------------------------------

Dans une application de production, vous mettez en place un mécanisme d'authentification et d'autorisation comme le système d'abonnement ASP.NET pour le composant frontal Web ASP.NET MVC, avec la méthode de service de l'API Web ASP.NET. Il existe également d'autres possibilités, comme l'utilisation d'un secret partagé, pour assurer la sécurité de la méthode de service de l'API Web. Les fonctions d'authentification et d'autorisation ont été omises dans cet exemple d'application pour qu'il reste simple à configurer et à déployer. (Le deuxième didacticiel de la série présente comment mettre en œuvre les restrictions d'adresse IP afin que les personnes non autorisées ne puissent pas utiliser l'application lorsque vous la déployez dans le cloud.)

Pour plus d'informations sur la mise en œuvre de l'authentification et de l'autorisation dans un projet Web ASP.NET MVC, consultez les ressources suivantes :

-   [Authentification et autorisation dans l'API Web ASP.NET](http://www.asp.net/web-api/overview/security/authentication-and-authorization/authentication-and-authorization-in-aspnet-web-api)
-   [Music Store Partie 7 : Abonnement et autorisation](http://www.asp.net/mvc/tutorials/mvc-music-store/mvc-music-store-part-7)

**Remarque** : nous avions prévu d'inclure un mécanisme permettant d'assurer la sécurité de la méthode de service de l'API Web à l'aide d'un secret partagé, mais cela n'a pas été terminé à temps pour la version initiale. Le troisième didacticiel ne montre donc pas comment concevoir le contrôleur d'API Web pour le processus d'abonnement. Nous espérons ajouter des instructions pour la mise en œuvre d'un processus d'inscription sécurisé dans la prochaine version de ce didacticiel. En attendant, vous pouvez tester l'application à l'aide des pages Web administrateur pour abonner des adresses électroniques à des listes.

Étapes suivantesÉtapes suivantes
--------------------------------

Dans le [prochain didacticiel](/en-us/develop/net/tutorials/multi-tier-web-site/2-download-and-run/), vous allez télécharger l'exemple de projet, configurer votre environnement de développement, configurer le projet pour votre environnement et tester le projet en local et dans le cloud. Dans les didacticiels suivants, vous verrez comment créer entièrement le projet.

Pour obtenir des liens sur les ressources supplémentaires pour travailler avec les tables, les files d'attente et les objets blob Azure Storage, consultez la fin du [dernier didacticiel de cette série](/en-us/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/).

[Didacticiel 2](/en-us/develop/net/tutorials/multi-tier-web-site/2-download-and-run/)


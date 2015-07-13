<properties
	pageTitle="En savoir plus sur les fonctionnalités dans les éditions BizTalk Services | Azure"
	description="Comparer les fonctions des éditions BizTalk Services : Gratuite, Développeur, De base, Standard et Premium. MABS, WABS"
	services="biztalk-services"
	documentationCenter=""
	authors="MandiOhlinger"
	manager="dwrede"
	editor="cgronlun"/>

<tags
	ms.service="biztalk-services"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article" 
	ms.date="03/03/2015"
	ms.author="mandia"/>


# Tableau comparatif des éditions de BizTalk Services

Azure BizTalk Services offre plusieurs éditions au choix. Utilisez cette rubrique pour déterminer quelle édition est adaptée à votre scénario et à vos besoins professionnels.


## Description des éditions

**GRATUITE (Évaluation)**

Création et gestion des connexions hybrides. Une connexion hybride offre un moyen simple de connecter un site web Azure à un système local, tel que SQL Server.

**DÉVELOPPEUR**

cette édition permet le traitement des messages des connexions hybrides, EAI et EDI grâce à un portail de gestion de partenaire commercial simple à utiliser, une prise en charge des schémas EDI répandus et des capacités de traitement EDI enrichies sur X12 et AS2. Permet également de créer des services de connexion au cloud pour les scénarios IAE les plus répandus, avec les protocoles HTTP/S, REST, FTP, WCF et SFTP pour lire et écrire des messages. Les adaptateurs SAP, Oracle eBusiness, Oracle DB, Siebel et SQL Server prêts à l'emploi de cette édition permettent d'utiliser la connectivité vers les systèmes métiers locaux. Utilisez un environnement conçu pour les développeurs, doté d'outils Visual Studio, facilitant le développement et le déploiement. Cette édition est limitée à des fins de développement et de test uniquement, sans contrat de niveau de service (SLA).

**DE BASE**

cette édition comprend la plupart des fonctionnalités de l'édition Développeur avec une amélioration de la capacité des connexions hybrides, des ponts EAI, des contrats EDI et des connexions Pack adaptateurs BizTalk. Elle offre également une haute disponibilité et la possibilité d'effectuer une mise à l'échelle avec un contrat de niveau de service (SLA).

**STANDARD**

cette édition comprend toutes les fonctionnalités de l'édition De base avec une amélioration de la capacité des connexions hybrides, des ponts EAI, des contrats EDI et des connexions Pack adaptateurs BizTalk. Elle offre également une haute disponibilité et la possibilité d'effectuer une mise à l'échelle avec un contrat de niveau de service (SLA).

**PREMIUM**

cette édition comprend toutes les fonctionnalités de l'édition Standard avec une amélioration de la capacité des connexions hybrides, des ponts EAI, des contrats EDI et des connexions Pack adaptateurs BizTalk. Elle offre également des fonctionnalités d'archivage, une haute disponibilité et la possibilité d'effectuer une mise à l'échelle avec un contrat de niveau de service (SLA).


## tableau comparatif des éditions
Le tableau suivant répertorie les différences :

<table border="1">
<tr bgcolor="FAF9F9">
        <th></th>
        <th>Gratuite (Évaluation)</th>
        <th>Développeur</th>
        <th>De base</th>
        <th>Standard</th>
        <th>Premium</th>
</tr>

<tr>
<td><strong>Prix de départ</strong></td>
<td colspan="5"><a HREF="http://go.microsoft.com/fwlink/p/?LinkID=304011">Tarification Azure BizTalk Services</a> <br/><br/> <a HREF="http://azure.microsoft.com/pricing/calculator/?scenario=full">Calcul des coûts Azure</a></td>
</tr>
<tr>
<td><strong>Configuration minimale par défaut</strong></td>
<td>1&#160;unité Gratuite</td>
<td>1&#160;unité Développeur</td>
<td>1&#160;unité De base</td>
<td>1&#160;unité Standard</td>
<td>1&#160;unité Premium</td>
</tr>
<tr>
<td><strong>Mise à l'échelle</strong></td>
<td>Pas de mise à l'échelle</td>
<td>Pas de mise à l'échelle</td>
<td>Oui, dans les incréments d'1 unité De base</td>
<td>Oui, dans les incréments d'1 unité Standard</td>
<td>Oui, dans les incréments d'1 unité Premium</td>
</tr>
<tr>
<td><strong>Montée en charge maximale autorisée</strong></td>
<td>Pas de mise à l'échelle</td>
<td>Pas de mise à l'échelle</td>
<td>Jusqu'à 8&#160;unités</td>
<td>Jusqu'à 8&#160;unités</td>
<td>Jusqu'à 8&#160;unités</td>
</tr>
<tr>
<td><strong>Ponts IAE par unité</strong></td>
<td>Non inclus</td>
<td>25</td>
<td>25</td>
<td>125</td>
<td>500</td>
</tr>
<tr>
<td><strong>EDI, AS2</strong>
<br/><br/>
Inclut les contrats TPM</td>
<td>Non inclus</td>
<td>Inclus. 10&#160;contrats par unité.</td>
<td>Inclus. 50&#160;contrats par unité.</td>
<td>Inclus. 250&#160;contrats par unité.</td>
<td>Inclus. 1000&#160;contrats par unité.</td>
</tr>
<tr>
<td><strong>Connexions hybrides par unité</strong></td>
<td>5</td>
<td>5</td>
<td>10</td>
<td>50</td>
<td>100</td>
</tr>
<tr>
<td><strong>Transfert de données des connexions hybrides (Go) par unité</strong></td>
<td>5</td>
<td>5</td>
<td>50</td>
<td>250</td>
<td>500</td>
</tr>
<tr>
<td><strong>Connexions du service d'adaptateurs BizTalk aux systèmes métiers locaux</strong></td>
<td>Non inclus</td>
<td>1&#160;connexion</td>
<td>2&#160;connexions</td>
<td>5&#160;connexions</td>
<td>25&#160;connexions</td>
</tr>
<tr>
<td align="left"><strong>Protocoles/systèmes pris en charge&#160;:</strong>
<ul>
<li>HTTP</li>
<li>HTTPS</li>
<li>FTP</li>
<li>SFTP</li>
<li>WCF</li>
<li>Service Bus (SB)</li>
<li>Objets blob Azure</li>
<li>API REST</li>
</ul>
</td>
<td>Non inclus</td>
<td>Inclus</td>
<td>Inclus</td>
<td>Inclus</td>
<td>Inclus</td>
</tr>
<tr>
<td><strong>Haute disponibilité</strong>
<br/><br/>
Pour le contrat de niveau de service (SLA), consultez <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=304011">Tarification – BizTalk Services</a>.
</td>
<td>Non inclus</td>
<td>Non inclus</td>
<td>Inclus</td>
<td>Inclus</td>
<td>Inclus</td>
</tr>
<tr>
<td><strong>Sauvegarde et restauration</strong></td>
<td>Non inclus</td>
<td>Inclus</td>
<td>Inclus</td>
<td>Inclus</td>
<td>Inclus</td>
</tr>
<tr>
<td><strong>Suivi</strong></td>
<td>Non inclus</td>
<td>Inclus</td>
<td>Inclus</td>
<td>Inclus</td>
<td>Inclus</td>
</tr>
<tr>
<td><strong>Archivage</strong><br/><br/>
Inclut NRR et le téléchargement des messages suivis</td>
<td>Non inclus</td>
<td>Inclus</td>
<td>Non inclus</td>
<td>Non inclus</td>
<td>Inclus</td>
</tr>
<tr>
<td><strong>Utilisation de code personnalisé</strong></td>
<td>Non inclus</td>
<td>Inclus</td>
<td>Inclus</td>
<td>Inclus</td>
<td>Inclus</td>
</tr>
<tr>
<td><strong>Utilisation des transformations, notamment la personnalisation XSLT</strong></td>
<td>Non inclus</td>
<td>Inclus</td>
<td>Inclus</td>
<td>Inclus</td>
<td>Inclus</td>
</tr>
</table>

**Remarque** <br/>Pour résister aux risques de pannes, la haute disponibilité requiert d'avoir plusieurs machines virtuelles dans une seule unité BizTalk.


## FAQ

#### Qu'est-ce qu'une unité BizTalk ?
Une « unité » correspond au niveau atomique d'un déploiement Azure BizTalk Services. Chaque édition est livrée avec une unité ayant différentes capacités de calcul et de mémoire. Par exemple, une unité De base comprend davantage d'ordinateurs qu'une unité Développeur, une unité Standard dispose d'une plus grande capacité de calcul qu'une unité De base, etc. Lorsque vous mettez à l'échelle un service BizTalk, cette mise à l'échelle s'effectue en termes d'unités.

#### Quelle est la différence entre BizTalk Services et la machine virtuelle Azure BizTalk ?
BizTalk Services fournit une véritable architecture PaaS (Platform-as-a-Service) permettant de développer des solutions d'intégration dans le cloud. Grâce au modèle PaaS, vous pouvez vous concentrer uniquement sur la logique de l'application et confier toute la gestion de l'infrastructure à Microsoft, en profitant des avantages suivants :

- Vous n'avez plus besoin de gérer ou de mettre à jour vos machines virtuelles
- Microsoft s'occupe de maintenir la disponibilité
- Vous contrôlez la mise à l'échelle, en demandant simplement un accroissement ou une réduction des capacités via le portail de gestion Azure

BizTalk Server sur les machines virtuelles Azure fournit une architecture IaaS (Infrastructure-as-a-Service). Vous pouvez créer des machines virtuelles et les configurer exactement comme votre environnement local, ce qui facilite l'exécution de vos applications existantes dans le cloud, en vous épargnant toute modification du code. Avec IaaS, vous êtes toujours responsable de la configuration de vos machines virtuelles, de leur gestion (par exemple, l'installation de logiciels et l'application de correctifs au système d'exploitation) et de la conception des applications pour la haute disponibilité.

Si vous voulez développer de nouvelles solutions d'intégration pour faciliter la gestion de votre architecture, choisissez BizTalk Services. Si vous voulez faire migrer rapidement vos solutions BizTalk ou si vous recherchez un environnement à la demande pour développer et tester vos applications BizTalk Server, choisissez BizTalk Server sur une machine virtuelle Azure.

#### Quelle est la différence entre le service d'adaptateur BizTalk et les connexions hybrides?
Le service d'adaptateur BizTalk est utilisé par un service Azure BizTalk. Le service d'adaptateur BizTalk utilise le Pack d’adaptateurs BizTalk pour se connecter à un système métier local. Une connexion hybride offre un moyen facile et pratique de connecter des applications Azure, comme des sites web et Mobile Services, à une ressource locale.

#### Qu’est-ce que le « Transfert de données des connexions hybrides (Go) par unité » ? Est-ce qu’il s’agit d’un transfert par minute/heure/jour/semaine/mois ? Que se passe-t-il lorsque la limite est atteinte ?

Le coût de connexion hybride par unité dépend de l'édition de BizTalk Services. En bref, les coûts dépendent de la quantité de données que vous transférez. Par exemple, un transfert quotidien de 10 Go de données est moins cher qu’un transfert quotidien de 100 Go. Utilisez le [Calcul des coûts](http://azure.microsoft.com/pricing/calculator/?scenario=full) de BizTalk Services déterminer les coûts spécifiques. En règle générale, les limites sont appliquées quotidiennement. Si vous dépassez la limite, tout surcoût est facturé au prix de 1 $ par Go.

#### Lorsque je crée un contrat dans BizTalk Services, pourquoi y-a-t-il deux ponts au lieu d'un ?

Chaque contrat comprend deux ponts différents : un pont de communication pour l'envoi, et un autre pour la réception.

####  Que se passe-t-il si j'atteins le nombre de ponts ou de contrats maximum ?

Vous ne pourrez plus déployer d'autres ponts ou créer d'autres contrats. Pour cela, vous devrez alors procéder à une mise à l'échelle en augmentant votre nombre d'unités du service BizTalk, ou effectuer une mise à niveau vers une édition supérieure.

#### Comment migrer d'un niveau de BizTalk Services à un autre ?

Utilisez le flux de sauvegarde et de restauration pour migrer d'un niveau à un autre. Seuls certains chemins de migration sont pris en charge. Pour plus d'informations sur les chemins de migration pris en charge, consultez la page [Sauvegarde et restauration de BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=329873).

#### Un service d'adaptateur BizTalk est-il inclus dans le service ? Comment recevoir le logiciel ?

Oui, le service d'adaptateur BizTalk livré avec le Pack adaptateurs BizTalk vous est fourni lorsque vous [téléchargez](http://www.microsoft.com/download/details.aspx?id=39087) le Kit de développement logiciel (SDK) Azure BizTalk Services.

## Suivant

Pour créer Azure BizTalk Services dans le portail de gestion Azure, accédez à [BizTalk Services : approvisionnement à l’aide du portail de gestion Azure](http://go.microsoft.com/fwlink/p/?LinkID=302280). Pour commencer à créer des applications, consultez la page [Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=235197).

## Voir aussi
- [Approvisionnement de BizTalk Services avec le portail de gestion Azure](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
- [Tableau comparatif des états d'approvisionnement BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
- [Onglets Tableau de bord, Surveiller et Mettre à l'échelle dans BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
- [Sauvegarde et restauration de BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
- [Limitation BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>
- [Nom et clé de l'émetteur dans BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=303941)<br/>
- [Utilisation du Kit de développement logiciel (SDK) Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=302335) <br/>
 

<!---HONumber=62-->
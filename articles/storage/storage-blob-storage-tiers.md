<properties
    pageTitle="Stockage chaud Azure des objets blob | Microsoft Azure"
    description="Niveaux de stockage d’objets blob pour un stockage économique des données d’objet, basé sur les modèles d’accès. Le stockage froid Azure utile est optimisé pour les données moins fréquemment utilisées."
    services="storage"
    documentationCenter=""
    authors="sribhat-msft"
    manager=""
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="05/09/2016"
    ms.author="sribhat"/>


# Stockage des objets blob Azure : niveaux de stockage chauds et froids

## Vue d'ensemble

Le stockage Azure offre désormais deux niveaux de stockage d’objets blob (stockage d’objets) afin que vous puissiez stocker vos données de manière plus économique en fonction de leur utilisation. Le **niveau de stockage chaud** Azure est optimisé pour le stockage des données souvent sollicitées. Le **niveau de stockage froid** Azure est optimisé pour le stockage des données rarement sollicitées et durables. Les données du niveau de stockage froid peuvent tolérer une disponibilité légèrement inférieure, mais nécessitent toujours une durabilité élevée, ainsi qu’un temps d’accès et des caractéristiques de débit similaires à ceux des données chaudes. Concernant les données froides, un contrat SLA de disponibilité légèrement inférieure et des coûts d’accès supérieurs sont des compromis acceptables pour des coûts de stockage beaucoup plus faibles.

Aujourd’hui, les données stockées dans le cloud connaissent une croissance exponentielle. Pour gérer les coûts liés à vos besoins de stockage en pleine expansion, il est utile d’organiser vos données selon des attributs tels que la fréquence d’accès et la période de rétention prévue. Les données stockées dans le cloud peuvent être très différentes en termes de mode de génération, de traitement et d’accès au long de leur durée de vie. Certaines données sont activement sollicitées et modifiées tout au long de leur durée de vie. Certaines sont très fréquemment sollicitées au début de leur durée de vie, puis les accès se raréfient considérablement à mesure qu’elles deviennent plus anciennes. D’autres sont inactives dans le cloud dès le départ et sont peu, voire pas sollicitées une fois stockées.

Chacun des scénarios d’accès aux données décrits ci-dessus peut bénéficier des avantages d’un niveau de stockage différencié, gage d’optimisation pour un modèle d’accès particulier. L’introduction des niveaux d’accès au stockage chauds et froids permet désormais au stockage d’objets blob Azure de répondre à ce besoin de niveaux de stockage différenciés aux modèles de tarification distincts.

## Comptes de stockage d’objets blob

Les **comptes de stockage d’objets blob** sont des comptes de stockage spécialisés pour le stockage des données non structurées en tant qu’objets blob dans Azure Storage. Avec les comptes de stockage d’objets blob, vous pouvez maintenant choisir entre les niveaux d’accès froid et chaud pour stocker vos données froides, moins fréquemment sollicitées, moyennant un coût de stockage plus faible, et stocker vos données chaudes, plus fréquemment sollicitées, moyennant un coût d’accès inférieur. Les comptes de stockage d’objets blob sont similaires à vos comptes de stockage à usage général existants et offrent les excellents niveaux de durabilité, disponibilité, évolutivité et performances dont vous bénéficiez aujourd’hui. Ils assurent notamment la cohérence d’API à 100 % pour les objets blob de blocs et d’ajout.

> [AZURE.NOTE] Les comptes de stockage d’objets blob prennent en charge uniquement les objets blob de blocs et d’ajout, mais pas les objets blob de pages.

Les comptes de stockage d’objets blob exposent l’attribut **Access Tier**, qui vous permet de spécifier le niveau de stockage comme **chaud** ou **froid** en fonction des données stockées dans le compte. Si le modèle d’utilisation de vos données est modifié, vous pouvez également basculer entre ces niveaux d’accès à tout moment.

> [AZURE.NOTE] La modification du niveau d’accès peut entraîner des frais supplémentaires. Veuillez consulter la section [Tarification et facturation](storage-blob-storage-tiers.md#pricing-and-billing) pour plus de détails.

Exemples de scénarios d’utilisation du niveau d’accès au stockage chaud :

- Données activement utilisées ou censées être fréquemment sollicitées (accès en lecture et écriture).
- Données conservées pour traitement et migration éventuelle vers le niveau de stockage froid.

Exemples de scénarios d’utilisation du niveau d’accès au stockage froid :

- Sauvegarde, archivage et récupération d’urgence d’ensembles de données.
- Ancien contenu multimédia qui n’est plus consulté fréquemment mais qui est censé être disponible immédiatement lors d’un accès.
- Jeux de données volumineux qui doivent être stockés à moindre coût, sachant que des données supplémentaires sont collectées pour un traitement ultérieur (*par exemple*, un stockage à long terme de données scientifiques ou de données de télémétrie brutes provenant d’une unité de production).
- Données d’origine (brutes) qui doivent être conservées, même après leur traitement sous un format final exploitable (*par exemple,*, des fichiers multimédias bruts après transcodage dans d’autres formats)
- Données de conformité et d’archivage qui doivent être stockées à long terme et qui sont très rarement sollicitées (*par exemple,* séquences vidéo de sécurité, anciens clichés de radiographie ou d’IRM pour des organismes de santé ou enregistrements audio et transcriptions d’appels de clients pour des services financiers).

Pour plus d’informations sur les comptes de stockage, consultez [À propos des comptes de stockage Azure](storage-create-storage-account.md).

Pour les applications qui requièrent uniquement le stockage d’objets blob de blocs ou d’objets blob d’ajout, nous recommandons d’utiliser des comptes de stockage d’objets blob, pour tirer parti du modèle de tarification différencié du stockage hiérarchisé. Toutefois, nous comprenons que cela n’est pas possible dans certains cas, notamment lorsque l’utilisation de comptes de stockage à usage général représente la meilleure option, par exemple :

- Vous devez utiliser des tables, files d’attente ou fichiers et souhaitez que vos objets blob soient stockés dans le même compte de stockage. Notez qu’il n’existe aucun avantage technique à les stocker dans le même compte, si ce n’est que les clés partagées seront les mêmes.
- Vous devez toujours utiliser le modèle de déploiement Classic. Les comptes de stockage d’objets blob sont uniquement disponibles via le modèle de déploiement Azure Resource Manager.
- Vous devez utiliser des objets blob de pages. Les comptes de stockage d’objets blob ne gèrent pas les objets blob de pages. En général, nous recommandons d’utiliser des objets blob de blocs, sauf si vous avez spécifiquement besoin d’objets blob de pages.
- Vous utilisez une version de l’[API REST des services de stockage](https://msdn.microsoft.com/library/azure/dd894041.aspx) antérieure à celle du 14/02/2014 ou une bibliothèque cliente avec une version inférieure à 4.x, et vous ne pouvez pas mettre à niveau votre application.

> [AZURE.NOTE] Les comptes de stockage d’objets blob sont actuellement pris en charge dans la majorité des régions Azure, et cette prise en charge est amenée à s’élargir. Pour consulter la liste à jour des régions disponibles, voir la page [Régions Azure - Services par région](https://azure.microsoft.com/regions/#services).

## Comparaison entre les niveaux d’accès

Le tableau suivant met en évidence la comparaison entre les deux niveaux d’accès :

<table border="1" cellspacing="0" cellpadding="0" style="border: 1px solid #000000;">
<col width="250"> <col width="250"> <col width="250">
<tbody>
<tr>
    <td><strong><center></center></strong></td>
    <td><strong><center>Niveau d’accès au stockage chaud</center></strong></td>
    <td><strong><center>Niveau d’accès au stockage froid</center></strong>&lt; /td
</tr>
<tr>
    <td><strong><center>Disponibilité</center></strong></td>
    <td><center>99,9&#160;%</center></td>
    <td><center>99 %</center></td>
</tr>
<tr>
    <td><strong><center>Disponibilité<br>(Lectures RA-GRS)</center></strong></td>
    <td><center>99,99 %</center></td>
    <td><center>99,9&#160;%</center></td>
</tr>
<tr>
    <td><strong><center>Frais d’utilisation</center></strong></td>
    <td><center>Coûts de stockage supérieurs<br>Coûts d’accès et de transaction inférieurs</center></td>
    <td><center>Coûts de stockage inférieurs<br>Coûts d’accès et de transaction supérieurs</center></td>
</tr>
<tr>
    <td><strong><center>Taille minimale des objets<center></strong></td>
    <td colspan="2"><center>N/A</center></td>
</tr>
<tr>
    <td><strong><center>Durée de stockage minimale<center></strong></td>
    <td colspan="2"><center>N/A</center></td>
</tr>
<tr>
    <td><strong><center>Latence<br>(Temps jusqu’au premier octet)<center></strong></td>
    <td colspan="2"><center>millisecondes</center></td>
</tr>
<tr>
    <td><strong><center>Cibles de performance et d’évolutivité<center></strong></td>
    <td colspan="2"><center>Identiques aux comptes de stockage à usage général</center></td>
</tr>
</tbody>
</table>

> [AZURE.NOTE] Les comptes de stockage d’objets blob présentent les mêmes objectifs de performance et d’évolutivité que les comptes de stockage à usage général. Pour plus d’informations, consultez la page [Objectifs de performance et évolutivité d'Azure Storage](storage-scalability-targets.md).

## Tarification et facturation

Les comptes de stockage d’objets blob utilisent un nouveau modèle de tarification pour le stockage d’objets blob, basé sur le niveau d’accès. Les considérations de facturation suivantes s’appliquent à l’utilisation des comptes de stockage d’objets blob :

- **Coûts de stockage** : les coûts de stockage de données varient selon la quantité de données stockées et le niveau d’accès. Le coût par gigaoctet du niveau d’accès au stockage froid est inférieur à celui du niveau d’accès au stockage chaud.
- **Coûts d’accès aux données** : pour les données du niveau d’accès au stockage froid, des frais d’accès aux données en lecture et écriture vous seront facturés par gigaoctet.
- **Coûts de transaction** : des frais par transaction s’appliquent pour les deux niveaux. Toutefois, le coût par transaction du niveau d’accès au stockage froid est supérieur à celui du niveau d’accès au stockage chaud.
- **Coûts de transfert de données de géoréplication** : ces coûts s’appliquent uniquement aux comptes pour lesquels la géoréplication est configurée, y compris GRS et RA-GRS. Le transfert de données de géoréplication implique des frais par gigaoctet.
- **Coûts de transfert de données sortantes** : les transferts de données sortantes (données transférées hors d’une région Azure) sont facturés pour l’utilisation de la bande passante par gigaoctet. Cette facturation est cohérente avec les comptes de stockage à usage général.
- **Modification du niveau d’accès** : passer d’un niveau d’accès froid à un niveau d’accès chaud implique des frais correspondant à la lecture de toutes les données existantes du compte de stockage pour chaque transition. En revanche, le passage d’un niveau d’accès chaud à un niveau d’accès froid est gratuit.

> [AZURE.NOTE] Pour permettre aux utilisateurs de tester les nouveaux niveaux de stockage et de valider les fonctionnalités après lancement, le passage d’un niveau d’accès froid à un niveau d’accès chaud sera gratuit jusqu’au 30 juin 2016. À partir du 1er juillet 2016, les frais seront appliqués à toutes les transitions du niveau d’accès froid au niveau d’accès chaud. Pour plus d’informations sur le modèle de tarification des comptes de stockage d’objets blob, voir la page [Tarification d’Azure Storage](https://azure.microsoft.com/pricing/details/storage/). Pour plus d’informations sur les frais qu’implique le transfert de données sortantes, consultez la page [Détails de la tarification – Transferts de données](https://azure.microsoft.com/pricing/details/data-transfers/).

## Quick Start

Dans cette section, nous décrivons les scénarios suivants utilisant le portail Azure :

- Création d’un compte de stockage d’objets blob
- Gestion d’un compte de stockage d’objets blob

### Utilisation du portail Azure

#### Créer un compte de stockage d’objets blob à l’aide du Portail Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Dans le menu Hub, sélectionnez **Nouveau** -> **Données et stockage** -> **Compte de stockage**.

3. Entrez un nom pour votre compte de stockage.

4. Sélectionnez le modèle de déploiement **Resource Manager**.

5. Sélectionnez le type de compte de stockage **Blob Storage**.

6. Sélectionnez le niveau d’accès : **Chaud** ou **Froid**. Le niveau par défaut est **Chaud**.

7. Sélectionnez l’option de réplication pour le compte de stockage : **LRS**, **GRS** ou **RA-GRS**. La valeur par défaut est **RA-GRS**. Pour plus d’informations sur les options de réplication d’Azure Storage, consultez [Réplication Azure Storage](storage-redundancy.md).

8. Sélectionnez l’abonnement dans lequel vous souhaitez créer le compte de stockage.

9. Spécifiez un nouveau groupe de ressources ou sélectionnez un groupe de ressources existant. Pour plus d’informations sur les groupes de ressources, consultez [Utilisation du Portail Azure pour gérer vos ressources Azure](../azure-portal/resource-group-portal.md).

10. Sélectionnez l’emplacement géographique de votre compte de stockage.

11. Cliquez sur **Créer** pour créer le compte de stockage.

#### Modifier le niveau d’accès d’un compte de stockage d’objets blob à l’aide du portail Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com) et accédez à votre compte de stockage.

2. Cliquez sur **Tous les paramètres**, puis sur **Configuration** pour afficher et/ou modifier la configuration du compte.

3. Spécifiez le niveau d’accès souhaité : **Chaud** ou **Froid**.

    > [AZURE.NOTE] La modification du niveau d’accès peut entraîner des frais supplémentaires. Veuillez consulter la section [Tarification et facturation](storage-blob-storage-tiers.md#pricing-and-billing) pour plus de détails.

## Migration vers des comptes de stockage d’objets blob

Cette section vise à aider les utilisateurs à effectuer une transition en douceur vers les comptes de stockage d’objets blob. Un compte de stockage d’objets blob est un compte spécialisé pour stocker uniquement les objets blob de blocs et d’ajout. Les comptes de stockage à usage général existants, qui vous permettent également de stocker des tables, des files d’attente, des fichiers, des disques et des objets blob ne peuvent pas être convertis en comptes de stockage d’objets blob. Pour utiliser les niveaux de stockage, vous devez créer des comptes de stockage d’objets blob et migrer vos données existantes vers les comptes nouvellement créés.

### Planification de la migration des données existantes

Si vous déplacez vos données vers un compte de stockage d’objets blob, vous souhaiterez probablement tirer parti du niveau d’accès au stockage froid pour réduire les coûts de stockage des données moins fréquemment utilisées. Lors de la planification de la migration des données vers un compte de stockage d’objets blob avec un niveau d’accès au stockage froid, la première étape consiste à évaluer votre modèle d’utilisation existant afin de déterminer si vous pourrez tirer parti de cette migration. En général, vous souhaitez connaître :

- Votre modèle de consommation de stockage : quel est le volume de données stockées et quelle est son évolution mensuelle ?
- Vos modèles d’accès au stockage : quel est le volume de données du compte faisant l’objet d’accès en lecture et en écriture (y compris les nouvelles données) ? Combien de transactions sont utilisées pour accéder aux données, et quelles sont ces transactions ?

Pour savoir comment surveiller vos comptes de stockage existants et collecter ces données, voir [Activation des métriques Azure Storage et affichage des données associées](storage-enable-and-view-metrics.md). Grâce à ces données, vous pouvez à présent utiliser la [Calculatrice des prix Azure Storage](https://azure.microsoft.com/pricing/calculator/?scenario=data-management) pour faciliter l’estimation des coûts.

### Migration des données existantes

Vous pouvez utiliser les méthodes suivantes pour migrer les données existantes vers les comptes de stockage d’objets blob à partir d’une solution de stockage local, d’un fournisseur de stockage cloud tiers ou de vos comptes de stockage à usage général existants dans Azure :

#### AzCopy

AzCopy est un utilitaire de ligne de commande Windows conçu pour la copie de données hautes performances vers ou à partir d’Azure Storage. Vous pouvez utiliser AzCopy pour copier des données dans votre compte de stockage d’objets blob à partir de vos comptes de stockage à usage général existants, ou pour charger des données à partir de vos périphériques de stockage locaux vers votre compte de stockage d’objets blob.

Pour plus d’informations, consultez [Transfert de données avec l’utilitaire de ligne de commande AzCopy](storage-use-azcopy.md).

#### Bibliothèque de déplacement des données

La bibliothèque de déplacement de données Azure Storage pour .NET est basée sur l’infrastructure principale de déplacement de données sous-tendant AzCopy. La bibliothèque est conçue pour assurer des opérations de transfert de données fiables, simples et hautes performances, comme AzCopy. Cela vous permet de tirer pleinement parti des fonctionnalités offertes par AzCopy dans votre application de façon native, sans avoir à gérer l’exécution et la surveillance des instances externes d’AzCopy.

Pour plus d’informations, consultez [Azure Storage Data Movement Library for .Net](https://github.com/Azure/azure-storage-net-data-movement) (Bibliothèque de déplacement des données Azure Storage pour .Net).

#### API REST ou bibliothèque cliente

Vous pouvez créer une application personnalisée pour migrer vos données vers un compte de stockage d’objets blob à l’aide de l’une des bibliothèques clientes Azure ou de l’API REST des services Azure Storage. Azure Storage offre des bibliothèques clientes enrichies pour une diversité de langages et plateformes, par exemple .NET, Java, C++, Node.JS, PHP, Ruby et Python. Les bibliothèques clientes offrent des fonctionnalités avancées telles que la logique de nouvelle tentative, la journalisation et les téléchargements parallèles. Vous pouvez également développer votre application directement avec l’API REST, qui peut être appelée à l’aide de n’importe quel langage permettant de créer des requêtes HTTP/HTTPS.

Pour plus d’informations, consultez l’article [Prise en main du stockage d’objets blob Azure](storage-dotnet-how-to-use-blobs.md).

> [AZURE.NOTE] Les objets blob chiffrés à l’aide du chiffrement côté client stockent les métadonnées relatives au chiffrement stockées avec l’objet blob. Il est absolument essentiel que n’importe quel mécanisme de copie s’assure de la préservation des métadonnées de blob et en particulier des métadonnées relatives au chiffrement. Si vous copiez des objets blob sans ces métadonnées, le contenu de l’objet blob ne sera plus récupérable. Pour plus de détails concernant les métadonnées liées au chiffrement, consultez [Azure Storage client side encryption](storage-client-side-encryption.md) (Chiffrement côté client Azure Storage).

## FAQ

1. **Les comptes de stockage existants restent-ils disponibles ?**

    Oui. Les comptes de stockage existants restent disponibles, avec les mêmes tarifs et fonctionnalités. Ils ne permettent pas de choisir un niveau d’accès et n’offrent plus de possibilité de hiérarchisation.

2. **Quand et pourquoi dois-je commencer à utiliser des comptes de stockage d’objets blob ?**

    Les comptes de stockage d’objets blob sont des comptes spécialisés pour le stockage des objets blob. Ils nous permettent d’introduire de nouvelles fonctionnalités axées sur les objets blob. Désormais, les comptes de stockage d’objets blob représentent la méthode recommandée pour le stockage des objets blob. En effet, certaines fonctionnalités, telles que la hiérarchisation du stockage, seront introduites pour ce type de compte. Toutefois, la migration s’effectue au moment où vous le souhaitez, selon vos besoins.

3. **Puis-je convertir mon compte de stockage existant en compte de stockage d’objets blob ?**

    Non. Le compte de stockage d’objets blob est un type de compte de stockage différent. Vous devrez créer un compte et migrer vos données comme expliqué plus haut.

4. **Puis-je stocker des objets dans les deux niveaux d’accès d’un même compte ?**

    L’attribut de niveau d’accès est défini au niveau du compte et s’applique à tous les objets de ce compte. Impossible de définir le niveau d’accès au niveau objet.

5. **Puis-je modifier le niveau d’accès de mon compte de stockage d’objets blob ?**

    Oui. Vous pourrez modifier le niveau d’accès du compte de stockage. La modification du niveau d’accès au niveau d’un compte s’applique à tous les objets stockés dans le compte. Le passage d’un niveau d’accès chaud à un niveau froid n’implique pas de frais. En revanche, le passage d’un niveau d’accès froid à un niveau chaud entraîne un coût par Go pour la lecture de l’ensemble des données du compte.

6. **À quelle fréquence puis-je modifier le niveau d’accès de mon compte de stockage d’objets blob ?**

    Nous n’appliquons pas de limite concernant la fréquence de modification du niveau d’accès. Cependant, notez que le passage d’un niveau d’accès froid à un niveau chaud entraîne des frais significatifs. Il est déconseillé de modifier le niveau d’accès trop fréquemment.

7. **Les objets blob du niveau d’accès au stockage froid se comportent-ils différemment de ceux se trouvant au niveau d’accès au stockage chaud ?**

    Les objets blob du niveau d’accès au stockage chaud ont la même latence que les objets blob des comptes de stockage à usage général. Les objets blob du niveau d’accès au stockage froid ont une latence similaire (en millisecondes) à celle des objets blob des comptes de stockage à usage général.

    Les objets blob du niveau d’accès au stockage froid auront un niveau de service de disponibilité légèrement inférieur (SLA) que les objets blob stockés au niveau d’accès au stockage chaud. Pour plus d’informations, consultez [SLA for storage](https://azure.microsoft.com/support/legal/sla/storage) (SLA du stockage).

8. **Puis-je stocker des objets blob de pages et des disques de machine virtuelle dans les comptes de stockage d’objets blob ?**

    Les comptes de stockage d’objets blob prennent en charge uniquement les objets blob de blocs et d’ajout, mais pas les objets blob de pages. Les disques de machine virtuelle Azure sont soutenus par des objets blob de pages. Par conséquent, les comptes de stockage d’objets blob ne peuvent pas être utilisés pour stocker des disques de machine virtuelle. Toutefois, il est possible de stocker des sauvegardes de disques de machine virtuelle sous forme d’objets blob de blocs dans un compte de stockage d’objets blob.

9. **Dois-je modifier mes applications existantes pour utiliser des comptes de stockage d’objets blob ?**

    Les comptes de stockage d’objets blob sont cohérents à 100 % avec l’API avec les comptes de stockage à usage général pour les objets blob de blocs et d’ajout. Tant que votre application utilise des objets blob de blocs ou d’ajout, et que vous utilisez la version 2014-02-14 de l’[API REST Storage Services](https://msdn.microsoft.com/library/azure/dd894041.aspx) ou une version ultérieure, votre application doit fonctionner. Si vous utilisez une version antérieure du protocole, vous devrez mettre à jour votre application pour utiliser la nouvelle version afin de travailler en toute transparence avec les deux types de comptes de stockage. En général, nous recommandons d’utiliser la dernière version, quel que soit le type de compte de stockage que vous utilisez.

10. **L’expérience utilisateur changera-t-elle ?**

    Les comptes de stockage d’objets blob sont très similaires aux comptes de stockage à usage général d’objets blob de blocs et d’ajout et héritent de toutes les fonctionnalités clés d’Azure Storage, notamment de niveaux élevés de durabilité, disponibilité, évolutivité, performances et sécurité. Hormis les fonctionnalités et restrictions spécifiques aux comptes de stockage d’objets blob et aux niveaux d’accès correspondants évoqués plus haut, il n’existe aucune différence.

## Étapes suivantes

### Évaluer des comptes de stockage d’objets blob

[Vérifier la disponibilité de comptes de stockage d’objets blob par région](https://azure.microsoft.com/regions/#services)

[Évaluer l’utilisation des comptes de stockage actuels en activant les métriques Azure Storage](storage-enable-and-view-metrics.md)

[Vérifier le prix du stockage d’objets blob par région](https://azure.microsoft.com/pricing/details/storage/)

[Vérifier la tarification des transferts de données](https://azure.microsoft.com/pricing/details/data-transfers/)

### Commencer à utiliser des comptes de stockage d’objets blob

[Prise en main du stockage d’objets blob Azure](storage-dotnet-how-to-use-blobs.md)

[Transfert de données vers et à partir d’Azure Storage](storage-moving-data.md)

[Transfert de données avec l’utilitaire de ligne de commande AzCopy](storage-use-azcopy.md)

<!---HONumber=AcomDC_0511_2016-->
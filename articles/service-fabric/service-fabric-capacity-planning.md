<properties
   pageTitle="Planification de la capacité pour les applications Service Fabric | Microsoft Azure"
   description="Explique comment identifier le nombre de nœuds de calcul requis pour une application Service Fabric"
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="coreysa"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="05/13/2016"
   ms.author="subramar"/>


# Planification de la capacité pour les applications Service Fabric


Ce document explique comment estimer la quantité de ressources (UC, RAM, stockage sur disque) dont vous avez besoin pour exécuter vos applications Azure Service Fabric. Il est courant que les ressources requises changent au fil du temps. Vous avez besoin généralement de peu de ressources quand vous développez/testez votre service, puis vous avez besoin de plus de ressources quand vous passez en production et que votre application grandit en popularité. Quand vous concevez votre application, il est préférable de réfléchir à la configuration requise à long terme et de faire dès maintenant des choix qui permettront à votre service d’évoluer facilement pour répondre à la hausse de demande des clients.

 Quand vous créez un cluster Service Fabric, vous décidez quels types de machines virtuelles (VM) doivent le constituer. Chaque machine virtuelle est fournie avec une quantité limitée de ressources sous forme d’UC (cœurs et vitesse), bande passante réseau, mémoire RAM et stockage sur disque. À mesure que votre service croît, vous pouvez effectuer des mises à niveau vers des machines virtuelles qui offrent davantage de ressources et/ou ajouter des machines virtuelles à votre cluster. Bien entendu, dans ce dernier cas, vous devez créer l'architecture votre service initialement de façon à tirer parti des nouvelles machines virtuelles ajoutées dynamiquement au cluster.

Certains services gèrent peu à pas de données sur les machines virtuelles proprement dites. Ainsi, la planification de la capacité doit se concentrer principalement sur les performances. Cela signifie que vous devez étudier soigneusement les performances des algorithmes du code et les UC des machines virtuelles (cœurs et vitesse) requises pour exécuter vos algorithmes. En outre, vous devez prendre en compte la bande passante réseau, notamment la fréquence des transferts réseau et la quantité de données transférées. Si votre service doit être performant lorsque l'utilisation de service augmente, vous pouvez ajouter davantage de machines virtuelles dans le cluster et équilibrer la charge des demandes réseau entre les machines virtuelles.

Pour les services qui gèrent un grand nombre de données sur les machines virtuelles, la planification de la capacité doit se concentrer principalement sur la taille. Cela signifie que vous devez être attentif à la capacité de RAM et du stockage sur disque de la machine virtuelle. Le système de gestion de mémoire virtuelle dans Windows rend l’espace disque similaire à la RAM pour le code d’application. Cela permet aux applications d'utiliser plus de mémoire que la quantité physique disponible sur la machine virtuelle. Avoir plus de mémoire vive augmente simplement les performances, car la machine virtuelle peut conserver davantage de stockage sur disque dans la mémoire RAM. Quand vous choisissez une machine virtuelle, sélectionnez-en une dont l’espace disque peut contenir toutes les données requises sur la machine virtuelle, puis choisissez une quantité de mémoire RAM vous permettant d’accéder à ces données à la vitesse souhaitée. Si les données de votre service augmentent au fil du temps, vous pouvez ajouter davantage de machines virtuelles au cluster et partitionner les données entre les machines virtuelles.

## Déterminer le nombre de nœuds nécessaire

Le partitionnement de votre service vous permet de faire évoluer les données de votre service (voir [Partitionnement de Service Fabric](service-fabric-concepts-partitioning.md) pour plus d'informations sur le partitionnement). Chaque partition doit tenir sur une seule machine virtuelle, mais plusieurs (petites) partitions peuvent être placées sur une seule machine virtuelle. Ainsi, avoir un plus grand nombre de petites partitions offre davantage de souplesse que d’avoir un petit nombre de partitions plus grandes. En contrepartie, disposer d’un grand nombre de partitions augmente la charge de Service Fabric et vous ne pouvez pas effectuer des opérations traitées sur plusieurs partitions. Il y a également plus de trafic réseau potentiel si votre code de service doit fréquemment accéder à des éléments de données qui résident dans des partitions différentes. Lorsque vous concevez votre service, vous devez étudier soigneusement ces avantages et inconvénients pour obtenir une stratégie de partitionnement efficace.

Supposons que votre application possède un seul service avec état qui a une taille de magasin qui doit atteindre DB\_Size Go dans un an. Vous êtes prêt à ajouter d’autres applications (et partitions) pour accompagner votre croissance au-delà de cette année. Pour trouver la valeur DB\_Size totale sur tous les réplicas, nous devons également prendre en compte le facteur de réplication (RF), qui détermine le nombre de réplicas de votre service (la valeur DB\_Size totale sur tous les réplicas correspond au facteur de réplication multiplié par DB\_Size). Node\_Size représente l’espace disque/la RAM par nœud que vous souhaitez utiliser pour votre service. Pour de meilleures performances, la valeur DB\_Size doit tenir en mémoire au sein du cluster et vous devez placer une valeur Node\_Size correspondant à la capacité de la mémoire RAM de la machine virtuelle choisie. En allouant une valeur Node\_Size qui est supérieure à la capacité de la mémoire RAM, vous vous reposez sur la pagination du système d’exploitation. Ainsi, les performances ne seront sans doute pas optimales, mais peut-être suffisantes pour votre service.

Le nombre de nœuds nécessaires pour optimiser les performances peut être calculé comme suit :

```
Number of Nodes = (DB_Size * RF)/Node_Size

```


## Prendre la croissance en compte

Vous pouvez calculer le nombre de nœuds en fonction de la valeur DB\_Size que votre service doit atteindre selon vous, en plus de la valeur DB\_Size avec laquelle vous commencez. Vous pouvez ensuite augmenter le nombre de nœuds à mesure que votre service se développe afin de ne pas surapprovisionner le nombre de nœuds. Toutefois, le nombre de partitions doit être basé sur le nombre de nœuds qui sont nécessaires quand vous exécutez votre service à croissance maximale.

Il est également judicieux d’avoir quelques ordinateurs de rechange (capacité excédentaire) disponibles à tout moment afin de pouvoir gérer les pics d’activité inattendus ou toute défaillance de l’infrastructure (par exemple, si plusieurs machines virtuelles tombent en panne). Bien que cela soit quelque chose que vous devez déterminer à l’aide de vos pics attendus, un bon point de départ serait de réserver quelques machines virtuelles supplémentaires (5 à 10 % supplémentaires).

La proposition ci-dessus suppose un seul service avec état. Si vous avez plus d’un service avec état, vous devez ajouter la valeur DB\_Size associée aux autres services à l’équation ou calculer le nombre de nœuds séparément pour chaque service avec état. Votre service peut avoir des réplicas ou des partitions qui ne sont pas équilibrés. Certaines partitions pouvant avoir plus de données que d’autres, consultez l’[article sur les meilleures pratiques de partitionnement](service-fabric-concepts-partitioning.md) pour plus d’informations. Toutefois, l’équation ci-dessus ne tient pas compte des partitions et des réplicas, car Service Fabric garantit que les réplicas sont répartis entre les nœuds de manière optimisée.


## Utiliser une feuille de calcul pour le calcul des coûts

Maintenant, nous allons placer des chiffres réels dans la formule ci-dessus. Un [exemple de feuille de calcul](https://servicefabricsdkstorage.blob.core.windows.net/publicrelease/SF%20VM%20Cost%20calculator-NEW.xlsx) montre comment planifier la capacité pour une application qui contient trois types d’objets de données. Pour chaque objet, nous estimons sa taille et le nombre d'objets que nous nous attendons à avoir. Nous sélectionnons également le nombre de réplicas pour chaque type d’objet. La feuille de calcul calcule la quantité totale de mémoire à stocker dans le cluster.

Ensuite, nous entrons une taille de machine virtuelle et le coût mensuel. Selon la taille de la machine virtuelle, la feuille de calcul vous indique le nombre minimal de partitions nécessaires à la répartition physique des données sur les nœuds. Vous pouvez souhaiter un plus grand nombre de partitions pour prendre en charge les besoins en calcul et trafic réseau spécifiques de votre application. La feuille de calcul montre que le nombre de partitions pour gérer les objets de profils utilisateur est passé de un à six.

À présent, avec toutes ces informations, la feuille de calcul montre que vous pouvez physiquement obtenir toutes les données avec les partitions et les réplicas souhaités sur un cluster de 26 nœuds. Toutefois, ce cluster étant très compact, vous pouvez vouloir des nœuds supplémentaires pour prendre en charge les mises à niveau et les défaillances de nœud. La feuille de calcul indique également qu’avoir plus de 57 nœuds ne fournit aucune valeur supplémentaire, car vous auriez des nœuds vides. Là encore, vous souhaiterez peut-être aller au-dessus des 57 nœuds malgré tout pour prendre en charge les mises à niveau et les défaillances de nœud. Vous pouvez modifier la feuille de calcul pour répondre aux besoins spécifiques de votre application.

![Feuille de calcul pour le calcul des coûts][Image1]



## Étapes suivantes

Découvrez le [Partitionnement des services Service Fabric][10] pour en savoir plus sur le partitionnement de votre service.



<!--Image references-->
[Image1]: ./media/SF-Cost.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-concepts-partitioning.md

<!---HONumber=AcomDC_0518_2016-->
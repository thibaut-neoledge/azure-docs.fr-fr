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
   ms.date="11/12/2015"
   ms.author="subramar"/>


# Planification de la capacité pour les applications Service Fabric


Ce document explique comment estimer la quantité de ressources (UC, RAM, stockage sur disque) dont vous avez besoin pour exécuter vos applications Service Fabric. Il est courant que les ressources requises changent au fil du temps. Vous avez besoin généralement de peu de ressources lorsque vous développez/testez votre service, puis vous avez besoin de plus de ressources lorsque vous passez en production et que votre application grandit en popularité. Lorsque vous concevez votre application, il est préférable de réfléchir à la configuration requise à long terme dès aujourd'hui et de faire dès maintenant des choix qui permettront à votre service d'évoluer facilement pour répondre à la hausse de demande des clients. Lorsque vous créez un cluster Service Fabric, vous décidez quels types de machines virtuelles (VM) doivent le constituer. Chaque machine virtuelle est fournie avec une quantité limitée de ressources sous forme d'UC (cœurs et vitesse), bande passante réseau, mémoire RAM et stockage sur disque. Lorsque votre service augmente au fil du temps, vous pouvez mettre à niveau vers des machines virtuelles offrant davantage de ressources ou ajouter davantage de machines virtuelles dans votre cluster. Bien entendu, dans ce dernier cas, vous devez créer l'architecture votre service initialement de façon à tirer parti des nouvelles machines virtuelles ajoutées dynamiquement au cluster.

Certains services gèrent peu à pas de données sur les machines virtuelles proprement dites. Par conséquent, la planification de la capacité doit se concentrer principalement sur les performances. Cela signifie que vous devez étudier soigneusement les performances des algorithmes du code et l'UC des machines virtuelles (cœurs et vitesse) requises pour exécuter vos algorithmes. En outre, vous devez également prendre en compte la bande passante réseau et en particulier la fréquence des transferts réseau et la quantité de données transférées. Si votre service doit être performant lorsque l'utilisation de service augmente, vous pouvez ajouter davantage de machines virtuelles dans le cluster et équilibrer la charge des demandes réseau entre les machines virtuelles.

Pour les services qui gèrent un grand nombre de données sur les machines virtuelles, la planification de la capacité doit se concentrer principalement sur la taille. Cela signifie que vous devez être attentif à la capacité de RAM et du stockage sur disque de la machine virtuelle. Le système de gestion de mémoire virtuelle Windows rend l'espace disque similaire à la RAM pour le code d'application. Cela permet aux applications d'utiliser plus de mémoire que la quantité physique disponible sur la machine virtuelle. Avoir plus de mémoire vive augmente simplement les performances car la machine virtuelle peut conserver davantage de stockage sur disque dans la mémoire RAM. Lorsque vous choisissez une machine virtuelle, sélectionnez-en une dont l'espace disque peut contenir toutes les données requises sur la machine virtuelle, puis choisissez une taille de mémoire RAM vous permettant d'accéder à ces données à la vitesse souhaitée. Si les données de votre service augmentent au fil du temps, vous pouvez ajouter davantage de machines virtuelles au cluster et partitionner les données entre les machines virtuelles.

## De combien de nœuds avez-vous besoin ?

Le partitionnement de votre service vous permet de faire évoluer les données de votre service (voir [Partitionnement de Service Fabric](service-fabric-concepts-partitioning.md) pour plus d'informations sur le partitionnement). Chaque partition doit tenir sur une seule machine virtuelle, mais plusieurs (petites) partitions peuvent être placées sur une seule machine virtuelle. Par conséquent, avoir un plus grand nombre de petites partitions offre davantage de souplesse au lieu d'avoir un petit nombre de partitions plus grandes. Le compromis est que disposer d'un grand nombre de partitions augmente la charge de Service Fabric et vous ne pouvez pas effectuer des opérations traitées sur plusieurs partitions. Il y a également plus de trafic réseau potentiel si votre code de service doit fréquemment accéder à des éléments de données qui résident dans des partitions différentes. Lorsque vous concevez votre service, vous devez étudier soigneusement ces avantages et inconvénients pour obtenir une stratégie de partitionnement efficace.

Supposons que votre application possède un seul service avec état qui a une taille de magasin qui doit atteindre DB\_Size Go dans un an, et vous êtes prêt à ajouter d'autres applications (et partitions) pour accompagner votre croissance au-delà de cette année. Pour trouver la taille totale de DB sur tous les réplicas, nous devons également prendre en compte le facteur de réplication, RF, qui détermine le nombre de réplicas de votre service (la taille totale de la base de données sur tous les réplicas correspond au facteur de réplication multiplié par DB\_size). Node\_Size représente l'espace disque/la RAM par nœud que vous souhaitez utiliser pour votre service. Pour de meilleures performances, la base de données doit tenir en mémoire au sein du cluster et vous devrez placer une valeur Node\_Size correspondant à la capacité de la mémoire RAM de la machine virtuelle choisie. En allouant une valeur Node\_Size qui est supérieure à la capacité de la mémoire RAM, vous vous reposez sur la pagination du système d'exploitation. Par conséquent, les performances ne seront sans doute pas optimales, mais peut-être suffisantes pour votre service.

Par conséquent, le nombre de nœuds nécessaires pour optimiser les performances peut être calculé comme suit :

```
Number of Nodes = (DB_Size * RF)/Node_Size

```


## Prendre la croissance en compte

Vous pouvez calculer le nombre de nœuds en fonction de la taille de la base de données que votre service doit atteindre selon vous, en plus de la taille de la base de données avec laquelle vous commencez, et augmenter le nombre de nœuds à mesure que votre service se développe afin de ne pas surapprovisionner le nombre de nœuds. Toutefois, le nombre de partitions doit être basé sur le nombre de nœuds requis lors de l'exécution de votre service à croissance maximale. Il est judicieux d'avoir quelques ordinateurs de rechange (capacité excédentaire) disponibles à tout moment afin de pouvoir gérer les pics d'activité inattendus ou toute défaillance de l'infrastructure (par exemple, si plusieurs machines virtuelles tombent en panne). Bien que cela soit quelque chose qui doit être déterminé à l'aide de vos pics attendus, un bon point de départ serait de réserver quelques machines virtuelles supplémentaires (5 à 10 % supplémentaires).

La proposition ci-dessus suppose un seul service avec état ; si vous avez plus d'un service avec état, vous devez ajouter la taille de la base de données associée aux autres services dans l'équation ou calculer le nombre de nœuds séparément pour chaque service avec état. Votre service peut avoir des réplicas ou des partitions qui ne sont pas équilibrés (par exemple, certaines partitions peuvent avoir plus de données que d'autres, veuillez consultez l'article de partitionnement sur les meilleures pratiques.) Toutefois, l'équation ci-dessus ne tient pas compte du nombre de partitions ou de réplicas, car Service Fabric garantit que les réplicas sont répartis entre les nœuds de manière optimisée.


## Feuille de calcul pour le calcul des coûts

Maintenant, nous allons placer des chiffres réels dans la formule ci-dessus. Un exemple de feuille de calcul [fourni ici](https://servicefabricsdkstorage.blob.core.windows.net/publicrelease/SF%20VM%20Cost%20calculator-NEW.xlsx) montre comment planifier la capacité pour une application contenant trois types d'objets de données. Pour chaque objet, nous estimons sa taille et le nombre d'objets que nous nous attendons à avoir. Nous avons également sélectionné le nombre de réplicas pour chaque type d'objet. La feuille de calcul calcule la quantité totale de mémoire à stocker dans le cluster. Ensuite, nous entrons une taille de machine virtuelle et le coût mensuel. Selon la taille de la machine virtuelle, la feuille de calcul vous indique le nombre minimal de partitions selon lesquelles vous devez diviser vos données pour tenir physiquement sur les nœuds. Vous pouvez souhaiter un plus grand nombre de partitions pour prendre en charge les besoins en calcul et trafic réseau spécifiques de votre application. La feuille de calcul affiche l'augmentation du nombre de partitions pour gérer les objets de profils utilisateur de 1 à 6.

À présent, avec toutes ces informations, la feuille de calcul montre que vous pouvez physiquement obtenir toutes les données avec les partitions et les réplicas souhaités sur un cluster de 26 nœuds. Toutefois, ce cluster est très compact, donc vous pouvez vouloir des nœuds supplémentaires pour prendre en charge les mises à niveau et les défaillances de nœud. La feuille de calcul indique également que plus de 57 nœuds ne fournit aucune valeur supplémentaire. Vous seriez obligé d'avoir des nœuds vides. Là encore, vous souhaiterez peut-être aller au-dessus des 57 nœuds malgré tout pour prendre en charge les mises à niveau et les défaillances de nœud. Vous pouvez modifier la feuille de calcul pour répondre aux besoins spécifiques de votre application.

![][Image1]



## Étapes suivantes

Découvrez le [Partitionnement des services Service Fabric][10] pour en savoir plus sur le partitionnement de votre service.



<!--Image references-->
[Image1]: ./media/SF-Cost.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-concepts-partitioning.md

<!---HONumber=AcomDC_1125_2015-->
<!-- saved from url=(0022)http://internet.e-mail -->

Mise à l'échelle d'une solution de base de données SQL
======================================================

Sur Azure, l'extensibilité de la base de données est synonyme de montée en charge, où une charge de travail est redistribuée sur plusieurs serveurs de commodité d'un centre de données. La montée en charge est une stratégie visant à résoudre des problèmes de capacité ou de performances des données. Une base de données très volumineuse subissant une très forte croissance finira peut-être par nécessiter une stratégie de montée en charge, que peu d'utilisateurs y accèdent ou, au contraire, un très grand nombre.

Le meilleur moyen d'obtenir une montée en charge sur Azure est d'utiliser la fédération. La fédération de base de données SQL est basée sur le partitionnement horizontal, où une ou plusieurs tables sont fractionnées par ligne et réparties sur plusieurs membres de la fédération.

La fédération n'est pas la seule réponse à chaque problème d'extensibilité. Parfois, les caractéristiques des exigences de vos données ou applications pointent vers des approches plus simples. La liste suivante présente des solutions potentielles par ordre de complexité.

Augmentation de la taille de la base de données
-----------------------------------------------

Les bases de données sont créées à une taille fixe sujette à un maximum imposé par chaque édition. Pour une édition Web, vous pouvez augmenter une base de données jusqu'à un maximum de 5 gigaoctets. Pour une édition Business, la taille maximale de la base de données est de 150 gigaoctets. La manière la plus évidente d'augmenter la capacité de la base de données est de modifier l'édition et la taille maximale :

     ALTER DATABASE school MODIFY (EDITION = 'Business', MAXSIZE=10GB);

Utilisation de plusieurs bases de données et allocation d'utilisateurs
----------------------------------------------------------------------

Dans les scénarios limités, vous pourriez créer des copies d'une base de données, puis allouer des connexions et des utilisateurs sur chaque base de données. Avant que la fédération ne soit une option, il s'agissait d'une approche courante pour redistribuer une charge de travail. Cette approche est viable pour les bases de données que vous utilisez à court terme, puis fusionnez ultérieurement dans une base de données principale que vous conservez localement, ainsi que pour les solutions qui fournissent des données en lecture seule.

Utilisation de fédérations
--------------------------

Les fédérations dans une base de données SQL sont utilisées pour obtenir une extensibilité et des performances accrues. Une ou plusieurs tables d'une base de données sont fractionnées par ligne et réparties sur plusieurs bases de données (membres de la fédération). Ce type de partitionnement horizontal est souvent nommé « sharding » (partitionnement). Les scénarios principaux dans lesquels ce partitionnement est utile sont ceux où vous devez atteindre une certaine extensibilité ou certaines performances, ou gérer la capacité.

Les fédérations sont prises en charge dans l'édition Business. Pour plus d'informations, consultez la page [Fédérations dans la base de données SQL](http://msdn.microsoft.com/fr-fr/library/windowsazure/hh597452.aspx) et [Didacticiel sur les fédérations de Base de données SQL -- DBA](http://msdn.microsoft.com/fr-fr/library/windowsazure/hh778416.aspx).

Prise en considération d'autres formes de stockage
--------------------------------------------------

Gardez à l'esprit qu'Azure prend en charge plusieurs formes de stockage des données, y compris des stockages de tables et d'objets blob. Si vous n'avez pas besoin de fonctionnalités relationnelles, le stockage des tables ou des objets blob peut être plus économique.


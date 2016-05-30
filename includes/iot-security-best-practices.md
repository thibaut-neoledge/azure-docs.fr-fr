# Meilleures pratiques de sécurité pour l’Internet des objets (IoT)

La sécurisation d’une infrastructure IoT implique la mise en œuvre d’une stratégie complète et rigoureuse. De la sécurisation des données dans le cloud à la protection de l’intégrité des données transitant sur le réseau Internet public, en passant par la capacité à configurer des appareils en toute sécurité, chaque couche contribue à garantir la sécurité de l’infrastructure globale.

## Sécurisation d’une infrastructure IoT
 
Cette stratégie de sécurité complète peut être développée et appliquée avec la participation active des différents acteurs impliqués dans la fabrication, le développement et le déploiement de l’infrastructure et des appareils IoT. Voici une description générale de ces acteurs.

- **Fabricant/intégrateur de matériel IoT** : il s’agit généralement de fabricants de matériels IoT déployés, d’intégrateurs qui assemblent le matériel de différents fabricants ou de fournisseurs de matériels dédiés à un déploiement IoT fabriqué ou intégré par d’autres fournisseurs.
- **Développeur de solutions IoT** : le développement d’une solution IoT est généralement assuré par un développeur de solutions, qui peut faire partie d’une équipe interne, ou par un intégrateur système spécialisé dans cette activité. Le développeur de solutions IoT peut développer différents composants de la solution IoT de bout en bout, intégrer une variété de composants du commerce ou open source ou adopter des solutions préconfigurées moyennant une légère adaptation.
- **Responsable du déploiement de solutions IoT** : une fois qu’une solution IoT est développée, elle doit être déployée sur le terrain. Cela implique le déploiement du matériel, l’interconnexion des appareils et le déploiement des solutions sur les appareils ou dans le cloud.
- **Opérateur de solutions IoT** : une fois que la solution IoT est déployée, elle requiert une exploitation, une surveillance, des mises à niveau et une maintenance sur la durée. Cela peut être assuré par une équipe interne comprenant des spécialistes en technologies de l’information, des équipes d’exploitation et de maintenance du matériel et des spécialistes du domaine qui contrôlent le comportement de l’infrastructure IoT globale. 

Les sections qui suivent décrivent les meilleures pratiques associées à chacun de ces acteurs, en termes de développement, de déploiement et d’exploitation d’une infrastructure IoT sécurisée.

## Fabricant/intégrateur de matériel IoT

Si vous êtes fabricant ou intégrateur de matériel IoT, suivez les meilleures pratiques ci-dessous :

- **Concevoir le matériel selon les exigences minimales** : la conception du matériel doit inclure les fonctionnalités minimales requises pour son fonctionnement, et rien de plus. Par exemple, des ports USB seront inclus uniquement s’ils sont nécessaires au fonctionnement de l’appareil. Les fonctionnalités supplémentaires exposent l’appareil à des vecteurs d’attaque indésirables et doivent être évitées. 
- **Protéger l’appareil contre les falsifications** : intégrez un mécanisme permettant de détecter toute falsification physique du matériel (ouverture du capot, suppression d’une partie de l’appareil, etc.). Les signaux de falsification peuvent être inclus dans le flux de données chargé vers le cloud, permettant d’informer les opérateurs de ces événements. 
- **Intégrer la sécurité au matériel** : si le coût des marchandises vendues le permet, intégrez des fonctionnalités de sécurité, telles qu’un stockage sécurisé et chiffré, ainsi qu’une fonctionnalité de démarrage basée sur un Module de plateforme sécurisée (TPM). Ces fonctionnalités renforcent la sécurité des appareils, protégeant ainsi l’infrastructure IoT globale.
- **Sécuriser les mises à jour** : le microprogramme doit être mis à jour durant toute la durée de vie de l’appareil. Prévoyez des chemins d’accès sécurisés pour les mises à jour et un chiffrement garanti de la version du microprogramme. L’appareil sera ainsi sécurisé pendant et après les mises à jour.

## Développeur de solutions IoT

Si vous êtes développeur de solutions IoT, suivez les meilleures pratiques ci-dessous :

- **Respecter la méthodologie de développement de logiciels sécurisés** : le développement de logiciels sécurisés requiert une réflexion de bout en bout sur la sécurité, depuis le lancement du projet jusqu’à son implémentation, aux tests et au déploiement. Le choix des plateformes, des langages et des outils est influencé par cette méthodologie. Microsoft Security Development Lifecycle offre une approche pas à pas de la création de logiciels sécurisés.
- **Choisir des logiciels Open source avec précaution** : les logiciels Open source offrent la possibilité de développer des solutions rapidement. Lorsque vous choisissez des logiciels open source, tenez compte du niveau d’activité de la communauté vis-à-vis de chaque composant open source. Avec une communauté active, les logiciels bénéficieront d’un vaste soutien, et les problèmes seront identifiés et traités. Ce ne sera pas le cas d’un logiciel open source obscur et inactif : les problèmes ne seront probablement pas identifiés.
- **Intégrer les composants avec précaution** : de nombreuses failles de sécurité logicielle existent à la limite des bibliothèques et des API. Des fonctionnalités qui ne sont pas requises pour le déploiement concerné peuvent rester disponibles par le biais d’une couche API. Pour garantir une sécurité globale, il convient donc de s’assurer que toutes les interfaces des composants intégrés sont sécurisées.      

## Responsable du déploiement de solutions IoT

Si vous êtes responsable du déploiement de solutions IoT, suivez les meilleures pratiques ci-dessous :

- **Déployer le matériel de façon sécurisée** : les déploiements IoT peuvent nécessiter le déploiement de matériels dans des lieux non sécurisés, comme des espaces publics ou des lieux non surveillés. Dans ce cas, assurez-vous que le déploiement matériel est protégé contre les falsifications, autant que faire se peut. Si le matériel est doté de ports, notamment de ports USB, assurez-vous qu’ils sont correctement protégés. De nombreux vecteurs d’attaque peuvent les utiliser comme point d’entrée.
- **Protéger les clés d’authentification** : lors du déploiement, chaque appareil requiert les ID d’appareils et les clés d’authentification associées, générés par le service cloud. Protégez physiquement ces clés, même après le déploiement. Une clé compromise pourrait être utilisée par un appareil malveillant pour usurper l’identité d’un appareil existant.

## Opérateur de solutions IoT

Si vous êtes opérateur de solutions IoT, suivez les meilleures pratiques ci-dessous :

- **Garder le système à jour** : vérifiez que les systèmes d’exploitation et tous les pilotes des appareils sont mis à jour avec les dernières versions. Lorsque les mises à jour automatiques sont activées, Windows 10 (IoT ou autres références) est constamment mis à jour par Microsoft. Les appareils IoT bénéficient ainsi d’un système d’exploitation sécurisé. Vous devez également mettre à jour régulièrement les autres systèmes d’exploitation, tels que Linux, pour les protéger contre les attaques malveillantes. 
- **Protéger le système contre les activités malveillantes**: implémentez les dernières fonctionnalités de protection antivirus et d’anti-programme malveillant sur le système d’exploitation de chaque appareil, lorsque celui-ci le permet. Ceci peut contribuer à atténuer la plupart des menaces externes. La plupart des systèmes d’exploitation modernes, tels que Windows 10 IoT et Linux, peuvent être protégés contre ces menaces grâce à des mesures appropriées. 
- **Procéder à des audits fréquents** : il est essentiel d’auditer l’infrastructure IoT en cas de problèmes de sécurité. La plupart des systèmes d’exploitation, tels que Windows 10 (IoT et autres références), offrent une journalisation des événements intégrée. Examinez les journaux fréquemment pour vous assurer qu’aucune violation de sécurité n’a été détectée. Les informations d’audit peuvent être envoyées sous la forme d’un flux de télémétrie au service cloud et peuvent être analysées.
- **Protéger physiquement l’infrastructure IoT** : les attaques de sécurité les plus menaçantes envers l’infrastructure IoT proviennent d’un accès physique aux appareils. La protection des appareils contre l’utilisation malveillante des ports USB et d’autres modes d’accès physiques représente donc une pratique de sécurité essentielle. La journalisation de l’accès physique, notamment de l’utilisation des ports USB, est indispensable pour détecter toute violation. Là encore, Windows 10 (IoT et autres références) peut assurer une journalisation détaillée de ces événements.
- **Protéger les informations d’identification cloud** : les informations d’identification cloud utilisées pour configurer et exploiter un déploiement IoT constituent peut-être le moyen le plus simple d’accéder à un système IoT et de le compromettre. Protégez les informations d’identification en modifiant fréquemment le mot de passe et en évitant de les utiliser sur des machines publiques. 

Les fonctionnalités des différents appareils IoT peuvent varier. Certains appareils peuvent être de véritables ordinateurs, exécutant des systèmes d’exploitation courants et d’autres peuvent exécuter des systèmes d’exploitation très légers. Les meilleures pratiques en matière de sécurité décrites ci-dessus peuvent s’appliquer à ces appareils selon différents degrés. Il se peut que le fabricant de ces appareils établisse ses propres meilleures pratiques en matière de sécurité et de déploiement. Le cas échéant, elles doivent être observées également.

Certains appareils existants et limités n’ont peut-être pas été conçus spécifiquement pour un déploiement IoT. Il se peut qu’ils ne soient pas en mesure de chiffrer les données, de se connecter à Internet, d’assurer un audit avancé, etc. Dans ce cas, l’utilisation d’une passerelle de champ moderne et sécurisée pour agréger les données des appareils existants peut offrir la sécurité requise pour connecter ces appareils à Internet. Les passerelles de champ assurent alors l’authentification sécurisée, la négociation de sessions chiffrées, la réception des commandes du cloud et beaucoup d’autres fonctionnalités de sécurité.


Des mises à jour de l’agent Azure File Sync seront publiées régulièrement pour ajouter de nouvelles fonctionnalités et pour résoudre les problèmes rencontrés. Nous vous recommandons de configurer Microsoft Update pour obtenir toutes les mises à jour de l’agent Azure File Sync à mesure que nous les publions. Nous sommes conscients que certaines organisations souhaitent contrôler et tester les mises à jour de manière stricte. 

Pour les déploiements utilisant des versions antérieures de l’agent Azure File Sync :

- Le service de synchronisation de stockage autorise la version principale précédente pendant trois mois après la publication initiale de la nouvelle version principale. Par exemple, le service de synchronisation de stockage prend en charge la version 1.\* pendant trois mois après la publication de la version 2.\*.
- Au bout de ces trois mois, le service de synchronisation de stockage commence à ne plus synchroniser les serveurs inscrits qui utilisent la version expirée avec leurs groupes de synchronisation.
- Pendant les trois mois d’utilisation de la version principale précédente, seule la version principale actuelle reçoit les correctifs de bogues.

> [!Note]  
> Nous vous avertirons par notification toast dans le portail Azure si vous utilisez une version d’Azure File Sync qui va expirer dans les trois mois suivants.
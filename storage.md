# Comprendre le stockage

Avant de choisir son OS ou ses composants, il est important de comprendre comment les données sont stockées sur les NAS. Cette documentation explique comment les NAS gèrent les données, les différents systèmes de fichiers (File Systems) et les différents types de RAID qui existent.

- [Comprendre le stockage](#comprendre-le-stockage)
- [Les différents files systems](#les-différents-files-systems)
  - [XFS](#xfs)
  - [BTRFS](#btrfs)
  - [ZFS](#zfs)
  - [Conclusion](#conclusion)
- [Les RAID c'est quoi ?](#les-raid-cest-quoi-)
  - [Stripe pool ou RAID0](#stripe-pool-ou-raid0)
    - [Exemple](#exemple)
  - [Mirror pool ou RAID1](#mirror-pool-ou-raid1)
    - [Exemple](#exemple-1)
  - [RAIDZ1 ou RAID5](#raidz1-ou-raid5)
    - [Exemple](#exemple-2)
  - [RAIDZ2 ou RAID6](#raidz2-ou-raid6)
    - [Exemple](#exemple-3)
  - [RAIDZ3](#raidz3)
  - [Striped+Mirror pool ou RAID10](#stripedmirror-pool-ou-raid10)

# Les différents files systems

Les files systems (FS) sont la façon dont vos données sont structurées et gérées par votre OS. Il existe de nombreux FS différents, mais nous allons ici nous concentrer sur trois en particulier.

## XFS

XFS est un FS qui date des années 90 et qui a pour objectif de permettre l'accès à de gros fichiers tout en évitant la corruption des fichiers.

Dans son fonctionnement, XFS utilise un système de journalisation, c'est-à-dire que le système enregistre tous les changements effectués sur les fichiers pour s'assurer que rien ne soit corrompu.

XFS est reconnu pour sa performance, en partie grâce à sa façon de subdiviser les volumes physiques des disques durs, mais aussi en raison de sa méthode d'indexation des données (arbres B+). Ce système est aussi performant car il permet le DMA (Direct Memory Access), qui permet le transfert de données entre les applications et les disques. Grâce à ce fonctionnement, XFS peut garantir une vitesse de lecture et d'écriture minimum pour certaines applications (c'est le seul FS qui peut permettre ça).

Ce FS est toujours très utilisé aujourd'hui, bien qu'il soit principalement destiné à un usage spécifique pour le stockage de données. La communauté autour de XFS est toujours très active et la documentation est assez complète, compte tenu de son ancienneté. Globalement, c'est un FS plutôt fiable.

## BTRFS

BTRFS est un FS qui date des années 2000.

Il fonctionne sur un principe de Copy-On-Write (COW), c'est-à-dire que le système manipule un fichier que si celui-ci est modifié. Lorsque ce fichier est modifié, il est copié plutôt que d'être directement écrasé, puis la version originale est supprimée. Ce fonctionnement permet d'éviter les problèmes liés au kernel ou à une coupure de courant.

BTRFS a été l'un des premiers systèmes de fichiers à permettre d'agrandir les pools de stockage disque par disque, ce qui est pratique pour les petits systèmes.

Cependant, ce système n'est pas parfait. Bien qu'il soit mis à jour assez fréquemment, de nouvelles fonctionnalités sont constamment ajoutées, malgré le fait que les fonctionnalités de base ne soient toujours pas complètement stabilisées (les implémentations RAID5 et RAID6 ne sont toujours pas parfaites, voir plus bas). De plus, si vous souhaitez envoyer ou recevoir des données chiffrées, BTRFS est une mauvaise option, car il doit déchiffrer puis rechiffrer les données à chaque transmission, ce qui entraîne un traitement des fichiers assez long.

BTRFS est prometteur et a une communauté très active, mais il est malheureusement encore loin d'être un FS mature.

## ZFS

ZFS est également un FS qui date des années 2000. Il a été créé à quelques semaines d'écart avec BTRFS. Initialement open-source, Oracle a racheté ZFS, ce qui a poussé la communauté à forker le projet et créer OpenZFS.

Comme BTRFS, ZFS fonctionne avec le principe de Copy-On-Write (COW).

ZFS est probablement le FS le plus abouti parmi les récents. Il est complet, prenant en charge de nombreux types de RAID, performant, scalable et évolutif. En plus de ça, il est mis à jour assez fréquemment, et ces mises à jour sont souvent très utiles (comme l'expansion de pool, enfin disponible).

C'est, selon moi, le meilleur FS qui existe, car il est à la fois flexible et très sécurisé, grâce à ses implémentations RAIDZ.

## Conclusion

N'hésitez pas à vous renseigner davantage sur les différents systèmes de fichiers mentionnés plus haut. Personnellement, je recommande fortement ZFS, car c'est, selon moi, le plus adapté pour du self-hosting à une échelle modérée. Il offre de nombreux avantages.

# Les RAID c'est quoi ?

Les Redundant Array of Independent Disks (RAID) sont des méthodes permettant de stocker et d'écrire des données sur plusieurs disques d'un même système. C'est une manière logicielle de gérer le stockage en faisant en sorte que plusieurs disques physiques ne forment qu'une unité logicielle. Ce système permet d'assurer la redondance des données et d'améliorer les performances de lecture.

Il existe plusieurs types de RAID qui mettent l'accent sur des avantages différents. Le RAID fonctionne avec un système de parité, qui permet de sécuriser les données si jamais un ou plusieurs disques physiques cessent de fonctionner. En gros, si un disque tombe en panne, il est possible de reconstituer les données perdues grâce aux données des autres disques. Ce système de parité prend de la place sur ce qu'on appelle le pool, c'est-à-dire l'ensemble des disques.

Afin de pouvoir utiliser un système de RAID, il est nécessaire d'avoir un RAID Controller, un composant physique auquel les disques durs sont connectés. Les solutions comme ZFS, XFS, etc. sont des alternatives logicielles permettant de se passer d'un contrôleur physique.

Pour simplifier, je vais décrire uniquement les différents niveaux de RAIDZ, le système RAID utilisé par ZFS, tout en évoquant les équivalents RAID classiques. Il faut noter que le fonctionnement réel des systèmes RAID et RAIDZ n'est pas exactement le même, mais leur finalité est similaire si ce n'est identique.

Afin de mieux comprendre les différents niveaux, des exemples seront donnés sur la capacité de stockage, ainsi que sur les vitesses d'écriture et de lecture. Tous les exemples utiliseront 4 disques de 4 To pour simuler les performances, sauf mention contraire.

## Stripe pool ou RAID0

Ce niveau ne permet pas de redondance des données, il permet simplement d'utiliser plusieurs disques comme un seul. Si un disque physique tombe en panne, toutes les données sont perdues, y compris celles des autres disques.

Ce mode de stockage n'est pas recommandé lorsqu'il est utilisé seul, mais il fait partie du niveau Stripe+Mirror (ou RAID10) qui est décrit plus bas.

Cette méthode de stockage est la moins coûteuse et celle qui a le moins de perte de capacité.

### Exemple

Si nous avons 4 disques de 4To, avec ce système nous aurons donc ~16To disponible avec une vitesse d'écriture et de lecture multiplié par 4.

## Mirror pool ou RAID1

Ce niveau nécessite au moins 2 disques et permet de créer une redondance complète entre les deux. Ça signifie que les 2 disques contiendront exactement les mêmes données.

En particulier avec ZFS, si vous utilisez plus de 2 disques dans une Mirror pool, la redondance sera de N-1 disques (N étant le nombre total de disques).

Ce système est principalement utilisé pour stocker des données importantes qui ne sont pas très volumineuses, afin de ne pas risquer de les perdre. Ce mode fait également partie du niveau Stripe+Mirror (ou RAID10).

### Exemple

Si nous avons 4 disques de 4To, avec ce système nous aurons donc 4To disponible avec une vitesse d'écriture normale mais une vitesse de lecture 4x supérieur. Avec 4 disques, ce système nous permet de perdre jusqu'à N-1, donc 3 disques dans notre cas, sans perdre nos données.

## RAIDZ1 ou RAID5

Ce niveau permet de stocker beaucoup de données de façon fiable tout en restant relativement peu coûteux. Ce niveau nécessite au minimum 3 disques pour fonctionner.

Ce système répartit les données sur tous les disques et utilise un disque pour stocker les valeurs de parité. Si un disque tombe en panne, il est possible de reconstruire son contenu en utilisant les données des autres disques. Ce mode de stockage permet donc de perdre jusqu'à un disque physique sans perdre de données.

Ce mode est efficace, mais il faut être conscient du risque que deux disques tombent en panne simultanément, ce qui entraînerait la perte de toutes les données.

### Exemple

Si nous avons 4 disques de 4To, avec ce système nous aurons donc 12To (un disque sert de parité) disponible avec une vitesse d'écriture normale mais une vitesse de lecture pouvant être jusqu'à 3x supérieur. Ce niveau permet de perdre jusqu'à 1 disque sans perdre aucune données.

## RAIDZ2 ou RAID6

Ce niveau permet de stocker beaucoup de données de façon très fiable, mais il commence à coûter cher. Ce niveau nécessite au moins 3 disques pour fonctionner.

Ce système fonctionne exactement de la même manière que le RAIDZ1, mais il utilise 2 disques pour stocker la parité.

Cette méthode de stockage est la plus recommandée pour les homelabs ou les NAS self-hosted, car le risque de perdre 3 disques en même temps est très faible. C'est donc la solution la plus fiable et la moins coûteuse pour protéger ses données.

### Exemple

Si nous avons 4 disques de 4To, avec ce système nous aurons donc 8To (un disque sert de parité) disponible avec une vitesse d'écriture normale mais une vitesse de lecture pouvant être jusqu'à 2x supérieur. Ce niveau permet de perdre jusqu'à 2 disque en même temps sans perdre aucune données.

## RAIDZ3

C'est le même principe que le RAIDZ2, mais avec 3 disques de parité. ZFS est le seul système de fichiers à avoir développé et implémenté ce système de stockage. Ce dernier est peu (pas) utilisé car il est coûteux, étant donné qu'un minimum de 4 disques est nécessaire pour fonctionner.

Globalement, ce système de stockage n'est pas recommandé, car il n'est utile que dans de très rares cas.

## Striped+Mirror pool ou RAID10

Ce niveau permet de stocker beaucoup de données de façon fiable, mais il est très coûteux. Il nécessite au moins 2 disques pour fonctionner.

Ce système fusionne les niveaux Stripe et Mirror : plusieurs disques Mirror contiennent des données Stripe réparties sur plusieurs disques différents.

Bien que ce système puisse fonctionner avec 2 disques, il est recommandé d'avoir au moins 4 disques pour en tirer tous les avantages.

Ce système de stockage est très efficace et permet de perdre plusieurs disques simultanément sans perdre de données. Cependant, il est rarement utilisé pour les personnes qui self-hostent, car il est extrêmement coûteux.

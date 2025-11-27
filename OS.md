# Le choix de l'OS

Bien que tous les OS puissent globalement servir de NAS, certains choix sont plus confortables que d'autres. Voici une courte liste des options les plus populaires.

- [Le choix de l'OS](#le-choix-de-los)
- [Linux](#linux)
- [Proxmox](#proxmox)
- [unRAID](#unraid)
- [TrueNAS](#truenas)
- [Conclusion](#conclusion)

# Linux

"Linux" est un terme très large, car il existe un milliard de distributions différentes. En gros, par "Linux", j'entends les distributions qui ne sont pas spécifiquement conçues pour faire des NAS (comme Ubuntu, Debian, Arch, Nix, etc.). Ces distributions sont entièrement capables de servir de NAS, à condition d'y consacrer un peu de temps et d'efforts.

# Proxmox

Proxmox est un hyperviseur, c'est-à-dire (en gros) un OS conçu pour faire tourner des machines virtuelles. Comme pour Linux mentionné plus haut, si vous avez les compétences nécessaires et que vous aimez bidouiller, c'est une excellente option (c'est ce que j'utilise sur mon serveur principal). En revanche, si vous recherchez un OS simple et facile à utiliser, je ne le recommande pas.

# unRAID

[unRAID](https://unraid.net/) est un OS spécialement créé pour les NAS, mais il est payant (de $49 à $249 en one time). Cet OS supporte XFS, ZFS, BTRFS et Unraid, leur propre solution de stockage. La principale différence avec d'autres systèmes de fichiers est qu'unRAID ne fonctionne pas en considérant que tous les disques forment un seul volume, mais les sépare physiquement.

Voici une liste non exhaustive des points positifs de unRAID :
- Facile à utiliser, avec une interface intuitive
- Une documentation assez complète malgré quelques trous mais globalement ça fait largement le taff
- Une communauté très active sur Reddit, les forums officiels, etc.
- La possibilité d'utiliser des conteneurs Docker, avec une gestion via l'interface d'unRAID
- Une excellente solution pour réduire la consommation électrique (notamment grâce à leur système de "cache" utilisant des SSD)
- Des upgrades qui ajoutent généralement pas mal de trucs utiles

Cependant, y'a des défauts avec unRAID aussi :
- Ce n'est pas l'OS le plus sécurisé. Tout fonctionne en root, ce qui est un peu aberrant
- Limité en termes de nombre de disques durs possibles en fonction de la version payée (c'est une honte)
- Les performances des machines virtuelles sont médiocres, tant en termes de performances que de fonctionnement global. Ça ne vaut clairement pas un Proxmox, VirtualBox, etc.
- Le processus de déplacement des fichiers, `mover`, est lent et globalement inefficace, surtout avec le "cache" (qui n'est pas un cache dans le sens traditionnel du terme) qui mal gère cette opération
- Certaines fonctionnalités de la documentation ne fonctionnent tout simplement pas. Ce sont souvent des fonctions spécifiques qui ne sont pas utiles à tout le monde, mais c'est frustrant si vous en avez besoin
- Sauvegarder sur plusieurs appareils est incroyablement chiant et y'a pas de support officiel pour ça, ce qui est dommage lorsque l'objectif principal est de gérer du stockage
- Les mises à jour sont souvent longues à venir
- Ça boot sur une CLÉ USB 2.0 en big 2025. Ok ça sert uniquement à boot mais c'est vraiment de la merde

De manière générale, c'est un OS très bon malgré ses défauts. Les critiques que j'ai à son sujet viennent du fait qu'il pourrait être bien meilleur s'ils prenaient vraiment en compte les retours de la communauté.

# TrueNAS

[TrueNAS](https://www.truenas.com/), comme unRAID, est un autre OS conçu spécifiquement pour les NAS. La version Community qui est gratuite est excellente. Cet OS ne supporte que ZFS mais il le fait de manière exhaustive. C'est probablement l'OS le plus flexible tout en restant relativement accessible via son interface, contrairement à un Linux classique ou à Proxmox.

Voici une liste non exhaustive des points positifs de TrueNAS :
- Beaucoup plus simple qu'un Linux classique
- Excellente sécurité
- Des mises à jour régulières qui suivent de près l'évolution d'OpenZFS
- Une documentation très complète
- Une communauté extrêmement active sur Reddit, les forums officiels, etc.
- Sûrement le meilleur OS dédié aux NAS en termes de performance.
- Scalable à un niveau énorme
- Un app market complet et plutôt efficace

Cependant, y'a des défauts avec TrueNAS :
- Moins simple à utiliser qu'unRAID
- Très adapté au cold storage, mais peut être plus contraignant si ce n'est pas l'objectif principal

# Conclusion

Le choix de l'OS dépend avant tout de votre cas d'utilisation. En général, je recommanderais plutôt TrueNAS ou unRAID dans la majorité des situations, mais les distributions Linux (comme Nix ou Debian) ou Proxmox sont également d'excellentes options. Il faut cependant accepter de devoir tout gérer via la ligne de commande (ou avec une interface moche, je parle de toi Proxmox), ce qui est un peu plus complexe et moins convivial que les interfaces de TrueNAS ou unRAID.

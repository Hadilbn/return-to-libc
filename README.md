# TP noté — Return-to-libc (CyberRangeCZ / KYPO)

Ce dossier reprend exactement la structure du scénario `library-junior-hacker`
que vous utilisiez déjà (`topology.yml` + `provisioning/` Ansible +
`training.json`), adaptée au sujet SEED Labs *Return-to-libc Attack Lab*.

## Ce qui a été changé par rapport à votre exemple

| Fichier | Rôle |
|---|---|
| `topology.yml` | 1 seule machine `pwn` (l'attaque est locale, pas besoin de serveur distant) |
| `provisioning/playbook.yml` | crée l'utilisateur `student`, applique le rôle `pwn` |
| `provisioning/roles/pwn/` | installe gcc 32 bits + gdb/peda, compile `retlib` en Set-UID root, désactive l'ASLR, relie `/bin/sh` à `zsh`, dépose les fichiers du lab et les "flags" de validation |
| `provisioning/roles/pwn/files/` | `retlib.c`, `prtenv.c`, `Makefile`, squelettes d'exploit Python, script gdb batch |
| `training.json` | 1 intro + 1 accès + 5 tâches notées + 1 tâche bonus + 1 conclusion, chaque tâche se débloquant après validation de la précédente, **barème /20**, **-0.5 point par indice révélé** |

## ⚠️ Point important à vérifier avant de donner le TP aux élèves

Les **Tâches 1 et 2** ne sont **pas** notées sur la valeur exacte des
adresses mémoire (ces adresses dépendent de la build précise de l'image —
version de libc, etc. — et je n'ai pas pu les calculer moi-même dans cet
environnement). Elles sont notées sur la **commande** utilisée (`p system`,
`export MYSHELL=...`), ce qui est stable et vérifiable.

Pour les **Tâches 3, 4 et 5**, la preuve de réussite est la lecture d'un
fichier `/root/flag_taskN.txt` (classique en CTF) : le binaire `retlib` ne
peut être lu que si le dépassement de tampon a réellement fonctionné et
donné un shell root. C'est fiable et indépendant des adresses exactes.
Les chaînes de ces flags sont dans
`provisioning/roles/pwn/vars/main.yml` (`flag_task3/4/5`) — **changez-les
à chaque nouvelle session** pour éviter la réutilisation d'anciens corrigés,
et reportez les nouvelles valeurs dans `training.json` (champ `answer`).

Changez aussi `BUF_SIZE` dans `provisioning/roles/pwn/files/Makefile`
chaque année (valeur entre 10 et 800, cf. section 2.3 du sujet) : cela
change la disposition de la pile et empêche la réutilisation de corrigés
d'une promotion à l'autre.

## Avant la première utilisation en conditions réelles

1. Déployez une sandbox de test avec ce `topology.yml` et validez qu'il est
   accepté par votre plateforme (le fichier d'origine utilisait un
   `router` même pour relier un réseau ; si votre version de la plateforme
   l'exige aussi pour un hôte unique, dupliquez le bloc `routers` /
   `router_mappings` de `library-junior-hacker/topology.yml`).
2. Lancez le provisioning Ansible (`requirements.yml` puis `playbook.yml`)
   et vérifiez que `~/lab/retlib` est bien `-rwsr-xr-x root root`.
3. Suivez vous-même les Tâches 1 à 5 pour confirmer que l'exploit fonctionne
   avec le `BUF_SIZE` choisi, puis ajustez si besoin les offsets suggérés
   dans les indices.
4. Vérifiez le total des points : 3 (T1) + 3 (T2) + 6 (T3) + 2 (T3bis) +
   6 (T4) = 20, + 3 bonus (T5).

## Déverrouillage tâche par tâche

C'est le comportement natif des `TRAINING_LEVEL` CyberRangeCZ : un niveau
ne se débloque qu'après une réponse correcte au niveau précédent — vous
n'avez rien à ajouter pour obtenir le "je valide la tâche 1, je passe à la
tâche 2" que vous demandiez.

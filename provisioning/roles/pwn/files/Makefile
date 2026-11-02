# BUF_SIZE est fixé par l'enseignant pour cette session de TP (identique
# pour tous les étudiants de cette session -> adresses reproductibles,
# donc vérifiables). Changez cette valeur (10 à 800) à chaque nouvelle
# promotion pour éviter la réutilisation de corrigés d'une année sur l'autre.
BUF_SIZE=24

all: retlib prtenv
	touch badfile

retlib: retlib.c
	gcc -m32 -DBUF_SIZE=$(BUF_SIZE) -fno-stack-protector -z noexecstack -o retlib retlib.c

prtenv: prtenv.c
	gcc -m32 -o prtenv prtenv.c

# A exécuter avec sudo une fois le binaire compilé (fait aussi par Ansible
# au provisioning, mais laissé ici au cas où l'étudiant doit recompiler)
setuid:
	sudo chown root:root retlib
	sudo chmod 4755 retlib

clean:
	rm -f retlib prtenv badfile

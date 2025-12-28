MegaTP – Infra Vagrant + Ansible (Linux HA + Windows AD)

Overview

Ce dépôt contient l'infrastructure complète du TP d'administration de systèmes répartis à l'IPSSI. Il déploie 4 VMs via Vagrant : 3 Linux (admin Ubuntu, node01/node02 RHEL8 en cluster HA) et 1 Windows Server 2019 pour Active Directory. Le provisionning et hardening s'effectuent via Ansible pour Linux et Windows.



Architecture


admin (192.168.56.10) → Ansible + Zabbix base

├── node01 (192.168.56.11) → PRIMARY HA + VIP 192.168.56.100

├── node02 (192.168.56.12) → BACKUP HA (failover VIP)

└── winsrv (192.168.56.20) → Windows AD-DS + Hardening

admin : Hôte Ansible, Ubuntu Jammy, base Zabbix.



node01/node02 : RHEL8, cluster HA scripté (VIP, NTP, pages web distinctes).



winsrv : Windows Server 2019, WinRM, AD-DS installé, hardening (SMBv1/LLMNR désactivés).



Structure du Projet


MegaTP/

├── Vagrantfile          # 4 VMs (Ubuntu + 2xRHEL8 + Win2019)

├── ansible/

│   ├── inventory/hosts.yml

│   ├── site.yml         # Playbook principal

│   ├── group\_vars/all.yml

│   └── roles/

│       ├── linux-hardening/   # Firewalld, SSH, services

│       ├── cluster-ha/        # VIP, NTP, pages HA

│       ├── zabbix-server/     # Sur admin

│       └── windows-ad/        # WinRM, AD-DS, hardening

└── README.md

Prérequis

Sur machine hôte Windows :



VirtualBox installé.



Vagrant installé.



Git installé.



Déploiement


cd C:\\MegaTP

vagrant up

vagrant ssh admin

cd /vagrant/ansible

ansible -i inventory/hosts.yml all -m ping

ansible-playbook -i inventory/hosts.yml site.yml

Tests de Validation

Linux HA

Ping : ansible all -m ping → OK sur admin/nodes.



VIP : sudo /usr/local/bin/ha-vip.sh sur node01 → 192.168.56.100 active.



Failover : Testé node02, retour node01.



NTP : Stratum 2 synchronisé.



Hardening : Firewalld (cockpit, http, ssh).



Windows AD

WinRM : ansible winsrv -m win\_ping.



AD-DS installé, fichiers C:\\AD-Ready-Ansible-Hardened.txt et C:\\Hardening-Complete.txt créés.



Résultats PLAY RECAP :



Linux : ok=11/10/3, failed=0.



Windows : ok=3, failed=0.



Utilisation Avancée

Linux only : ansible-playbook site.yml --limit "admin:cluster\_nodes".



Windows only : ansible-playbook site.yml --limit windows.



Notes

HA simplifiée (scripts VIP/NTP) due aux limites de generic/rhel8.



Usage pédagogique IPSSI, local uniquement.



Git push :




git init

git add .

git commit -m "TP IPSSI complet - Linux HA + Windows AD"

git remote add origin https://github.com/TON\_USERNAME/MegaTP-IPSSI.git

git branch -M main

git push -u origin main

Remplacez TON\_USERNAME par votre GitHub.


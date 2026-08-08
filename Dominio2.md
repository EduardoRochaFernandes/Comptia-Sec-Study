# Domínio 2 — Threats, Vulnerabilities & Mitigations

> Convenção: **termo em inglês** — explicação em português.

## 2.1 — Threat Actors & Motivations

**Attributes of actors** (como se caracterizam): **Internal/External**, **Resources/Funding**, **Level of sophistication/capability**.

- **Shadow IT** — colaboradores/departamentos que usam TI (software, SaaS, servidores) **sem autorização nem conhecimento do departamento de TI** (ex.: RH a pagar uma ferramenta SaaS no cartão da empresa). *Não* é "alguém com pouco conhecimento.* Recursos internos, sofisticação baixa.
- **Unskilled attacker / Script kiddie** — poucos recursos e sofisticação; usa ferramentas de terceiros. Motivos: disruption/chaos, notoriedade.
- **Hacktivist** — recursos e sofisticação **moderados**; motivos **political/philosophical** (fazer uma declaração).
- **Insider threat** — recursos médios (acesso legítimo!); motivos: **revenge**, **blackmail**, financial gain.
- **Organized crime** — **altos** recursos e sofisticação; motivo **financial gain** (venda de dados).
- **Nation-state** — o nível **mais alto**; motivos **espionage**, **war**, service disruption. Associado a **APT (Advanced Persistent Threat)** — ataque sofisticado e persistente ao longo do tempo.

> **Ordem de sofisticação** (Sybex): Nation-state > Organized crime > Hacktivist > Shadow IT.

**Motivations** (lista do exame): **Data exfiltration**, **Espionage**, **Service disruption**, **Blackmail**, **Financial gain**, **Philosophical/political beliefs**, **Ethical** (ex.: hacker autorizado/white-hat), **Revenge**, **Disruption/chaos**, **War**. *(Ransomware associa-se a financial gain, blackmail e exfiltration — não a revenge.)*

## 2.2 — Threat Vectors & Attack Surfaces

Muitos vetores devem-se a **misconfiguration** e falta de literacia digital: dados expostos, **default credentials**, **open service ports**, protocolos desatualizados/mal configurados.

**Threat vectors por tipo:**
- **Message-based** — **Email**, **SMS**, **IM** (Slack/Discord). Mitigar IM: usar instância organizacional em vez de ferramentas públicas.
- **Image-based** — imagem com dados/código escondidos via **steganography**.
- **File-based** — o vetor file-based mais comum é **malware**.
- **Voice call** — **vishing**, tipicamente com **pretexting**.
- **Removable device** — pen/USB; explora o **autorun.inf** do Windows.
- **Vulnerable software** — **Client-based** (agente instalado, mais visibilidade mas mais superfície) vs **Agentless** (sem agente para atacar, mas menos controlo).
- **Unsupported systems/applications** — sem patches nem suporte do fabricante.
- **Unsecure networks** — **Wireless**, **Wired**, **Bluetooth**. Se não usa **WPA-2/WPA-3**, todo o tráfego é legível. Segurança máxima: **WPA-3**.
- **Open service ports** — cada porto aberto é superfície de ataque (validar por *configuration review* se não houver scanner).
- **Default credentials** — 1.ª ação a validar num router/impressora de consumo: **mudar a password default**.
- **Supply chain** — **MSP (Managed Service Provider)**, **Vendors**, **Suppliers**. Risco principal: serviços confiados a terceiros, dependências open source, gray market (não se valida a origem), hardware falso, firmware malicioso. Mitigar: **cobertura contratual** adequada.

**Human vectors / Social engineering:**
- **Phishing** — engenharia social (medo/urgência) para clicar em link ou dar dados. Difere de spam por procurar **credenciais/dados**. Detetável por **typosquatting** (`examp1e.com`).
  - **Vishing** (voz), **Smishing** (SMS), **Spear phishing** (alvo específico), **Whaling** (alvo C-level), **BEC (Business Email Compromise)** (email de "fornecedor/chefe" a pedir mudança de pagamento).
- **Impersonation** — passar-se por identidade credível (suporte, banco, estafeta); usa **pretexting** e **brand impersonation** (deepfakes possíveis).
- **Watering hole** — comprometer um site de terceiros que o alvo visita (a segurança está no terceiro, difícil de prevenir).
- **Misinformation / Disinformation** — *misinformation* (não intencional) vs *disinformation* (intencional); contas/bots falsos a espalhar factos errados.
- **Pretexting** — criar um pretexto/história para extrair informação.
- **Shoulder surfing** / **Piggybacking–Tailgating** — observar por cima do ombro / seguir alguém para uma zona restrita.

> **Defesa contra impersonation no helpdesk:** exigir que quem liga verifique a identidade com **informação não pública**.

## 2.3 — Vulnerabilities

**Application:**
- **Memory injection** — injetar código na memória de outro processo. **DLL Injection** é o método clássico (substitui/aponta o DLL para um infetado). *Defesa:* usar **caminhos totalmente qualificados** para DLLs.
- **Buffer overflow** — falta de validação do tamanho: enviar mais dados do que a variável comporta. Pode causar **crash (DoS)** mas também **execução de código/privilege escalation** (não é só DoS). *(Integer overflow: valor de 64 bits numa variável de 4 bytes.)*
- **Race conditions** — dois eventos em simultâneo que o programa não devia permitir. **TOC (Time-of-Check)** → **TOU (Time-of-Use)**: verifica e usa um valor desatualizado (ex.: saldo bancário). **TOE (Target of Evaluation)** é o alvo avaliado. *Defesa:* **file locking** (bloquear o recurso até terminar).
- **Malicious update** — atualização infetada; confirmar **digital signature** compatível com a do fabricante e fazer backups.

**Operating system (OS)-based** — alvo comum (muito código = mais vulnerabilidades). Remediar vuln de kernel = **patch do OS** (não da aplicação).

**Web-based:**
- **SQL Injection (SQLi)** — injetar SQL (`OR 1=1--`) para ler/alterar a base de dados. IPS bloqueia `'` e `OR 1=1`.
- **Cross-Site Scripting (XSS)** — injetar JavaScript que outros utilizadores vão ver. **Non-persistent/reflected** (na query de um link) vs **Persistent/stored** (HTML/JS guardado e renderizado na página).
- **Directory traversal** — `../../../etc/passwd` (por vezes com null byte `%00`) para ler ficheiros fora da pasta web.
- **CSRF (Cross-Site Request Forgery)** Alvo: Servidor — abusa da **confiança do site no utilizador autenticado** (cookies de sessão) para executar ações em nome dele.
- **SSRF (Server-Side Request Forgery)** Alvo: Utilizador — abusa da confiança **do servidor**: faz o próprio servidor pedir URLs internos. *Defesa:* bloquear hostnames/localhost (`127.0.0.1`), URLs sensíveis (`/admin`) e usar allow lists. *(Remover SQL das queries NÃO ajuda no SSRF.)*

**Hardware:**
- **Firmware** — software básico, com menos camadas de segurança. *Defesas do fabricante:* firmware **assinado**, encriptado, com code review (input validation não é defesa de firmware por si).
- **EOL (End-of-Life)** — deixa de ser produzido, mas ainda com updates. **EOSL (End-of-Service-Life)** — totalmente sem suporte/updates.
- **Legacy** — dispositivos EOSL numa empresa; alvos frequentes (baixa segurança, esquecidos). *Ação:* isolar/segmentar e planear substituição.

**Virtualization:**
- **VM escape** — sair da VM e chegar ao **hypervisor** (todas as VMs).
- **Resource reuse** — uma VM/volume novo traz **dados antigos** de outro tenant. *Defesa:* **encriptar volumes na criação**.

**Cloud-specific** — qualquer pessoa com Internet pode tentar aceder; principais riscos: **misconfigurations** (ex.: security groups mal geridos no AWS), **insecure APIs**. Aplicam-se directory traversal, RCE, SQLi, etc.

**Supply chain (providers)** — **Service provider**, **Hardware provider** (hardware/firmware malicioso, OS images adulteradas), **Software provider**. SaaS: maior risco = **comprometimento do fornecedor → acesso aos teus dados**.

**Cryptographic** — algoritmos/protocolos fracos (ver ataques cripto em 2.4).

**Misconfiguration** — ex.: regra de firewall `ALLOW FROM ANY:TCP 80 TO ANY:ANY` no topo do ruleset.

**Mobile device:**
- **Jailbreaking** — **iOS/iPhone** (ex.: instalar o gestor **Cydia**).
- **Rooting** — **Android**.
- **Sideloading** — instalar apps fora da store oficial (possível após jailbreak/root). *Prevenir:* **MDM (Mobile Device Management)** — ex.: Intune, Jamf.

**Zero-day** — vulnerabilidade que **o fabricante ainda não conhece** (sem patch disponível).

## 2.4 — Indicators of Malicious Activity

### Malware
- **Virus** — anexa-se a ficheiros legítimos; **requer ação humana** (executar o ficheiro). Tipos: application, boot sector, script, **macro** (ficheiros Office), **fileless** (PowerShell/memória).
- **Worm** — **propaga-se sozinho** por vulnerabilidades de rede, **sem ação humana**; pode instalar mais malware.
- **Ransomware** — encripta os dados e exige resgate (cripto); o OS continua a funcionar para mostrar a mensagem. *Defesa principal:* **backups offline**.
- **Trojan** — parece legítimo mas instala malware; a vítima instala voluntariamente. **RAT (Remote Access Trojan)** — dá controlo/administração remota ao atacante.
- **Rootkit** — instala-se no **kernel**, tornando-se parte do OS → **invisível** a AV tradicional. *Defesa:* **Secure Boot (UEFI)** valida a assinatura do OS no arranque.
- **Keylogger** — regista as teclas (passwords, dados bancários). Pode derrotar MFA capturando o código introduzido.
- **Spyware** — monitoriza atividade/browsers e envia ao atacante.
- **Bloatware** — software pré-instalado indesejado (toolbars, apps); geralmente **inofensivo**, mas consome recursos.
- **Logic bomb** — código dormente até uma **condição** (data/evento); tipicamente de um insider, sem assinatura AV, difícil de detetar. *Defesa:* monitorizar ficheiros críticos, least privilege, separação de funções.
- **Botnet** — rede de dispositivos infetados sob um **C2 (Command & Control)** (ex.: via **IRC**, porto 6667), usada para DDoS/spam.

### Physical attacks
- **Brute force** (físico — forçar entrada), **RFID/Badge cloning** (clonar crachá para herdar privilégios; PIN adicional mitiga), **Environmental** (atacar o ambiente: energia, clima — detetar com **sensores de temperatura**).

### Network attacks
- **DoS** — tornar um recurso indisponível **a partir de uma origem** (ataque deliberado).
- **DDoS** — massivo, via **botnet**; frequentemente **UDP** (sem validação como TCP).
  - **Amplified/Reflected** — enviar pequenos pedidos com **IP da vítima falsificado (spoofed)** a resolvers/serviços abertos que devolvem respostas enormes à vítima (ex.: DNS amplification).
- **DNS attacks** — **DNS poisoning** (envenenar cache/servidor), **Domain hijacking** (mudar registo do domínio no registrar), **DNS hijacking** (redirecionar resolução).
- **Wireless** — **deauthentication attack** (envia frames de deauth ao MAC da vítima), **RF jamming** (ruído), **evil twin / rogue AP** (AP falso). **Bluetooth**: **Bluejacking** (enviar mensagens não pedidas) vs **Bluesnarfing** (roubar dados).
- **On-path / Man-in-the-Middle** — atacante no meio da comunicação. **ARP poisoning** (associa o MAC do atacante ao gateway — indício: **ARP table** com endereço alterado do gateway). **Man-in-the-Browser** (malware rouba cookies).
- **Credential replay** — capturar e **reenviar** credenciais; associado a **on-path** e ao protocolo **NTLM** (NTLM relay). *Defesa:* encriptação e nonce/salt diferente por autenticação.
- **Replay / Session hijacking (sidejacking)** — roubar e reutilizar **cookies de sessão**. *(Sidejacking ≠ sideloading.)*

### Application attacks
- **Injection**, **Buffer overflow**, **Replay**, **Forgery** (CSRF/SSRF), **Directory traversal** e **Privilege escalation**:
  - **Vertical** — ganhar privilégios superiores (ex.: root a partir de user).
  - **Horizontal** — aceder a recursos de outro utilizador do mesmo nível (ex.: `su` para um colega).

### Cryptographic attacks
- **Downgrade** — forçar cifra/protocolo mais fraco (ou plaintext) para ler o tráfego. *Defesa:* impedir **TLS fallback**.
- **Collision** — dois ficheiros com o **mesmo hash** e conteúdos diferentes.
- **Birthday** — usa o paradoxo do aniversário (23 → 50%) para **encontrar colisões** de hash (modificar um documento malicioso até o hash bater com o legítimo).

### Password attacks
- **Spraying** — poucas passwords comuns contra **muitas contas** (evita lockout).
- **Brute force (offline)** — rouba o ficheiro de hashes (ex.: via SQLi) e tenta quebrá-lo offline. Alvos: **Windows SAM** e **Linux /etc/shadow**.
- **Dictionary attack** — lista de passwords comuns. **Rainbow table** — tabelas pré-computadas de hashes (derrotadas por **salt**).

### Indicators (IoCs)
- **Account lockout** — bloqueio por tentativas falhadas (rever *authentication logs*).
- **Concurrent session usage** — sessões simultâneas (suspeito quando em **dois locais ao mesmo tempo**).
- **Impossible travel** — logins em geografias impossíveis no intervalo (ver `/var/log/auth.log`).
- **Blocked content** — tentativas repetidas de aceder a conteúdo bloqueado (ex.: **DNS blackhole**).
- **Resource consumption** — uso anómalo (disco/CPU/tráfego de saída).
- **Resource inaccessibility** — recurso indisponível (indício típico de **DoS**).
- **Out-of-cycle logging** — atividade/logs fora do horário/ciclo normal.
- **Missing logs** — logs apagados pelo atacante (ex.: `audit.log` vazio) → configurar alertas para ausência de logs.
- **Published/documented** — IoCs de **threat feeds** para integrar no SIEM.

## 2.5 — Mitigation Techniques

Mitigação reduz o **impacto**; hardening reduz a **superfície de ataque**.

- **Segmentation** — dividir a rede (ex.: **VLANs**) para limitar a superfície e isolar sensibilidades (least privilege ao nível do sistema).
- **Access control** — **ACL (Access Control List)** (permitir/negar por porto, protocolo, IP) e **permissions**. *(Permissões `777` = open/weak permissions.)*
- **Application allow list** — só corre o que está listado (máximo controlo; instalar exige aprovação/admin). Oposto: **deny list**.
- **Isolation** — isolar de imediato um sistema comprometido para não afetar outros.
- **Patching** — corrigir vulnerabilidades (1.º passo perante uma CVE: **ver se há patch**).
- **Encryption** — objetivo principal = **confidentiality**; protege dados em disco (**BitLocker** full-disk, **EFS** file-level), em trânsito (**transport encryption**) e ficheiros por email (**file-level**).
- **Monitoring** — logs + **SIEM** (logging centralizado para ambientes grandes).
- **Least privilege** — cada utilizador só com os privilégios necessários (limita o que um atacante exfiltra).
- **Configuration enforcement** — impor configuração (ex.: **Intune**, **Jamf** via MDM).
- **Decommissioning** — retirar legacy devices: **destruir/limpar drives** e depois **remover do inventário**.

**Hardening techniques:**
- **Encryption** (roubo de portátil), **Endpoint protection / EDR** (analisa um endpoint; **XDR** vê a rede toda; quarentena + restauro), **Host-based firewall** (camada 3-4: portos/protocolos/IPs; melhor proteção em redes não confiáveis), **HIPS** (previne no host) vs **HIDS** (só deteta/alerta) — via **signatures**, **anomalies**, **heuristics**, **Disabling ports/protocols** (ex.: **Telnet/23**, reduzir footprint), **Default password changes**, **Removal of unnecessary software** (reduz superfície **e** patch management).

**Outros tipos de firewall/IPS:**
- **NGFW (Next-Gen Firewall)** — camada 7, inspeciona conteúdo com contexto da aplicação.
- **WAF (Web Application Firewall)** — protege apps web contra XSS/SQLi.
- **IPS vs IDS** — IPS deteta **e bloqueia**; IDS só deteta/alerta. IPS **inline/ativo** (bloqueia) vs **passivo/out-of-band** (copia o tráfego, analisa e alerta, como um IDS).
- **UTM (Unified Threat Management)** — junta várias destas funções num só dispositivo.

**Termos de análise:** **IoC (Indicators of Compromise)**, **OSINT**, **predictive analysis** (ML sobre dados históricos), **threat maps**, **posture assessment** (estado dos dispositivos).
1.	EDR, NAO PREVINE CONTRA MALWARE APENAS BEHAVIOUYR  PREDICTIVE ANALYSIS. Analisa tráfego de rede de um único endpoint usa machine learning e predictive analysis para detetar tipos de ataques que o sistema ainda não enfrentou. Coloca o sistema em quarentena e retorna-o para o backup mais recente. XDR vê a rede toda e múltiplos endpoints e suas redes
2.	HIPS e HIDS, o primeiro analisa alterações nas configurações de OS, aplicações e registry keys. O segundo não ativamente para, apenas deteta e alerta. Detetam via anomalias e signatures (padroes conhecidos) e heuristic (caracteres de um código ou ficheiro). Ambos funcionam a nível de tráfego de rede de um único dispositivo (daí o “H” de host).
3.	Host-Based Firewall, camada de segurança na camada 4 de TCP/IP que analisa todo o tráfego na rede baseado em portos, protocolos e IPs
4.	NGFW opera na camada 7 e analisa o conteúdo mas de acordo com o contexto da aplicação inteira
5.	WAF (Web Application Firewall) funciona como um IPS em aplicações web contra XSS e SQLi
6.	IPS e IDS, ambos têm como objetivo filtrar e detetar alertas na rede inteira. No entanto, o primeiro deteta e bloqueia a ameaça, enquanto o segundo apenas a deteta e alerta o sistema. Nota: um IPS pode ter dois tipos de monitorização: ativa/inline, onde para se detetar anomalia, e passiva/out of band, onde cria uma cópia do tráfego, analisa-o, e alerta o sistema (tal como um IDS) se vir uma anomalia.
ures

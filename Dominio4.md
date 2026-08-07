# Domínio 4 — Security Operations

> Convenção: **termo em inglês** — explicação em português. (Baseado no teu resumo; corrigi o essencial e acrescentei o que faltava dos objetivos 4.1–4.9.)

## 4.1 — Security Techniques

### Secure baselines & hardening
**Security baseline** — conjunto de configurações de segurança aplicadas no deployment (firewalls, patches, etc.). Origem típica: **CIS Benchmarks** (configurações recomendadas). Ciclo: **Establish → Deploy → Maintain**. *Depois de escolher o benchmark, o passo seguinte é **rever** o baseline e afastar o que não serve à organização.*
- **SCAP (Security Content Automation Protocol)** — permite que ferramentas diferentes usem os **mesmos nomes** para a mesma vulnerabilidade/config (alinha com NIST 800-53).
- **Hardening targets:** mobile, workstations, switches, routers, cloud, servers, ICS/SCADA, embedded, RTOS, IoT. *(Se não há vulns conhecidas, o hardening que mais compensa num router é **desativar serviços desnecessários**.)*
- **ICS/SCADA e legacy sem patches** → hardening por **isolation/segmentation** (não há add-on security). **IoT/embedded** têm **opções de hardening muito limitadas**.

### Wireless deployment
- **Site survey** + **heat map** — análise física do ambiente RF para ver cobertura e interferências (fazer **primeiro**).
- **Mobile deployment models:**
  - **BYOD (Bring Your Own Device)** — o utilizador traz o seu; **menos controlo / mais escolha** para o utilizador.
  - **COPE (Corporate-Owned, Personally Enabled)** — a empresa compra (modelo específico), permite uso pessoal razoável.
  - **CYOD (Choose Your Own Device)** — escolher de uma **lista aprovada**; a empresa é dona e gere centralmente.
- **MDM (Mobile Device Management)** — gere apps/dados; features: **remote wipe**, **geofencing** (impedir uso fora do campus), **content/application management**, **containerization / storage segmentation** (isolar dados corporativos), **FDE** (full-disk encryption).

### Wireless security settings
- **Open** (nenhuma) · **WPA3 Personal** — passphrase, mas usa **SAE (Simultaneous Authentication of Equals)** em vez do handshake PSK do WPA2 (é isto que o torna mais seguro). **WPA3 Enterprise** — autenticação por **auth server** via **AAA/RADIUS** + **802.1X**; cifra **AES**.
- **Connection methods:** Cellular (proteger com **VPN**), Wi-Fi, Bluetooth (risco: **bluejacking**, fingerprinting).

### Application security
- **Input validation** — validar tipos/tamanhos/ranges no **servidor de confiança** (não confiar no cliente).
- **Secure cookies** — atributo Secure = cookie só enviado por **HTTPS**.
- **Static code analysis (SAST)** — analisa o **código-fonte** (revisão manual = static).
- **Dynamic analysis (DAST) / Fuzzing** — envia muitos inputs à **app em execução** para achar falhas/DoS.
- **Code signing** — output = **hash assinado** do software com a **chave privada** do autor.
- **Sandboxing** — testar malware/código num ambiente **isolado**.
- **Ambientes:** Development → Test → **Staging** (idêntico ao live, último antes de produção) → Production.

## 4.2 — Asset Management
- **Acquisition/procurement** — comprar do **OEM** (não gray market) para evitar dispositivos adulterados; para open source, **verificar dependências**.
- **Assignment/accounting** — **Ownership** (dono) e **Classification** (classificar os dados).
- **Monitoring / asset tracking** — **Inventory** (sem inventário não se sabe se falta algum sistema, nem se os scans são exatos), **Enumeration** (port scans enumeram ativos), **asset tag** (barcode/RFID).
- **Disposal / decommissioning** — **Sanitization** (**quick-format NÃO** sanitiza), **Destruction** (SSD → destruição física para garantia total; **degauss só funciona em magnético**), **Certification** (certificate of destruction de um terceiro), **Data retention** (quanto tempo se guarda).

## 4.3 — Vulnerability Management
### Identification
- **Vulnerability scan** — **authenticated scans** dão a **maior visibilidade** (> unauthenticated/port scan). Ferramentas: **Nessus/OpenVAS**.
- **Application security:** **Static analysis**, **Dynamic analysis**, **Package monitoring** (dependências).
- **Threat feed:** **OSINT** (público), **Proprietary/third-party** (pago/vetado), **Information-sharing organization** (ex.: ISAC/CTA), **Dark web** (via TOR).
- **Penetration testing** — simula e **explora**; começa por **exploration/exploitation** → **lateral movement** → **persistence** (backdoors) → **proxy attack**. Segue **rules of engagement**.
- **Responsible disclosure / Bug bounty** — reportar privadamente seguindo **rules of engagement** ; **bug bounty** paga por vulnerabilidades reportadas.
- **System/process audit.**

### Analysis
- **Confirmation:** **False positive** (reporta vuln que não existe — ex.: já corrigida) vs **False negative** (não reporta vuln que existe).
- **CVSS (Common Vulnerability Scoring System)** — score **0–10**; três grupos de métricas: **Base**, **Temporal**, **Environmental**. O **impact metric** (no Base) é composto por **Confidentiality, Integrity, Availability**. *(3.0 = severidade **baixa**.)*
- **CVE (Common Vulnerabilities and Exposures)** — o **identificador/lista** da vulnerabilidade (é um **vulnerability feed**). *CVSS pontua; CVE identifica.*
- **Exposure factor (EF)** — **% do valor do ativo** perdido no evento. **Environmental variables**, **industry/organizational impact**, **Risk tolerance** (tempo que a empresa aceita conviver com a falha). Para priorizar com a realidade da organização → **CVSS com environmental metrics**.

### Response & remediation
- **Patching**, **Segmentation**, **Compensating controls** (ex.: mover para VLAN protegida + jump server quando não há patch), **Insurance** (transferência de risco — **NÃO** é controlo técnico), **Exceptions** (exceção à regra) e **Exemptions** (dispensa oficial — ex.: servidor que crasha ao ser scaneado → documentar exemption + compensating controls).
- **Validation of remediation:** **Rescanning**, **Audit** (auditor terceiro para atestar), **Verification**.
- **Reporting.**

## 4.4 — Alerting & Monitoring
- **Activities:** **Log aggregation**, **Alerting**, **Scanning**, **Reporting**, **Archiving**, **Alert response** (→ **Quarantine**: mover ficheiro para local seguro; **Alert tuning**: reduzir falsos alertas/ruído).
- **Tools:** **SCAP**, **Benchmarks** (CIS), **Agents vs Agentless** (24/7 vs só em ações como login), **SIEM** (agrega, **correlaciona** e reporta; dashboards de **trends**, alerts), **Antivirus**, **DLP**, **Vulnerability scanners**.

## 4.5 — Enterprise Capabilities
- **Firewall** — regras por lista (mais específicas primeiro) com porto; o que não corresponde é **implicit deny**. **Screened subnet** = firewall com 3 interfaces (Internet / web servers / rede interna). *No exemplo do teu print: allow SSH 22, HTTP 80, HTTPS 443, RDP 3389, DNS, NTP; **deny ICMP/ping** — atenção ao RDP 3389 aberto (deve ser restringido).*
- **IDS/IPS** — por **signatures** (precisa do código do ataque, ex.: SQLi), **trends** e **anomalies** (para ataques novos/desconhecidos).
- **Web filter** — **URL scanning** (dados mais atuais), **content categorization**, **block rules**, **reputation**, **agent-based** ou **centralized proxy**.
- **DNS filtering** — o servidor bloqueia domínios maliciosos (rápido para travar phishing por URL).
- **OS security** — **Group Policy** (deploy de baseline em toda a empresa Windows), **Active Directory** (base de dados central); **DAC** = comportamento padrão do Linux/Windows filesystem.
- **Secure protocols (substituições):** **Telnet→SSH (22)**, **HTTP→HTTPS (443)**, **IMAP→IMAPS**, **FTP→SFTP**, **LDAP→LDAPS (636)**. Envolver um protocolo inseguro em **TLS** para o proteger em trânsito.
- **Email security** — **Gateway** (deteta email legítimo/phishing antes da entrega). **SPF** (TXT no DNS que diz que servidores podem enviar pela tua conta), **DKIM** (assinatura com **chave privada** do remetente, verificada com a pública), **DMARC** (combina SPF+DKIM e diz o que fazer quando falham).
- **FIM (File Integrity Monitoring)** — deteta alterações a ficheiros que raramente mudam (processos de sistema, bibliotecas).
- **DLP (Data Loss Prevention)** — bloqueia transferência de dados sensíveis (data in use no endpoint, in motion na rede, at rest no OS; USB/cloud/email). *Requer **classificar/tag** os dados primeiro.*
- **NAC (Network Access Control)** — só deixa ligar dispositivos que cumprem (patches/AV); **agent-based** dá mais detalhe. *Bypass: **MAC cloning**.*
- **EDR / XDR** — deteção/resposta por **behavior + ML** num endpoint (EDR) ou correlacionando vários (**X**DR); dashboard para ransomware.
- **UEBA (User and Entity Behavior Analytics)** — ML sobre o comportamento do utilizador para detetar anomalias.
- **SNMP (Simple Network Management Protocol)** : Trata-se de um protocolo que monitoriza diferentes dispositivos. Para escolher um vai-se ao “menu” MIB, depois escolhe-se o dispositivo via uma espécie de “barcode” OID. No porto 161, o dispositivo informa o router das suas métricas nos últimos minutos de x em x tempo. Já no porto 162, existem regras configuradas para que, se algo anormal ocorrer, o dispositivo ignore esse período e avise logo.
- **Netflow**: Ao contrário de uma host-based firewall que monitoriza e bloqueia em tempo real diferentes alertas num OS e num dispositivo em específico, o netflow apenas alerta sobre dados monitorizados da rede que corre num router/switch (ou seja na rede inteira).
  
## 4.6 — Identity & Access Management (IAM)
IAM = dar as permissões certas às pessoas certas, do **onboarding ao offboarding**. **Interoperability** = boa junção destes processos.
- **Provisioning / De-provisioning** — dar/remover acessos (aplicar **least privilege**). *Novo colaborador: provisionar pelo **role**, nunca copiar de outra pessoa (evita **privilege creep**).*
- **Identity proofing** — verificar quem é o utilizador: **Resolution** (inicial) → **Validation** (password/perguntas) → **Attestation** (documento oficial: **passaporte**, cartão de cidadão). *Conta Google/Facebook **não** serve para proofing.*
- **Federation** — aceder a recursos via terceiro ("login via Facebook/Google"); usa **SAML token**.
- **SSO (Single Sign-On)** — um login para vários sistemas.
  - **LDAP (Lightweight Directory Access Protocol)** — lê grandes diretórios (AD), baseado no X.500; organiza em árvore (**DIT**) e caminhos (**DN**). AD é a base de dados; LDAP lê-a. **LDAPS = porto 636**.
  - **OAuth** — framework de **autorização** (não autenticação); define que recursos o utilizador tem.
  - **SAML (Security Assertion Markup Language)** — standard **XML** para trocar **autenticação**.
  - **OpenID Connect** — autenticação sobre OAuth (login com Google/Microsoft na cloud).
- **Attestation** — confirmar/validar a posse de uma identidade.
- **Access controls:**
  - **MAC (Mandatory)** — o admin classifica recursos por **label** (Confidential/Secret); o mais rígido.
  - **SELinux** - (MAC no Linux)
  - **DAC (Discretionary)** — o **dono** do recurso define quem lê/escreve (padrão do Linux/Windows).
  - **RBAC (Role-Based)** — permissões pelo **cargo/role**; escalável.
  - **Rule-Based** — regras definidas pelo admin com base em **hora, IP, localização, dispositivo**.
  - **ABAC (Attribute-Based)** — decide por **atributos** (utilizador, recurso, ambiente — ex.: localização geográfica; pode rejeitar login por local errado).
  - **Time-of-day restrictions** e **Least privilege**.
- **MFA:**
  - **Factors:** **Something you know** (password/PIN), **Something you have** (token, SMS, USB key), **Something you are** (biometria), **Somewhere you are** (IP/GPS). *PIN + password = **1 fator** (ambos "know").*
  - **Implementations:** **Biometrics**, **Hard/soft tokens**, **Security keys**. Segurança: **hardware token > app token > SMS** (SMS é o mais fraco; static codes → risco de **theft**).
- **Password concepts:** **Length** (maior impacto contra brute-force), **Complexity**, **Reuse** (impedido por **password history**), **Expiration/Age** (**minimum age** impede ciclar passwords). **Password managers**. **Passwordless** — **Windows Hello**, **FIDO2 security key** (um PIN sozinho **não** é passwordless).
- **PAM (Privileged Access Management):** **Just-in-Time (JIT) permissions**, **Password vaulting** (check-out de passwords root em emergência, com registo e reset após uso), **Ephemeral credentials**.

## 4.7 — Automation & Orchestration
Usar scripting/automação para operações seguras.
- **Use cases:** **User/resource provisioning**, **Guard rails**, **Security groups** (firewall virtual na cloud), **Ticket creation**, **Escalation**, **Enabling/disabling services**, **CI/CD & testing**, **APIs** (proteger com **autenticação** e **API keys** — limitam e registam uso por cliente).
- **Benefits:** **Efficiency/time saving**, **Enforcing baselines** (consistência entre sistemas), **Standard configs**, **Secure scaling**, **Employee retention**, **Reaction time**, **Workforce multiplier**.
- **Other considerations:** **Complexity**, **Cost**, **Single point of failure**, **Technical debt**, **Ongoing supportability** (scripts que envelhecem mal).

## 4.8 — Incident Response
**Processo (7 fases):** **Preparation** (kit, playbooks, sandboxes) → **Detection** → **Analysis** → **Containment** (limitar o dano: **isolation/quarantine**) → **Eradication** (remover a causa — ex.: **wipe + reinstalar** de media conhecida) → **Recovery** (repor operação; adicionar regras de firewall/patches) → **Lessons learned**.
- **Training** e **Testing:** **Tabletop exercise** (falar/discutir o cenário à mesa, menos disruptivo) e **Simulation** (ações reais de recuperação sem impactar produção).
- **Root cause analysis (RCA):** técnicas **Five Whys** e **Fishbone/Ishikawa diagram**.
- **Threat hunting** — procurar proativamente indícios (ex.: contas criadas fora de horas).
- **Digital forensics (RFC 3227):**
  - **Legal hold** — o jurídico ordena preservar dados (o **data custodian** guarda a **ESI** num repositório à parte); não apagar apesar dos ciclos normais.
  - **Chain of custody** — trilho documental (apreensão, custódia, transferência, análise, disposição) de **quem acedeu, quando e o quê**; gravar vídeo da remoção do disco documenta **chain of custody + provenance**.
  - **Order of volatility** — recolher do **mais volátil ao menos volátil**: registos CPU/cache → RAM → tráfego de rede → disco local → media removível → **backups** (menos volátil).
  - **Acquisition** — trabalhar sempre em **cópias**; aquisição **live** para dispositivos que se bloqueiam ao desligar; VM VMware → **snapshot** com ferramentas nativas (não `dd`). Validar com **hash** (imagem = original).
  - **Preservation**, **Reporting** (nonrepudiation → **assinar digitalmente** os registos), **E-discovery** (recolher/produzir documentos eletrónicos, só aquisição).
- **Retention policy** define quanto tempo os logs existem (distinta de legal hold/backup/classification).

## 4.9 — Data Sources (investigação)
- **Log data:** **Firewall logs**, **Application logs** (Windows Event Viewer; **application log** = erros de software instalado), **Endpoint logs**, **OS-specific security logs** (Linux: `/var/log/secure` no RedHat, `/var/log/auth.log` no Debian/Ubuntu — falhas de autenticação), **IPS/IDS logs** (melhores para **DDoS**), **Network logs**, **Metadata** (autor de ficheiro Office → metadados; origem de email → **headers**).
- **Data sources:** vulnerability scans, automated reports, **dashboards**, **packet captures** (Wireshark).
- **Ferramentas:** `grep -c 'evento' ficheiro` (contar ocorrências), **timelining** (depende de relógios corretos), **Cuckoo** (sandbox de malware), **MITRE ATT&CK** (táticas/técnicas de adversários, sem ordem fixa).

## Acrónimos (Domínio 4)
- **SCAP** — Security Content Automation Protocol
- **CIS** — Center for Internet Security (benchmarks)
- **CVE / CVSS** — Common Vulnerabilities and Exposures / Common Vulnerability Scoring System
- **SAST / DAST** — Static / Dynamic Application Security Testing
- **OSINT** — Open-Source Intelligence
- **ISAC** — Information Sharing and Analysis Center
- **MDM** — Mobile Device Management
- **BYOD / COPE / CYOD** — Bring Your Own / Corporate-Owned Personally Enabled / Choose Your Own Device
- **WPA3 / SAE** — Wi-Fi Protected Access 3 / Simultaneous Authentication of Equals
- **RADIUS** — Remote Authentication Dial-In User Service
- **EAP** — Extensible Authentication Protocol (EAP-TLS, PEAP, EAP-PWD)
- **802.1X** — Port-Based Network Access Control
- **SIEM** — Security Information and Event Management
- **UEBA** — User and Entity Behavior Analytics
- **SNMP** — Simple Network Management Protocol (v3 = seguro)
- **NetFlow / IPFIX / sFlow** — protocolos de flow/bandwidth
- **DLP** — Data Loss Prevention
- **FIM** — File Integrity Monitoring
- **NAC** — Network Access Control
- **EDR / XDR** — (Extended) Endpoint Detection and Response
- **SPF / DKIM / DMARC** — email authentication frameworks
- **LDAP(S)** — Lightweight Directory Access Protocol (Secure, 636)
- **SAML / OAuth / OIDC** — Security Assertion Markup Language / Open Authorization / OpenID Connect
- **SSO** — Single Sign-On
- **MAC / DAC / RBAC / ABAC** — Mandatory / Discretionary / Role- / Attribute-Based Access Control
- **MFA** — Multi-Factor Authentication
- **PAM** — Privileged Access Management (JIT, vaulting, ephemeral)
- **FIDO2** — passwordless security key standard
- **CI/CD** — Continuous Integration / Continuous Delivery
- **API** — Application Programming Interface
- **RCA** — Root Cause Analysis
- **ESI** — Electronically Stored Information
- **RFC 3227** — guia de recolha de evidência (order of volatility)
- **MITRE ATT&CK** — matriz de táticas/técnicas de adversários

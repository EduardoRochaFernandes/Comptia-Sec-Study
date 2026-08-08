# Domínio 3 — Security Architecture

> Convenção: **termo em inglês** — explicação em português. (Baseado no teu resumo; só corrigi o essencial e acrescentei o que faltava.)

## 3.1 — Architecture Models

### Cloud Infrastructure
Modelo muito usado pela capacidade de armazenamento e facilidade de acesso. Grandes tipos:
1. **Hybrid cloud** — >1 provider em simultâneo a comunicar. *Concern principal (Sybex): aumento de complexidade + desafios de jurisdição/data sovereignty.*
2. **IaC (Infrastructure as Code)** — a infraestrutura é criada via **código** (não manualmente na AWS/Azure), permitindo automação e deploy/replace de VMs em escala. **IMPORTANTE**: *Boas práticas: **version control** e **artifact signing** (evitar adulteração); remediar vuln = atualizar a imagem base e reinstanciar.*
3. **Serverless** — pagas só pelo tempo de execução; **não precisa de administrador**. Não é ideal para apps complexas; num DDoS de exaustão de recursos, a preocupação-chave é o **custo**.

**Provider** — indivíduo/empresa que fornece recursos para a cloud. **Responsibility matrix** (quem gere o quê):
- **SaaS** — provider gere tudo **exceto as contas** (cliente).
- **PaaS** — provider gere tudo **exceto aplicações e contas**; partilhado: OS, aplicações, dados (o provider é responsável pela **application security** da plataforma).
- **IaaS** — provider gere **hardware/datacenters/redes/hosts físicos**; cliente gere o resto (OS, apps, dados, contas).
- **On-premises** — o cliente gere tudo.
- **Third-party vendors** do CSP: mitigam-se **através do contrato com o próprio CSP**.

**Formas de alojar a app:**
- **Monolithic** — bloco inteiro; para escalar uma parte, escala tudo.
- **Microservices** — serviços **loosely coupled**, de propósito específico, via protocolos leves; maior flexibilidade de recursos e segurança; ideal para escalar transações sem dependências.

### Network Infrastructure
- **Air gap (isolamento físico)** — isolamento absoluto, sem qualquer ligação (ex.: +1 cliente = +1 switch). Máxima segurança para ICS/SCADA e backups (evita malware).
- **SDN (Software-Defined Networking)** — centraliza o controlo para escalar. Três planos:
    - **Management Plane** (admin configura políticas),
    - **Control Plane** (o "cérebro", decide o encaminhamento)
    - **Data Plane** (infraestrutura física que reencaminha).
    - *O mais crítico a proteger é o **SDN controller**.*
- **VLANs (isolamento lógico)** — segmenta um único switch em várias redes virtuais isoladas; escala muito melhor que air gap. VMs não comunicam salvo se permitido por router/firewall.
- **Centralized vs. decentralized** — descentralizado dá **maior resiliência** mas **maior complexidade** (ex.: escritórios regionais + vários SaaS = modelo decentralized).

### Outros tipos de infraestrutura
- **On-premises vs Cloud** — on-prem: hardware/espaço local (CapEx); cloud: alojada por terceiro, subscrição/pagamento por uso (**OpEx**).
- **Virtualization vs Containerization** — na virtualização, o **hypervisor** abstrai o hardware e cada **VM** tem o seu **Guest OS** completo; na containerização, o motor (ex.: **Docker**) corre sobre o **Host OS** e **partilha o kernel**, isolando só a app + dependências (leve, sem OS dedicado). *Ataque ao hypervisor = **VM escape**.*
- **IoT** — dispositivos com sensores/conectividade; risco elevado (hardware limitado, software desatualizado, ignorados na monitorização). Hardening mais eficaz: **baseline de configuração padrão** + zona de segurança separada. Riscos comuns: insecure communication, data leaks, vulnerabilidades (ransomware não é típico).
- **SCADA / ICS (Industrial Control Systems)** — monitorização/controlo de grandes indústrias (refinarias, centrais). Proteção mais eficaz contra ataques de rede: **isolamento físico** (ou VLAN separada); grande preocupação de longo prazo: **inability to patch**.
- **RTOS (Real-Time Operating System)** — prioriza a **rapidez/determinismo** da tarefa (ex.: travagem de um carro) em vez de UX. Segurança: **firmware seguro** (não se instalam ferramentas de segurança).
- **Embedded systems** — hardware + software num só, com um **propósito específico** (semáforos, equipamento médico). Limitações: **compute** (CPU/memória), sem bulk storage, **inability to patch**, longa vida útil, não configuráveis pelo utilizador.
- **High availability (HA)** — manter sistemas disponíveis apesar de falhas: **redundante** (backup pode precisar de config manual), **HA** (backup assume de imediato) ou **Active-Active HA** (ambos ativos, mais caro). *1.º passo num design HA: **identificar single points of failure**.*

**Considerations** de uma infraestrutura: 
  - **Availability** (% de tempo sem falhas),
  - **Resilience**,
  - **Responsiveness**,
  - **Scalability** (**Vertical** = mais recursos; **Horizontal** = mais máquinas),
  - **Elasticity** (provisiona/desprovisiona automaticamente conforme a carga — evita over/underprovisioning),
  - **Cost**,
  - **Ease of deployment**,
  - **Risk transference** (ex.: seguro / suporte de terceiros),
  - **Ease of recovery**,
  - **Patch availability / Inability to patch**,
  - **Power**,
  - **Compute**.

## 3.2 — Secure Enterprise Infrastructure

### Security zones & placement
Ter o máximo de **security zones** para segmentar dispositivos/redes com níveis de acesso diferentes e reduzir a **attack surface**. Fluxo típico: **untrusted zone** (público) — *firewall 1* — **screened zone/subnet** (acesso público controlado: web/email servers) — *firewall 2* — **trusted zone** (só certos grupos: bases de dados, diretórios).
- **Device placement** — onde se colocam os dispositivos (ex.: clusters redundantes **não** no mesmo rack).
- **Connectivity** — evitar single points: **path diversity** e **dois ISPs** distintos.
- **Attack surface** — avalia-se com port scan / OSINT / **penetration testing** (o mais completo para superfície exposta).

### Failure modes
- **Fail-open** — tudo continua a funcionar (prioriza disponibilidade); preferido para **taps in-line**.
- **Fail-closed** — tudo para quando algo falha (prioriza segurança; escolhe-se se preferires downtime a falta de monitorização).

### Network appliances
- **Load balancer** — distribui tráfego; se um servidor falha, redistribui. Tem **um único VIP (Virtual IP)** (o IP que os clientes veem). Algoritmos:
  - **Round-robin** (um a um),
  - **Weighted response time** (pelo tempo de resposta),
  - **Least connection** (menos ligações ativas),
  - **(Weighted) least connection** (com rating do servidor),
  - **Source IP hashing** (usa o IP do cliente),
  - **Session persistence / Sticky** (mesmo cliente -> mesmo servidor). *Não cobre **risk transference**.*
- **Clustering** — vários servidores respondem **como se fossem o mesmo dispositivo** (distinto de load balancing).
- **IPS/IDS monitoring:** **Inline / Active NIPS** (filtra, deteta e **bloqueia** antes de chegar), **Passive / Out-of-band** (recebe cópia do tráfego, deteta e alerta, como um IDS). Um **IDS** é sempre **passivo**. Deteção por **signatures**, **heuristics** e **anomalies** (esta última apanha comportamentos de APT após baseline).
- **Network / Physical tap** — Passivo. hardware inserido entre switch e router que copia o sinal; se falhar, a ligação continua.
- **Port mirror / SPAN (Switch Port Analyzer)** — Semi-Passivo (no exame, se network tap e port mirror aparecerem, o tap é que é o passivo). Funcionalidade do switch que copia tráfego para monitorização passiva.
- **Sensors** — recolhem dados (incl. **environmental sensors** para o estado do datacenter).
- **Jump server** — aceder remotamente via **SSH/RDP** a uma rede interna protegida (host intermédio).
- **Proxy server** — intermediário cliente<->internet/servidores. Tipos: **Explicit** (configurado no OS), **Transparent** (invisível, interceta), **Open** (gerido por terceiros), **Forward** (interno -> internet), **Reverse** (internet -> interno). *(Vários clientes com o mesmo IP público -> proxy.)*

### Port security & NAC
- **Port security** — filtra por **MAC address** (que máquinas podem ligar-se a um porto Ethernet).
- **802.1X** (Port-Based NAC) — antes de autenticar, o porto **só deixa passar tráfego EAP**. Fluxo: **Supplicant** tenta aceder -> **EAP** pede a identidade e envia ao **Auth Server** -> pede username/password -> supplicant fornece -> Auth Server verifica. Usado no **WPA3-Enterprise**; pode atribuir **VLAN por role**.
- **NAC** - Serve para verificar se um dispositivo está legível ou não de entrar numa rede privada
- **EAP** (Extensible Authentication Protocol) — linguagem entre supplicant e Auth Server: **EAP-TLS** (certificado nos **dois** lados), **PEAP** (certificado só do lado do **servidor**), **LEAP / EAP-MD5** (sem verificação forte).

### Firewall types
- **Layer 4 firewall / host-based** — portos, protocolos, IPs.
- **NGFW (Next-Generation Firewall)** — **Layer 7**, inspeciona conteúdo com contexto da aplicação; usa **application awareness**, **deep packet inspection** e **threat feeds** (para novos ataques sem criar regras).
- **WAF (Web Application Firewall)** — protege apps web contra XSS/SQLi; ideal para reagir depressa a **zero-days** de SQLi (ex.: filtrar aspas simples). Manter regras atuais via **threat feed**.
- **UTM (Unified Threat Management)** — junta firewall + IDS/IPS + AV + DLP + MDM num só (**SD-WAN não** é função de UTM).

### Secure communication / access
- **VPN** — encripta tráfego privado e envia-o por uma rede pública.
- **IPSec VPN** — **Layer 3 (rede)**; liga duas redes/empresas (site-to-site).
    - **AH (Authentication Header)** autentica o pacote mas **não encripta**;
    - **ESP (Encapsulating Security Payload)** autentica **e encripta** (mais usado);
    - **IKE (Internet Key Exchange)** protocolo que estabelece as **SAs (Security Associations)** que AH/ESP usam;
    - **Tunnel mode** — encripta o **pacote inteiro** e adiciona novo IP header (site-to-site). Toma decisões baseado no IP.
    - **Transport mode** — encripta **apenas o payload** e **mantém o IP header original** visível (usado quando a política depende do porto/header). *Fornece confidentiality, authentication e integrity — não availability.*
- **SSL/TLS VPN** — **Layer 7**; ligação remota fácil (browser-based), sem instalar cliente. Aplicar **TLS em todo o lado**. **Nunca usar SSL** (inseguro).
- **IPSec vs TLS** — no IPSec o dispositivo fica **com o IP da empresa** (rede completa); no TLS acede-se só à app e navega-se remotamente.
- **SD-WAN (Software-Defined WAN)** — Encaminha os utilizadores diretamente à cloud, NÃO protege tráfego (em vez de passar pelo datacenter); gere **MPLS, broadband, LTE** (não Wi-Fi).
- **SASE (Secure Access Service Edge)** — **SD-WAN na cloud** + segurança extensiva (**zero trust**, **CASB**, firewall) a substituir VPNs tradicionais.

## 3.3 — Data Protection

### Data states & considerations
- **Data at rest** — armazenados em disco/drive (proteção: **encryption**).
- **Data in transit / motion** — a fluir na rede (proteção: **TLS/VPN**; *disk encryption não protege em trânsito*).
- **Data in use** — a ser processados na RAM/CPU (proteção: **secure enclave** e controlo de acesso; **hashing não serve**).
- **Data sovereignty** — as leis aplicáveis aos dados dependem da **localização** onde estão.
- **Geolocation** — impacta as questões de data sovereignty; base das restrições geográficas.

### Data types
- **Regulated** — regulado por terceiros/lei (ex.: **GDPR, HIPAA, FERPA, GLBA, PCI DSS**).
- **Trade secrets** — informação proprietária interna (ex.: modelos de análise, listas de clientes).
- **Intellectual property** — ativos intangíveis criados pela empresa (designs, conceitos), protegidos legalmente.
- **Legal information** — ex.: contratos, **NDAs**, **SOWs**.
- **Financial information** — nºs de conta, cartões, faturas (privado/sensível).
- **Proprietary data / PII / PHI** — dados da organização / que identificam um indivíduo / de saúde (regulados pela **HIPAA**).
- **Human-readable vs Non-human-readable** — texto vs binário/QR/código de barras (híbrido: código de barras com número por baixo).

### Data classifications
**Sensitive** (privado) < **Confidential** (mais privado) · **Public/Unclassified** · **Private/Restricted** (requer permissões) · **Critical** (deve estar sempre disponível). *("Top Secret" é classificação governamental, não de negócio.)*

### Methods to secure data
- **Encryption** (melhor para data at rest), **Hashing** (fingerprint de sentido único, para integridade — **não reversível**), **Masking** (ocultar caracteres, ex.: `****-****-1234`; **vulnerável a client-side tampering**), **Tokenization** (substituir por token sem significado, mapeado ao original, **não reversível sem o esquema**), **Obfuscation** (guarda-chuva: masking/encryption/tokenization), **Segmentation** (agrupar dados por caso de uso/sensibilidade e aplicar controlos diferentes), **Geographic restrictions** (geofencing — **GPS + Wi-Fi**; MDM para móveis), **Permission restrictions**.

## 3.4 — Resilience & Recovery

### High availability
- **Load balancing vs Clustering** — LB distribui carga; clustering = servidores a agir como um só.
- **Fault tolerance vs HA** — **Fault tolerance** é desenhado para **evitar por completo** interrupções (redundância total); **HA** **minimiza** interrupções.
- **Platform diversity** — usar **vários fabricantes** (ex.: Cisco + Juniper) para que uma vuln de um não derrube tudo (custo: **complexidade de gestão**).
- **Multi-cloud** — vários fornecedores de cloud (maior proteção contra outage de um vendor; > que multi-AZ ou multi-region).

### Site considerations
- **Hot site** — cópia exata em tempo real, assume de imediato.
- **Warm site** — hardware/rede montados, pronto a receber dados.
- **Cold site** — espaço vazio, só com utilities (eletricidade/Internet).
- **Geographic dispersion** — sites/servidores geograficamente dispersos (ajuda a **availability**; considerar se o pessoal consegue chegar/foi afetado pelo desastre).

### Continuity & capacity
- **Continuity of operations (COOP)** — plano para perda de acesso a instalações, perda de pessoal e perda de serviços.
- **Capacity planning** — garantir capacidade adequada para a procura: **People** (cross-training), **Technology** (load balancers, storage para logs, **compute**), **Infrastructure** (datacenters diversos). Cloud ajuda.

### Testing (do menos ao mais disruptivo)
- **Tabletop exercise** — discussão guiada de papéis e ações (**menos** disruptivo).
- **Simulation** — simular um evento real (notificações, procedimentos, comunicações). NÃO deita abaixo o sistema pra ver se ele volta a funcionar. Isso seria Fail Over.
- **Parallel processing** — processar em paralelo no sistema secundário sem causar outage (testa redundância sem "partir" nada).
- **Fail over** — **forçar** a passagem para o sistema de backup para ver se assume (mais disruptivo).

### Backups
- **On-site vs Off-site** — disco/pen local vs cloud (off-site: retrieval pode atrasar a recuperação; pode ser afetado pelo mesmo desastre se demasiado perto).
- **Frequency** — a **RPO** define a frequência (RPO de 6h -> backup <= 6/6h).
- **Snapshots** — versão pontual (útil para reverter VM/malware); inclui disco/memória/power state, **não** a config do hypervisor. Mais usado para VMs.
- **Replication** — cópia **contínua** (síncrona/assíncrona) dos dados vivos; **não precisa de restauro** (ao contrário dos backups, que são periódicos e precisam de restauro).
- **Journaling** — regista alterações para restaurar até ao momento da falha (**pouca ou nenhuma perda** de dados); custo = **tempo** de restauro.
- **Full / Differential / Incremental:**
  - **Full** — copia tudo.
  - **Differential** — copia tudo o que mudou **desde o último full** (restauro = full + **1** differential).
  - **Incremental** — copia o que mudou **desde o último backup** (full **ou** incremental); restauro = full + **todos** os incrementais.
- **Encryption** — backups cifrados: garantir que a **chave de recuperação** é recuperável no desastre.

### Power
- **UPS (Uninterruptible Power Supply)** — energia temporária (curtas falhas/undervoltage). Tipos: **offline/standby**, **line-interactive**, **online/double-conversion**. Falhas: **blackout**, **brownout** (parcial/temporária), **surge** (excesso).
- **Generators** — falhas **prolongadas** (preocupações: combustível, manutenção, capacidade). HA ideal: **UPS + generators**.

### Objetivos de recuperação (em falta no teu resumo)
- **RPO (Recovery Point Objective)** — quanto **dado** se pode perder (-> define a frequência de backup).
- **RTO (Recovery Time Objective)** — **quanto tempo** pode demorar a recuperação.
- **MTTR (Mean Time To Repair/Recover)** — tempo médio para reparar/repor.
- **MTBF (Mean Time Between Failures)** — tempo médio entre falhas.

## Correções feitas ao teu resumo
- **Transport mode** — cifra **só o payload** (mantém o IP original); **não** cifra o pacote inteiro (isso é o **tunnel mode**).
- **Differential** = desde o último **full** (restauro = full + 1); **Incremental** = desde o último backup (restauro = full + todos os incrementais).
- **Failover test** = forçar a passagem para o backup (não "ver se os outros também falham").

## Distratores comuns (revisão rápida)
- **RPO** = dados perdidos; **RTO** = tempo de recuperação.
- **Transport mode** = só payload; **Tunnel mode** = pacote inteiro + novo IP.
- **AH** autentica (não cifra); **ESP** cifra + autentica; **IKE** cria as SAs.
- **IPSec** = Layer 3 (fica com IP da empresa); **TLS VPN** = Layer 7 (browser, fácil).
- **UPS** = curtas; **Generator** = prolongadas.
- **Hot** (imediato) / **Warm** (hardware pronto) / **Cold** (só utilities).
- **Data in use** -> **secure enclave** (não hashing).
- **Masking** é vulnerável a client-side tampering; **tokenization** não é reversível sem o esquema.
- **Port security** = MAC; **802.1X** = autenticação (deixa passar só EAP antes).
- **SD-WAN** gere MPLS/broadband/LTE (não Wi-Fi); **SASE** = SD-WAN + ZT + CASB + FW.
- **Fault tolerance** evita por completo; **HA** minimiza.

## Acrónimos (Domínio 3)
- **IaC** — Infrastructure as Code
- **SaaS / PaaS / IaaS** — Software / Platform / Infrastructure as a Service
- **SDN** — Software-Defined Networking
- **SD-WAN** — Software-Defined Wide Area Network
- **SASE** — Secure Access Service Edge
- **CASB** — Cloud Access Security Broker
- **ICS / SCADA** — Industrial Control System / Supervisory Control and Data Acquisition
- **RTOS** — Real-Time Operating System
- **IoT** — Internet of Things
- **HA** — High Availability
- **VLAN** — Virtual LAN
- **NAC** — Network Access Control
- **EAP** — Extensible Authentication Protocol (EAP-TLS, PEAP, LEAP, EAP-MD5)
- **VIP** — Virtual IP
- **NIPS / NIDS** — Network Intrusion Prevention/Detection System
- **SPAN** — Switch Port Analyzer
- **WAF / NGFW / UTM** — Web App Firewall / Next-Gen Firewall / Unified Threat Management
- **VPN** — Virtual Private Network
- **IPSec** — Internet Protocol Security (AH, ESP, IKE, SA)
- **TLS / SSL** — Transport Layer Security / Secure Sockets Layer
- **MPLS** — Multiprotocol Label Switching
- **PII / PHI** — Personally Identifiable / Protected Health Information
- **HIPAA / GDPR / FERPA / GLBA / PCI DSS** — regulações de dados
- **NDA / SOW** — Non-Disclosure Agreement / Statement of Work
- **RPO / RTO** — Recovery Point / Time Objective
- **MTTR / MTBF** — Mean Time To Repair / Between Failures
- **UPS / PDU** — Uninterruptible Power Supply / Power Distribution Unit
- **COOP** — Continuity of Operations Plan
- **AZ** — Availability Zone

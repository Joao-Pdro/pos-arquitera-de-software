# Modelo OSI

## O que é

O **OSI (Open Systems Interconnection)** é um modelo de referência da ISO que divide a comunicação em rede em **7 camadas**. Cada camada tem uma função e só conversa com a camada de cima e a de baixo. Serve para entender, ensinar e diagnosticar problemas ("em qual camada está a falha?").

## As 7 camadas

| Nº | Camada       | Função                                                                 | PDU (unidade de dados) | Exemplos                       |
|----|--------------|------------------------------------------------------------------------|------------------------|--------------------------------|
| 7  | Aplicação    | Interface entre o usuário/programa e a rede.                           | Dados                  | HTTP, DNS, SMTP, FTP, DHCP     |
| 6  | Apresentação | Formato dos dados: tradução, compressão e criptografia.                | Dados                  | TLS/SSL, JPEG, ASCII           |
| 5  | Sessão       | Abre, mantém e encerra o diálogo entre duas aplicações.                | Dados                  | NetBIOS, RPC                   |
| 4  | Transporte   | Comunicação **fim a fim**: segmentação, controle de fluxo e confiabilidade. | Segmento (TCP) / Datagrama (UDP) | TCP, UDP          |
| 3  | Rede         | **Endereçamento lógico (IP)** e **roteamento** entre redes.            | Pacote                 | IP, ICMP, roteadores           |
| 2  | Enlace       | Comunicação entre dispositivos na mesma rede local, com endereço **MAC**. | Quadro (frame)      | Ethernet, Wi-Fi, switches      |
| 1  | Física       | Transmissão dos **bits** pelo meio (cabo, fibra, rádio).               | Bits                   | Cabos, hubs, sinais elétricos  |

Para decorar de cima para baixo (7 → 1): **A**ll **P**eople **S**eem **T**o **N**eed **D**ata **P**rocessing (Aplicação, Apresentação, Sessão, Transporte, Rede, Enlace, Física).

## Quem cuida do IP e da confiabilidade

Pergunta de prova comum: *"quais camadas gerenciam os IPs e garantem a transferência de dados confiável?"* A resposta é **3 – Rede e 4 – Transporte**.

- **Camada 3 (Rede):** cuida dos endereços IP e do roteamento dos pacotes. Ela entrega "na melhor tentativa" (*best effort*), **sem garantir** que o pacote chegue.
- **Camada 4 (Transporte):** garante a confiabilidade com **confirmação (ACK)**, **retransmissão**, **ordenação** e **controle de fluxo**. É onde o TCP trabalha.

Por que as outras combinações estão erradas:

- **Enlace + Rede:** o Enlace usa MAC, não IP, e só garante a entrega entre vizinhos diretos, não fim a fim.
- **Transporte + Sessão:** a Sessão controla o diálogo entre aplicações e não tem relação com IP.
- **Física + Transporte:** a Física só move bits, sem endereçamento.

## TCP x UDP (camada 4)

| Característica | TCP                                   | UDP                              |
|----------------|---------------------------------------|----------------------------------|
| Conexão        | Orientado a conexão (*three-way handshake*) | Sem conexão                |
| Confiabilidade | Confirma, retransmite e ordena        | Não garante nada                 |
| Velocidade     | Mais lento (tem sobrecarga)           | Mais rápido                      |
| Uso típico     | Web, e-mail, transferência de arquivos | Streaming, VoIP, jogos, DNS     |

---

# Modelo TCP/IP

## O que é

É o modelo que a internet realmente usa. Tem **4 camadas** e é mais enxuto que o OSI: as camadas 5, 6 e 7 do OSI viram uma só, e as camadas 1 e 2 também.

## As 4 camadas

| Nº | Camada TCP/IP                      | Função                                        | Protocolos                  | Equivale no OSI |
|----|------------------------------------|-----------------------------------------------|-----------------------------|-----------------|
| 1  | **Aplicação**                      | Serviços usados pelos programas.              | HTTP, HTTPS, DNS, SMTP, FTP | 5, 6 e 7        |
| 2  | **Transporte**                     | Comunicação fim a fim, confiável ou não.      | TCP, UDP                    | 4               |
| 3  | **Internet**                       | Endereçamento IP e roteamento.                | IP, ICMP, ARP*              | 3               |
| 4  | **Host/Rede** (Acesso à Rede)      | Acesso ao meio físico e entrega no enlace.    | Ethernet, Wi-Fi             | 1 e 2           |

\* O ARP costuma ser colocado entre a camada de Internet e a de Host/Rede, dependendo do autor.

Numerando do topo para baixo: **1 – Aplicação, 2 – Transporte, 3 – Internet e 4 – Host/Rede**. A camada 4 se chama **Host/Rede**, também chamada de **Acesso à Rede** ou **Interface de Rede**.

> **Pegadinhas:** *Apresentação* e *Enlace* são nomes do **OSI**, não do TCP/IP. *TCP* é um **protocolo** da camada de Transporte, não o nome de uma camada.

## OSI x TCP/IP lado a lado

```
        OSI                       TCP/IP
 7  Aplicação      ─┐
 6  Apresentação    ├──────►   Aplicação
 5  Sessão         ─┘
 4  Transporte     ────────►   Transporte
 3  Rede           ────────►   Internet
 2  Enlace         ─┐
 1  Física         ─┴──────►   Host/Rede
```

- O **OSI** é teórico, usado como referência e para ensino.
- O **TCP/IP** é o modelo prático, em que os protocolos da internet foram construídos.

---

# Clusters de computadores

## O que é

Um **cluster** é um conjunto de computadores (chamados **nós**) que trabalham juntos e aparecem para o usuário como **um único sistema**. Os objetivos principais são **alta disponibilidade**, **desempenho** e **escalabilidade**.

## Tipos de cluster (quanto ao funcionamento dos nós)

| Tipo               | Como funciona                                                                  | Característica principal                    |
|--------------------|--------------------------------------------------------------------------------|---------------------------------------------|
| **Stand-Alone**    | Um único servidor, sem cluster.                                                | Sem redundância: se cair, o serviço para.   |
| **Ativo-Ativo**    | Todos os nós trabalham ao mesmo tempo, com a carga dividida (*load balancing*). | Melhor aproveitamento dos recursos.        |
| **Ativo-Passivo**  | Um nó atende tudo e o outro fica **em espera (standby)**, assumindo só se o ativo falhar. | Simples, com **failover** automático. |
| **Container**      | Agrupa contêineres (Docker) em vários hosts, orquestrados por ferramentas como Kubernetes. | Escala e recuperação automáticas. |

## Ativo-Ativo x Ativo-Passivo

```
Ativo-Ativo:                      Ativo-Passivo:

  Cliente                           Cliente
     │                                 │
 balanceador                        Nó ATIVO  ◄── atende tudo
   ┌──┴──┐                              │ (falhou?)
 Nó A   Nó B  ◄── os dois atendem   Nó PASSIVO ◄── só assume no failover
```

| Critério                  | Ativo-Ativo                          | Ativo-Passivo                         |
|---------------------------|--------------------------------------|---------------------------------------|
| Uso dos recursos          | Alto (todos trabalham)               | Baixo (o passivo fica ocioso)         |
| Complexidade              | Maior (sincronizar estado e carga)   | Menor                                 |
| Se um nó falhar           | Os outros absorvem a carga (pode sobrecarregar) | O passivo assume no lugar     |
| Custo por capacidade útil | Menor                                | Maior (paga-se por um nó parado)      |

## Conceitos relacionados

- **Failover:** troca automática para o nó reserva quando o principal falha.
- **Load balancing (balanceamento de carga):** distribuição das requisições entre os nós.
- **Alta disponibilidade (HA):** manter o serviço no ar mesmo com falhas de componentes.
- **Heartbeat:** sinal periódico entre os nós para detectar se o outro ainda está vivo.

> **Resumo da questão:** *dois nós, um sempre funcionando e o outro só ativado se o principal falhar* → **Cluster Ativo-Passivo**.

---

# Zero Trust

## O que é

**Zero Trust (Confiança Zero)** é um modelo de segurança baseado no princípio **"nunca confie, sempre verifique"** (*never trust, always verify*). Estar dentro da rede corporativa **não** dá confiança automática: todo acesso precisa ser autenticado, autorizado e validado.

## Segurança de perímetro (modelo antigo)

No modelo tradicional, a segurança fica na "borda" da rede (firewall, VPN):

- **Fora da rede** = não confiável.
- **Dentro da rede** = confiável, com acesso amplo e sem validações adicionais.

```
Perímetro tradicional:               Zero Trust:

 ┌────── rede interna ──────┐         cada acesso é verificado,
 │  tudo confia em tudo     │         mesmo dentro da rede
 └──────────┬───────────────┘
        firewall (única barreira)
```

**O problema:** se um invasor passa pelo perímetro (credencial roubada, malware, funcionário mal-intencionado), ele se move **lateralmente** pela rede com muita liberdade.

## Princípios do Zero Trust

| Princípio                    | O que significa                                                                |
|------------------------------|--------------------------------------------------------------------------------|
| **Verificar explicitamente** | Autenticar e autorizar com base em identidade, dispositivo, localização e contexto. |
| **Privilégio mínimo**        | Dar só o acesso necessário e pelo tempo necessário (*least privilege*).        |
| **Assumir a violação**       | Projetar como se o invasor já estivesse dentro: segmentar e monitorar.         |
| **Verificação contínua**     | Revalidar durante a sessão, não só no login.                                   |

## Práticas comuns

- **MFA** (autenticação multifator).
- **Microssegmentação** da rede, para limitar o movimento lateral.
- **Gestão de identidade e acesso (IAM)** e acesso condicional.
- Checagem de **saúde do dispositivo** antes de liberar o acesso.
- **Monitoramento e logs** contínuos.

## Por que as outras opções da questão estão erradas

A pergunta descreve uma organização que "concede acesso amplo só porque o usuário está dentro da rede". As alternativas erradas são justamente variações dessa lógica:

- **Segurança baseada apenas em MAC:** identificação fraca, e o MAC é fácil de falsificar (*spoofing*).
- **Autorização implícita, sem verificação de identidade:** é confiar sem verificar, o oposto do Zero Trust.
- **Acesso irrestrito por perímetro interno:** é a confiança baseada em localização que o Zero Trust combate.

> **Resumo da questão:** modelo que se opõe a "estar dentro da rede = confiável" → **Zero Trust**.

---

# Resumo rápido

- **OSI:** 7 camadas (Física, Enlace, Rede, Transporte, Sessão, Apresentação, Aplicação).
- **TCP/IP:** 4 camadas (Aplicação, Transporte, Internet, **Host/Rede**).
- **IP e roteamento:** camada **3 (Rede)**. **Confiabilidade fim a fim:** camada **4 (Transporte)**.
- **Cluster Ativo-Passivo:** um nó trabalha, o outro espera para assumir (failover).
- **Cluster Ativo-Ativo:** todos os nós trabalham juntos, com balanceamento de carga.
- **Zero Trust:** "nunca confie, sempre verifique", com privilégio mínimo e verificação contínua, o oposto da confiança baseada no perímetro.

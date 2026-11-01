# Básico de IPv4

## O que é

O **IP (Internet Protocol)** identifica e localiza um dispositivo em uma rede. O IPv4 é a versão 4 do protocolo e funciona como o "endereço" de uma máquina: sem ele, os pacotes não sabem para onde ir.

## Estrutura do endereço

- Tem **32 bits**, divididos em **4 octetos** de 8 bits.
- É escrito em **notação decimal pontuada**: `192.168.1.10`.
- Cada octeto vai de **0 a 255** (2⁸ = 256 valores).
- Total de endereços possíveis: 2³² = **4.294.967.296** (cerca de 4,3 bilhões). Isso não bastou para todos os dispositivos do mundo, e daí vêm o NAT, os IPs privados e o IPv6.

```
Decimal:  192      .  168      .  1        .  10
Binário:  11000000 .  10101000 .  00000001 .  00001010
          octeto 1    octeto 2    octeto 3    octeto 4
```

## Convertendo binário em decimal

Cada bit de um octeto vale uma potência de 2. Soma-se o peso das posições com bit `1`:

| Posição | 1   | 2  | 3  | 4  | 5 | 6 | 7 | 8 |
|---------|-----|----|----|----|---|---|---|---|
| Peso    | 128 | 64 | 32 | 16 | 8 | 4 | 2 | 1 |

Exemplos:

```
11000000 = 128 + 64                = 192
10101000 = 128 + 32 + 8            = 168
00001010 = 8 + 2                   = 10
11111111 = 128+64+32+16+8+4+2+1    = 255
```

Para o caminho inverso (decimal → binário), pergunte para cada peso, da esquerda para a direita, "cabe no que sobrou?". Se cabe, é `1` e subtrai; se não, é `0`.

## Rede e host

Todo endereço IPv4 tem duas partes:

- **Parte de rede:** identifica a rede em que o dispositivo está.
- **Parte de host:** identifica o dispositivo dentro dessa rede.

Quem diz onde termina uma parte e começa a outra é a **máscara de sub-rede** (assunto da próxima seção). Em `192.168.1.10/24`, os 24 primeiros bits são a rede (`192.168.1`) e os 8 últimos são o host (`10`).

## Endereços especiais dentro de uma rede

Em qualquer rede, dois endereços **não podem ser atribuídos** a dispositivos:

| Endereço          | Bits de host | Função |
|-------------------|--------------|--------|
| Endereço de rede  | todos `0`    | Identifica a própria rede (ex.: `192.168.1.0`). |
| Broadcast         | todos `1`    | Alcança todos os hosts da rede (ex.: `192.168.1.255`). |

Por isso o número de hosts utilizáveis é `2^(bits de host) − 2`.

## Tipos de comunicação

- **Unicast:** de um host para outro host específico.
- **Broadcast:** de um host para todos da rede local.
- **Multicast:** de um host para um grupo de hosts (faixa `224.0.0.0` a `239.255.255.255`).

## Conceitos que acompanham o IP

| Conceito        | O que é |
|-----------------|---------|
| **Máscara**     | Separa a parte de rede da parte de host. |
| **Gateway padrão** | IP do roteador, por onde o tráfego sai da rede local para outras redes. |
| **DNS**         | Traduz nomes (`google.com`) em endereços IP. |
| **DHCP**        | Atribui IP, máscara, gateway e DNS automaticamente aos dispositivos. |
| **IP estático** | Configurado manualmente (comum em servidores e impressoras). |
| **IP dinâmico** | Recebido via DHCP e pode mudar com o tempo. |
| **IP público**  | Roteável na internet, único no mundo. |
| **IP privado**  | Usado só dentro de redes locais, e o NAT o traduz para um público na saída. |

---

# Máscaras de sub-rede

Um endereço IPv4 possui 4 blocos (octetos) separados por ponto, cada um indo de 0 até 255. Cada octeto tem 8 bits (32 no total), e o valor é calculado com operações binárias.

A máscara define quantos bits do início pertencem à rede (fixos). Os bits restantes pertencem aos hosts e podem variar.

Exemplo com `/24` (24 bits de rede, 8 bits de host):

```
|------ rede (fixos) ------|  |- hosts -|
11000000.10101000.00000001.  00000000
   192  .   168  .    1   .     0
```

- `/24` = `255.255.255.0`
- Hosts possíveis: 2⁸ − 2 = 254 (exclui o endereço de rede e o de broadcast)

## Exemplo com `/28` (28 bits de rede, 4 bits de host)

```
|------- rede (fixos) -------|   |hosts|
11000000.10101000.00000001.0000  0000
   192  .   168  .    1   .  0
```

- `/28` = `255.255.255.240` (último octeto: `11110000`)
- Endereços por sub-rede: 2⁴ = 16 (salto de 16 em 16)
- Hosts utilizáveis: 16 − 2 = 14

Sub-redes dentro de `192.168.1.0`:

| Sub-rede         | Primeiro host | Último host | Broadcast     |
|------------------|---------------|-------------|---------------|
| 192.168.1.0/28   | 192.168.1.1   | 192.168.1.14  | 192.168.1.15  |
| 192.168.1.16/28  | 192.168.1.17  | 192.168.1.30  | 192.168.1.31  |
| 192.168.1.32/28  | 192.168.1.33  | 192.168.1.46  | 192.168.1.47  |
| ...              | ...           | ...         | ...           |
| 192.168.1.240/28 | 192.168.1.241 | 192.168.1.254 | 192.168.1.255 |

Ao todo são 16 sub-redes (2⁴), pois 4 bits do último octeto foram "emprestados" para a rede.

---

# Classes de IPv4

## IPv4 privado (IANA — iana.org)

A IANA reservou faixas de endereços que **não são roteadas na internet pública**. Qualquer rede (casa, empresa, escola) pode reutilizá-las internamente. Para sair para a internet, o tráfego passa por **NAT**, que troca o IP privado por um IP público.

| Classe | Máscara de sub-rede | IPs por rede | Range de IP                   |
|--------|---------------------|--------------|-------------------------------|
| A      | 255.0.0.0 /8        | 16.777.214   | 10.0.0.0 a 10.255.255.255     |
| B      | 255.255.0.0 /16     | 65.534       | 172.16.0.0 a 172.31.255.255   |
| C      | 255.255.255.0 /24   | 254          | 192.168.0.0 a 192.168.255.255 |

### Como ler a tabela

A máscara da classe diz quantos bits são de **rede** e quantos sobram para **hosts**. O número de IPs por rede vem de `2^(bits de host) − 2` (descontando o endereço de rede e o de broadcast):

| Classe | Bits de rede | Bits de host | Cálculo      | Hosts      |
|--------|--------------|--------------|--------------|------------|
| A      | 8            | 24           | 2²⁴ − 2      | 16.777.214 |
| B      | 16           | 16           | 2¹⁶ − 2      | 65.534     |
| C      | 24           | 8            | 2⁸ − 2       | 254        |

```
Classe A:  |rede|-------- hosts --------|
           00001010.00000000.00000000.00000000   (10.x.x.x)

Classe B:  |------ rede ------|---- hosts ----|
           10101100.00010000.00000000.00000000   (172.16.x.x)

Classe C:  |--------- rede ---------|-- hosts --|
           11000000.10101000.00000000.00000000   (192.168.x.x)
```

### Quando usar cada uma

- **Classe A (10.0.0.0/8):** redes muito grandes, como grandes empresas e provedores. Dá para dividir em muitas sub-redes menores (veja `/28` acima).
- **Classe B (172.16.0.0/12):** redes de médio porte, como campus e filiais.
- **Classe C (192.168.0.0/16):** redes pequenas, como roteador doméstico (`192.168.0.x` ou `192.168.1.x`) e escritórios.

> **Atenção ao range da classe B:** o bloco privado vai só de `172.16.0.0` até `172.31.255.255`. Isso equivale a um `/12`, e não a um `/16` inteiro. `172.15.x.x` e `172.32.x.x` são IPs **públicos**. O `/16` da tabela é a máscara padrão da classe, e dentro do bloco cabem 16 redes `/16` (`172.16` a `172.31`).
> O mesmo vale para a classe C: o bloco privado `192.168.0.0/16` contém 256 redes `/24`.

## Endereços reservados

| Bloco          | Uso                    | Explicação |
|----------------|------------------------|------------|
| `0.0.0.0/8`    | Rota padrão / "esta rede" | `0.0.0.0` significa "qualquer endereço". Na tabela de rotas, a rota padrão (default route) é escrita `0.0.0.0/0`: o que não tem rota mais específica vai por ela (normalmente o gateway). Como origem, `0.0.0.0` é usado por um host que ainda não tem IP (ex.: pedido DHCP). |
| `127.0.0.0/8`  | Loopback               | Aponta para a **própria máquina**. O mais usado é `127.0.0.1` (`localhost`). O tráfego nunca sai pela placa de rede, e serve para testar serviços locais (`ping 127.0.0.1`). |
| `169.254.0.0/16` | Link-local (APIPA)   | IP privado **atribuído automaticamente** quando o host não consegue um IP por DHCP. Só funciona dentro do mesmo segmento de rede e não é roteado. Se você vê `169.254.x.x` na sua máquina, normalmente é sinal de que o DHCP falhou. |

> Na imagem o link-local aparece como `168.254.0.0/16`. O correto é **`169.254.0.0/16`**.

## Resumo rápido

- Privados: `10.0.0.0/8`, `172.16.0.0/12`, `192.168.0.0/16`.
- Loopback: `127.0.0.0/8`.
- Link-local (sem DHCP): `169.254.0.0/16`.
- Rota padrão: `0.0.0.0/0`.
- Fora disso, o IP é público e roteável na internet.

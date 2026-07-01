# Implementação do Protocolo Go-Back-N (GBN) sobre UDP

Trabalho da disciplina de Redes de Computadores, com implementação de um protocolo de transporte confiável (Go-Back-N) sobre sockets UDP em Java, incluindo transmissão de arquivos, simulação de perda de pacotes, coleta de métricas e geração de relatório técnico.

## Descrição do projeto

O projeto implementa um Emissor e um Receptor que se comunicam via UDP e utilizam o algoritmo Go-Back-N para garantir a entrega confiável de um arquivo. O Emissor lê o arquivo de origem, o transmite em pacotes numerados sequencialmente e retransmite pacotes em caso de perda ou timeout. O Receptor recebe os pacotes, simula perda de pacotes de acordo com uma probabilidade configurável, reconstrói o arquivo recebido e valida a integridade dos dados por meio de hash MD5.

Principais características implementadas:

- Comunicação exclusivamente via sockets UDP nativos.
- Máquina de estados do Go-Back-N (janela deslizante, base, próximo número de sequência, buffer circular).
- Handshake inicial entre Emissor e Receptor para sincronização da sessão.
- Temporizador de retransmissão com reenvio em lote de todos os pacotes não confirmados.
- Simulação de perda de pacotes no Receptor, com probabilidade configurável.
- Verificação de integridade do arquivo transferido via hash MD5.
- Encerramento controlado da sessão por meio de pacote FIN.
- Script de bateria de testes automatizados, variando tamanho de janela e probabilidade de perda.
- Geração de gráficos e tabelas em LaTeX a partir dos resultados coletados.

## Estrutura do repositório

```
Trabalho_Redes/
├── Relatorio_Tecnico_Redes.pdf     # Relatório técnico do trabalho
├── trabalho_final_redes.pdf        # Documento final entregue
├── Testes/                         # Scripts e resultados de análise
│   ├── generate_plots.py           # Geração dos gráficos a partir do CSV
│   ├── resultados.csv              # Resultados consolidados dos testes
│   ├── tabela_*.tex                # Tabelas em LaTeX geradas a partir dos dados
│   └── *.png                       # Gráficos gerados (throughput, retransmissão, perda etc.)
└── Trabalho_Redes/
    └── Redes/
        ├── src/main/java/gbn/      # Código-fonte da implementação
        │   ├── Emissor.java        # Lógica do emissor (rdt_send, timer, handshake, FIN)
        │   ├── Receptor.java       # Lógica do receptor (simulação de perda, ACKs, MD5)
        │   └── PacoteGBN.java      # Estrutura do pacote (cabeçalho e serialização)
        ├── bin/                    # Classes compiladas (saída do javac)
        ├── input/                  # Arquivos de entrada usados nos testes (imagens)
        ├── output/                 # Arquivos recebidos e resultados dos testes
        ├── resultados.csv          # Resultados da execução local
        ├── run_tests.sh            # Script de bateria de testes automatizados
        └── como_executar.txt       # Instruções rápidas de execução
```

## Requisitos

- Java Development Kit (JDK) 8 ou superior.
- Python 3 com as bibliotecas `pandas`, `matplotlib` e `seaborn` (apenas para regenerar os gráficos, opcional).
- Ambiente Linux/Unix com `bash` (necessário apenas para rodar `run_tests.sh`).

## Como compilar

A partir do diretório `Trabalho_Redes/Trabalho_Redes/Redes`:

```bash
javac -d bin src/main/java/gbn/*.java
```

## Como executar

É necessário abrir dois terminais: um para o Receptor e outro para o Emissor. O Receptor deve ser iniciado primeiro.

**Terminal 1 — Receptor:**

```bash
java -cp bin gbn.Receptor
```

**Terminal 2 — Emissor:**

```bash
java -cp bin gbn.Emissor <arquivo_origem> <IP_destino>:<caminho_destino> <tamanho_janela> <prob_perda>
```

Exemplo:

```bash
java -cp bin gbn.Emissor input/woman.jpg 127.0.0.1:output/woman.jpg 100 0.1
```

Onde:

- `arquivo_origem`: caminho do arquivo a ser transmitido.
- `IP_destino:caminho_destino`: endereço do Receptor e caminho onde o arquivo será salvo.
- `tamanho_janela`: tamanho da janela deslizante (N) do protocolo Go-Back-N.
- `prob_perda`: probabilidade de perda simulada de pacotes, entre 0 e 1.

Observação: execute os comandos em uma janela de terminal sem quebra de linha automática, para que os logs de progresso não sejam desformatados.

## Bateria de testes automatizados

O script `run_tests.sh` executa uma bateria de testes variando o tamanho da janela (N = 1, 2, 4, 8, 16, 32) e a probabilidade de perda (0%, 5%, 10%, 15%), consolidando os resultados no arquivo `resultados.csv`.

```bash
./run_tests.sh woman.jpg
```

Os resultados incluem: pacotes enviados, retransmissões, pacotes descartados, taxa de perda efetiva, tempo total de transmissão, throughput e verificação de integridade via MD5.

## Geração dos gráficos e tabelas

A partir do diretório `Testes/`, os gráficos e tabelas em LaTeX podem ser regenerados com:

```bash
python3 generate_plots.py
```

O script utiliza o arquivo `resultados.csv` como entrada e produz gráficos comparativos de throughput, retransmissões, perda efetiva e tempo de transmissão em função do tamanho da janela e da probabilidade de perda.

## Relatório técnico

O relatório completo do trabalho, com fundamentação teórica, detalhamento da implementação, metodologia de testes e análise dos resultados, está disponível em `Relatorio_Tecnico_Redes.pdf`.

## Autores
- [Jeann Victor](https://github.com/JeannVictor) 
- [Thallysson Luis](https://github.com/Thallysson100) 


Trabalho desenvolvido para a disciplina de Redes de Computadores — UNIFAL-MG.

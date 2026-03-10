# Semana 1: Definição do Alvo, Revisão de CLI e Busca de Ortólogos

Esta seção documenta os passos iniciais do projeto, estabelecendo a proteína alvo da pesquisa, a configuração do ambiente de trabalho em terminal Linux e a mineração de dados evolutivos.

## 🎯 Proteína Alvo do Estudo

O projeto tem como foco o estudo estrutural e evolutivo da seguinte enzima ligada aos processos de memória:

* **Proteína:** Proteína quinase C tipo zeta (PKM$\zeta$)
* **Gene:** *prkcz*
* **Isoforma:** 5
* **Número de acesso (NCBI):** NM_001429423.1 (mRNA) / NP_001416352.1 (Proteína)
* **Código UNIPROT:** P09217
* **Organismo de referência:** *Rattus norvegicus*

## 🐧 Revisão e Configuração de Ambiente (Linux)

Para a execução das etapas de bioinformática, foi realizada uma revisão prática de comandos essenciais de interface de linha de comando (CLI) em ambiente Linux, operando via conexão remota (SSH) em servidor:

* **Navegação e Leitura de Arquivos:** Utilização de comandos de manipulação de diretórios (`pwd`, `ls`) e visualização direta de arquivos FASTA e resultados tabulares (`cat`).
* **Transferência Segura de Dados:** Uso do comando `scp` (Secure Copy) para o download seguro de arquivos e diretórios de resultados do servidor remoto (máquina virtual) para a máquina local, garantindo a integridade dos dados para análises visuais futuras.
* **Execução de Pipelines:** Configuração de comandos baseados em ferramentas do pacote BLAST+ (`makeblastdb`, `tblastn`, `blastp`) utilizando paralelismo (`-num_threads`) e filtragem rigorosa (`-evalue`, `-outfmt 6`).

## 🧬 Busca de Ortólogos (Ferramenta OASIS)

A etapa final da semana consistiu no levantamento evolutivo da sequência alvo.

Utilizando a ferramenta **OASIS**, a sequência da proteína de referência (NP_001416352.1) foi submetida a uma busca ativa por ortólogos em diversas espécies. O processo resultou na compilação de um arquivo `.fasta` curado, contendo as sequências ortólogas validadas. 

Este arquivo formou o banco de dados restrito utilizado nas etapas subsequentes para a validação cruzada (BLAST reverso) dos fragmentos genômicos obtidos na montagem *de novo*, garantindo que apenas sequências com parentesco evolutivo direto com a PKM$\zeta$ fossem isoladas para modelagem 3D.

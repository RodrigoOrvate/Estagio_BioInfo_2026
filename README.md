# Semana 1: Definição do Alvo, Revisão de CLI e Busca de Ortólogos

Esta seção documenta os passos iniciais do projeto, estabelecendo a proteína alvo da pesquisa, a configuração do ambiente de trabalho em terminal Linux e a mineração de dados evolutivos.

## 🎯 Proteína Alvo do Estudo

O projeto tem como foco o estudo estrutural e evolutivo da seguinte enzima ligada aos processos de memória:

* **Proteína:** Proteína quinase C tipo zeta (PKMζ)
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

Utilizando a ferramenta **[OASIS](https://github.com/RodrigoOrvate/OASIS)**, desenvolvida e documentada em repositório próprio, a sequência da proteína de referência (NP_001416352.1) foi submetida a uma busca ativa por ortólogos em diversas espécies. O processo resultou na compilação de um arquivo `.fasta` curado, contendo as sequências ortólogas validadas. 

Este arquivo formou o banco de dados restrito utilizado nas etapas subsequentes para a validação cruzada (BLAST reverso) dos fragmentos genômicos obtidos na montagem *de novo*, garantindo que apenas sequências com parentesco evolutivo direto com a PKMζ fossem isoladas para modelagem 3D.

# Semana 2: Relatório de Atividades: Bioinformática e Engenharia de Dados

### 1. Pipeline MaraudersGenoMap: Processamento e Predição

O pipeline [MaraudersGenoMap](https://github.com/evomol-lab/MaraudersGenoMap) foi aplicado para processar dados genômicos da espécie _Ctenodon histrix_ (**SRA31116834**), permitindo a transição de leituras brutas para a identificação de proteínas candidatas.

-   **SRAget.sh**: Realiza o download dos dados via `prefetch` e utiliza o `fasterq-dump --split-files` para separar as leituras em pares (**Forward** e **Reverse**), preservando a orientação necessária para a montagem.
    
-   **assembly-megahit.sh**: Executa o controle de qualidade e a montagem _de novo_ utilizando o **MEGAHIT**, consolidando as leituras em sequências contínuas denominadas contigs (`final.contigs.fa`).
    
-   **ProtSearch.sh**: Utiliza o **Prodigal** para predizer proteínas a partir dos contigs e o **HMMER** para buscar domínios conservados da família de interesse (**PKMζ**) com base em perfis probabilísticos (.hmm).
    
-   **get_Seq_results.sh**: Filtra os resultados do HMMER e utiliza o `seqtk` para extrair as sequências completas das proteínas identificadas.
    
-   **lectin_hits.faa**: Arquivo final gerado pelo pipeline contendo as sequências proteicas da planta que apresentam similaridade estrutural com os domínios da família PKMζ pesquisada.
    

### 2. Sistematização e Mineração em MariaDB

Iniciou-se a estruturação de um ambiente relacional para otimizar a análise funcional e o cruzamento de dados entre o genoma da planta e os ortólogos obtidos via **OASIS**.

-   **Arquitetura de Tabelas**: Criação das tabelas `result_blast` (alinhamentos brutos), `ponte_homologos` (vínculo entre planta e homólogos) e `hsa_description` (dicionário de anotação funcional).
    
-   **Data Cleaning (ETL)**: Execução de comandos SQL para padronização de identificadores e remoção de artefatos de arquivos FASTA (como o caractere ">"), garantindo a integridade dos relacionamentos (**JOINs**).
    
-   **Identificação de Hits**: Através de consultas customizadas, foram identificados **216 registros** especificamente relacionados ao gene **PRKCZ**, confirmando a presença dessa família no material genômico analisado.
    

### 3. Consulta SQL de Validação

```
SELECT 
    r.query AS contig_planta, 
    r.identity, 
    d.description
FROM 
    result_blast r
JOIN 
    ponte_homologos p ON r.query = p.contig_id
JOIN 
    hsa_description d ON p.homologo_id = d.query
WHERE 
    d.description LIKE '%PRKCZ%'
ORDER BY 
    r.identity DESC;
```

----------

**Conclusão Técnica**: A integração entre os scripts de montagem e o banco de dados MariaDB permitiu transformar dados brutos em informação biológica categorizada, validando a presença de domínios relacionados à **PKMζ** em _Ctenodon histrix_.

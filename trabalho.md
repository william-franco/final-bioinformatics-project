Trabalho Final de Bioinformática

Realizar um estudo de caso, em formato de relatório, com os organismos patogênicos abaixo, dando as seguintes informações:

* Descrição da bactéria, doença que causa, alguma curiosidade a respeito do organismo, se é gram positiva ou gram negativa  
* Proteoma de referência (Uniprot): ex: UP000000811 Treponema pallidum
* Total de proteínas do proteoma  
* Lista de publicações associadas a esse proteoma  
* Detalhamento da máquina que usou para rodar as amostras (CPU, Memoria RAM, Quantidade e tipo de armazenamento (ssd, nvme, hd))  
* Executar o software FastProtein ([https://github.com/labioinfoufsc/FastProtein](https://github.com/labioinfoufsc/FastProtein)) para o proteoma completo e responder  
  * Tempo total de processamento  
  * Quantas proteínas foram processadas?  
  * Quantas foram ignoradas no processamento?  
  * Gráfico de localização subcelular (gerado no FastProtein)  
  * Gráfico de dispersão ponto isoelétrico x massa molecular (gerado no FastProtein)  
  * Total de proteínas com domínio transmembranar  
  * Total de proteínas com evidências de membrana  
* Executar o software EpiBuilder ([https://github.com/labioinfoufsc/EpiBuilder](https://github.com/labioinfoufsc/EpiBuilder)) e como *input* utilizar *apenas* o arquivo com as primeiras 50 proteínas de membrana obtidas no software FastProtein e responder  
  * Das 50 proteínas, quantas proteínas foram preditas como tendo epítopos?  
  * Faça uma tabela contendo o ID da proteína, descrição, epítopo, localização (inicial e final) e se o epítopo é ou não N-glicosilado dos 5 primeiros epítopos preditos.  
  * Cole a topologia completa do epítopo número 1 da predição.  
* Entregável: repositório git com os seguintes arquivos  
  * relatório em pdf
    * Modelo: relatorio.md
  * input.fasta  
  * top50.fasta  
  * results\_fastprotein (diretório)  
  * results\_epibuilder (diretório)  
* Apresentação: (3 pontos)

**Comandos de execução (exemplo)**

Organismo: Treponema pallidum  
Código do proteoma de referência: UP000000811  
Informações gerais do proteoma: [https://www.uniprot.org/proteomes/UP000000811](https://www.uniprot.org/proteomes/UP000000811)

Comandos linux:

\#Baixa o proteoma de ID UP000000811 direto do Uniprot (única coisa que vai alterar no script abaixo)

curl \-s "https://rest.uniprot.org/uniprotkb/stream?format=fasta\&query=proteome:UP000000811" \-o input.fasta

\#Roda em terminal o FastProtein indicando o proteoma de referência  
docker run \--rm \-v ${PWD}:/data bioinfoufsc/fastprotein:clean-latest fastprotein \-i /data/input.fasta \-o /data/results\_fastprotein

\#Separa apenas as 50 primeiras proteinas estimadas como de membrana  
awk '/^\>/{c++} c\<=50' results\_fastprotein/raw/membranes.fasta \> top50.fasta

\#Roda o epibuilder no arquivo gerado top50.fasta (o parametro –loc para gram positiva é gram\_pos e para gram negativa \#é gram\_neg)

docker run \-it \--rm \\  
        \-v ${PWD}:/data/ \\  
        \-v /var/run/docker.sock:/var/run/docker.sock \\  
        \-v epibuilder-data:/tmp/epibuilder \\  
        \-e EPIBUILDER\_VOLUME=epibuilder-data \\  
        bioinfoufsc/epibuilder-core epibuilder \\  
        \--input\_file /data/top50.fasta \\  
        \--loc gram\_pos \\  
        \--output /data/results\_epibuilder  

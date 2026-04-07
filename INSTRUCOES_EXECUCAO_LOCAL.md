# Instruções para reproduzir o trabalho localmente

Este guia assume **Linux** com **Docker** instalado e acesso à internet. Todos os caminhos são relativos à pasta do projeto (onde estão `trabalho.md` e `relatorio.md`).

## Pré-requisitos

1. **Docker Engine** (testado com Docker 28.x).  
2. Espaço em disco suficiente para imagens (`bioinfoufsc/fastprotein:clean-latest`, `bioinfoufsc/epibuilder-core`) e resultados.  
3. Opcional: **Pandoc** (+ distribuição LaTeX ou motor HTML) para gerar `relatorio.pdf` a partir de `relatorio.md`.

Verificar Docker:

```bash
docker --version
```

## 1. Obter o proteoma (UniProt)

Substitua apenas o ID do proteoma na URL se sua equipe usar outro organismo.

```bash
cd /caminho/para/projeto-final

curl -s "https://rest.uniprot.org/uniprotkb/stream?format=fasta&query=proteome:UP000000811" -o input.fasta

# Conferir quantidade de sequências (esperado: 1027 para UP000000811)
grep -c '^>' input.fasta
```

Metadados do proteoma no navegador: [https://www.uniprot.org/proteomes/UP000000811](https://www.uniprot.org/proteomes/UP000000811)

## 2. FastProtein (Docker)

Baixar a imagem (na primeira vez):

```bash
docker pull bioinfoufsc/fastprotein:clean-latest
```

Executar a análise (monta o diretório atual em `/data` dentro do container):

```bash
rm -rf results_fastprotein

docker run --rm -v "${PWD}:/data" bioinfoufsc/fastprotein:clean-latest \
  fastprotein -i /data/input.fasta -o /data/results_fastprotein
```

Principais saídas:

- `results_fastprotein/summary.txt` — totais (processadas, ignoradas, TM, membrana).  
- `results_fastprotein/image/` — gráficos PNG (localização subcelular, pI × massa).  
- `results_fastprotein/raw/membranes.fasta` — proteínas com evidência de membrana (usado no passo seguinte).

## 3. Top 50 proteínas de membrana

```bash
awk '/^>/{c++} c<=50' results_fastprotein/raw/membranes.fasta > top50.fasta

grep -c '^>' top50.fasta   # deve imprimir 50
```

## 4. EpiBuilder (Docker)

Para *Treponema pallidum* use **`gram_neg`**. Para espécies Gram-positivas, troque por **`gram_pos`**.

Criar volume nomeado (recomendado pelo exemplo oficial; evita problemas de cache do pipeline):

```bash
docker volume create epibuilder-data 2>/dev/null || true
```

Executar **sem** `-it` (adequado a scripts/CI); use `-it` apenas se quiser terminal interativo:

```bash
rm -rf results_epibuilder
mkdir -p results_epibuilder

docker run --rm \
  -v "${PWD}:/data/" \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v epibuilder-data:/tmp/epibuilder \
  -e EPIBUILDER_VOLUME=epibuilder-data \
  bioinfoufsc/epibuilder-core epibuilder \
  --input_file /data/top50.fasta \
  --loc gram_neg \
  --output /data/results_epibuilder
```

Arquivos úteis:

- `results_epibuilder/protein-summary.tsv` — epítopos por proteína.  
- `results_epibuilder/epitope-detail.tsv` — lista ordenada de epítopos.  
- `results_epibuilder/topology.tsv` — topologia detalhada (incluindo epítopo #1).  
- `results_epibuilder/epibuilder.xlsx` — planilha consolidada.

## 5. Documentar hardware (para o relatório)

Exemplos de comandos no Ubuntu/Debian:

```bash
lscpu
free -h
lsblk -d -o NAME,SIZE,TYPE,ROTA,MODEL
uname -a
```

## 6. Gerar `relatorio.pdf` (opcional)

Se **Pandoc** e um motor de PDF estiverem instalados:

```bash
# Exemplo com PDF via LaTeX (pacotes: pandoc, texlive-xetex ou equivalente)
pandoc relatorio.md -o relatorio.pdf --from markdown --resource-path=.:results_fastprotein/image
```

Se as figuras não aparecerem, confira `--resource-path` ou converta o Markdown no VS Code / Cursor com extensão de exportação.

## 7. Git (entrega)

```bash
git init
git add relatorio.md relatorio.pdf input.fasta top50.fasta results_fastprotein results_epibuilder INSTRUCOES_EXECUCAO_LOCAL.md
git commit -m "Trabalho final: proteoma UP000000811, FastProtein e EpiBuilder"
```

Crie o repositório remoto (GitHub/GitLab), adicione `git remote add origin ...` e faça `git push`.

**Checklist de entrega:** `relatorio.pdf`, `input.fasta`, `top50.fasta`, pastas `results_fastprotein/` e `results_epibuilder/` completas.

## Problemas comuns

| Sintoma | Sugestão |
|--------|----------|
| Permissão negada no Docker | Adicione seu usuário ao grupo `docker` e faça login de sessão novamente. |
| Arquivos em `results_*` pertencem a `root` (falha no `git add`) | Após os containers, ajuste o dono com: `docker run --rm -v "${PWD}:/data" alpine sh -c "chown -R $(id -u):$(id -g) /data/results_fastprotein /data/results_epibuilder"` ou use `sudo chown` no host. |
| `results_epibuilder` vazio ou erro de volume | Garanta que `results_epibuilder` exista e esteja vazio **ou** remova-o antes, conforme acima. |
| EpiBuilder lento | Normal: o pipeline Nextflow pode levar vários minutos para 50 sequências. |
| Gram errado no EpiBuilder | Ajuste `--loc` para `gram_pos` ou `gram_neg` conforme a espécie. *Treponema pallidum* é Gram-negativa → use `gram_neg` (o exemplo do `trabalho.md` com `gram_pos` não se aplica a este organismo). |

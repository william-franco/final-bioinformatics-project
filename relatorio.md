

# **RELATÓRIO DE ESTUDO DE CASO: ANÁLISE PROTEÔMICA E EPÍTOPOS DA BACTÉRIA *Treponema pallidum* (cepa Nichols)**

**IDENTIFICAÇÃO DA EQUIPE**

* **Integrantes:** William Ferreira Franco
* **Data:** 05/04/2026

---

## **1\. CARACTERIZAÇÃO DO ORGANISMO PATOGÊNICO**

* **Descrição morfológica:** Espiroqueta (hélice a sinusoidal), com duas membranas, camada fina de peptidoglicano e flagelos no espaço periplasmático.  
* **Classificação de Gram:** Gram-negativa (embora muitas espiroquetas não colorem bem pelo método de Gram clássico, *T. pallidum* é tratada como Gram-negativa pela organização da parede/membrana externa).  
* **Patologia:** Agente etiológico da **sífilis**, doença sexualmente transmissível que pode evoluir por estágios (primária, secundária, latente e terciária), com manifestações cutâneas, sistêmicas e, em fases tardias, envolvimento cardiovascular e neurológico.  
* **Curiosidade:** É um **parasita humano obrigatório** que não pode ser cultivado *in vitro* em meios artificiais de rotina; a cepa **Nichols** foi isolada em 1912 do líquor de um paciente com sífilis secundária e mantida em coelhos desde então, sendo referência para o genoma completo publicado.  
* **Cepa analisada:** Nichols (*Treponema pallidum* subsp. *pallidum* str. Nichols).  
* **Imagem em microscópio da bactéria:** \[Inserir figura de microscopia, se disponível no material da disciplina\]

---

## **2\. DADOS DO PROTEOMA (UNIPROT)**

* **ID do Proteoma de Referência:** [UP000000811](https://www.uniprot.org/proteomes/UP000000811)  
* **Espécie/Linhagem:** *Treponema pallidum* (strain Nichols)  
* **Total de proteínas no proteoma:** 1027  
* **Total de cromossomos:** 1 (cromossomo circular principal; montagem [GCA_000008605.1](https://www.ebi.ac.uk/ena/browser/view/GCA_000008605.1))  
* **Lista de publicações associadas:**  
  1. Fraser C.M., Norris S.J., Weinstock G.M., *et al.* Complete genome sequence of *Treponema pallidum*, the syphilis spirochete. *Science* **281**, 375–388 (1998). PMID: [9665876](https://pubmed.ncbi.nlm.nih.gov/9665876/). DOI: [10.1126/science.281.5375.375](https://doi.org/10.1126/science.281.5375.375).

---

## **3\. AMBIENTE DE EXECUÇÃO (HARDWARE)**

*Detalhes da máquina utilizada para rodar os softwares FastProtein e EpiBuilder (medidos neste ambiente em 05/04/2026).*

* **CPU (Processador):** Intel 12th Gen Core i7-1260P (12 núcleos físicos, 16 CPUs lógicas, até ~4,7 GHz).  
* **Memória RAM:** 16 GB instalados (LPDDR5).  
* **Armazenamento:** SSD NVMe 1 TB.  
* **Sistema Operacional:** Ubuntu 24.04.4 LTS.

---

## **4\. RESULTADOS: FASTPROTEIN**

* **Tempo total de processamento:** **00:04:16** (4 minutos e 16 segundos; log do FastProtein: início 23:07:11 UTC, fim 23:11:27 UTC, 05/04/2026).  
* **Total de proteínas processadas:** **1001**  
* **Total de proteínas ignoradas:** **26** (sequências contendo `*` ou `X`, conforme log do FastProtein)  
* **Proteínas com domínio transmembranar:** **250** (coluna “Proteins with TM” em `results_fastprotein/summary.txt`)  
* **Proteínas com evidências de membrana:** **259** (“Membrane proteins” em `results_fastprotein/summary.txt`)

### **4.1. Gráficos Gerados**

**Localização subcelular (WoLF PSORT — resumo em barras):**

![Localização subcelular prevista](results_fastprotein/image/subcell-resume-bar.png)

**Dispersão: ponto isoelétrico × massa molecular (kDa):**

![Massa molecular (kDa) vs ponto isoelétrico](results_fastprotein/image/kda-vs-pi.png)

*Nota:* o arquivo `output.md` do FastProtein referencia também um gráfico em pizza (`subcell-resume-pie.png`); nesta execução da imagem `clean-latest` apenas os PNG de barras e de dispersão foram gerados na pasta `results_fastprotein/image/`.

---

## **5\. RESULTADOS: EPIBUILDER**

*Entrada:* `top50.fasta` (50 primeiras sequências de `results_fastprotein/raw/membranes.fasta`).  
*Parâmetro de localização:* `--loc gram_neg` (adequado a *Treponema pallidum*, Gram-negativa).  
*Duração do pipeline Nextflow (log):* ~7 min 36 s.

* **Nº de proteínas (das 50) com epítopos preditos:** **16** (proteínas com pelo menos um epítopo em `results_epibuilder/protein-summary.tsv`, coluna `Epitopes` > 0).

### **5.1. Tabela dos 5 Primeiros Epítopos Preditos**

(Ordenação conforme coluna `N` em `results_epibuilder/epitope-detail.tsv`. Descrições obtidas de `results_fastprotein/output.tsv`.)

| ID Proteína | Descrição | Epítopo (Sequência) | Localização (Início–Fim) | N-glicosilado? |
| :---- | :---- | :---- | :---- | :---- |
| O30405 | Glycerophosphodiester phosphodiesterase | SFYTRGKRHTP | 113–124 | Não |
| O83289 | Amino acid ABC transporter, permease protein (BraC) | RYTTAPFPPTPAN | 215–228 | Não |
| O83444 | V-type ATP synthase subunit I 1 | QTKNVTRGEE | 62–72 | Sim (motivo NVT\[4:6\] no relatório do EpiBuilder) |
| O83561 | tRNA modification GTPase MnmE | GGKNGEVRDR | 379–389 | Não |
| O66103 | Membrane protein insertase YidC | RTPPEIGPERN | 254–265 | Não |

### **5.2. Topologia do Epítopo #1**

*Representação copiada de `results_epibuilder/topology.tsv` (epítopo ranqueado como 1). Fonte monoespaçada:*

```
1	O30405	O30405	Periplasmic	   BepiPred-3.0	0.15	0.25	-	SFYTRGKRHTP	113	124	N	0	-	11	1.35	11.00	-1.71	0.82
				          Emini	1.00	1.94	0.91	EEEEEEEEEE.         	
				       Kolaskar	1.03	0.98	0.18	.........EE         	
				    Chou Fosman	0.94	1.07	1.00	EEEEEEEEEEE         	
				 Karplus Schulz	0.99	1.03	1.00	EEEEEEEEEEE         	
				         Parker	1.09	2.79	1.00	EEEEEEEEEEE         	
				    All matches	-	-	0.09	.........E.         	
				         N-Glyc	-	-	0.00	...........         	
				     Hydropathy	-	-1.71	-	-+---------         	
```

---

## **6\. Repositório**

* **Repositório git (preencher após publicação):** \[COLAR O URL DO REPOSITÓRIO AQUI — ex.: GitHub/GitLab\]  
* **Geração do PDF:** exporte este `relatorio.md` para PDF com Pandoc (veja `INSTRUCOES_EXECUCAO_LOCAL.md`) ou use a função “Exportar PDF” do seu editor.

*Confirme se os seguintes arquivos estão no repositório Git:*

* \[  \] **relatorio.pdf** (versão PDF deste documento)  
* \[ x \] **relatorio.md** (fonte do relatório)  
* \[ x \] **input.fasta** (proteoma UniProt)  
* \[ x \] **top50.fasta** (50 primeiras proteínas de membrana do FastProtein)  
* \[ x \] **results_fastprotein/** (saída completa)  
* \[ x \] **results_epibuilder/** (saída completa)

---

## **7\. Referências de software**

* FastProtein: [https://github.com/labioinfoufsc/FastProtein](https://github.com/labioinfoufsc/FastProtein) — Moreira R.S. *et al.*, PeerJ 12:e18309, 2024.  
* EpiBuilder: [https://github.com/labioinfoufsc/EpiBuilder](https://github.com/labioinfoufsc/EpiBuilder)  
* UniProt Proteomes: [https://www.uniprot.org/proteomes/UP000000811](https://www.uniprot.org/proteomes/UP000000811)

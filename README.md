# ESG_VM_Dataset
Dataset structure for ESG Value Mining Program
# 📊 Estrutura Geral do Banco de Dados – ESG Data Mining

Este documento descreve a modelagem relacional do banco de dados para o programa ESG Data Mining, com foco em stakeholders como investidores, ONGs e gestores de projeto. O objetivo é permitir a correlação de dados entre diferentes fontes para apoiar decisões de investimento em projetos com viés ESG.

---

## 👤 Entidade: Usuario

| Campo     | Tipo       | Descrição                                 |
|-----------|------------|-------------------------------------------|
| id        | UUID (PK)  | Identificador único do usuário            |
| nome      | String     | Nome completo                             |
| email     | String     | E-mail de login (único)                   |
| tipo      | Enum       | `investidor`, `gestor`, `ong`             |

**Relacionamentos:**
- Um usuário pode ser dono de vários projetos (`ProjetoNbS`)
- Um usuário pode favoritar vários projetos (via `Favorito`)

---

## 🌱 Entidade: ProjetoNbS

| Campo              | Tipo         | Descrição                                 |
|--------------------|--------------|-------------------------------------------|
| id                 | UUID (PK)    | Identificador do projeto                  |
| nome               | String       | Nome do projeto                           |
| descricao          | Text         | Descrição detalhada                       |
| tipo               | Enum         | `REDD+`, `ARR`, `ALM`, etc.               |
| localizacao        | Point        | Coordenadas geográficas (lat, long)       |
| impacto_estimado   | JSON         | Ex: `{carbono: 1200, agua: 300}`          |
| status             | Enum         | `em_captação`, `em_execução`, `finalizado`|
| id_usuario         | UUID (FK)    | Dono do projeto (referência a `Usuario`)  |

---

## ⭐ Entidade: Favorito (associativa)

| Campo         | Tipo      | Descrição                                 |
|---------------|-----------|-------------------------------------------|
| id_usuario    | UUID (FK) | Referência ao usuário                     |
| id_projeto    | UUID (FK) | Referência ao projeto                     |

> Representa os projetos que um usuário marcou como favoritos.

---

## 💰 Entidade: FundoESG

| Campo              | Tipo         | Descrição                                 |
|--------------------|--------------|-------------------------------------------|
| id                 | UUID (PK)    | Identificador do fundo                    |
| nome               | String       | Nome do fundo                             |
| criterios          | JSON         | Ex: `{setores: [...], regioes: [...], impacto: [...]}` |
| valor_disponivel   | Decimal      | Valor disponível para investimento        |

---

## 🔗 Entidade: Match (associativa)

| Campo         | Tipo      | Descrição                                 |
|---------------|-----------|-------------------------------------------|
| id            | UUID (PK) | Identificador do match                    |
| id_projeto    | UUID (FK) | Projeto relacionado                       |
| id_fundo      | UUID (FK) | Fundo relacionado                         |
| score         | Decimal   | Grau de afinidade                         |
| status        | Enum      | `pendente`, `aceito`, `rejeitado`         |

> Representa a compatibilidade entre projetos e fundos com base em critérios ESG.

---

## 🗺️ Entidade: GeoDados

| Campo       | Tipo      | Descrição                                 |
|-------------|-----------|-------------------------------------------|
| id          | UUID (PK) | Identificador do dado geográfico          |
| tipo        | Enum      | `Naturebase`, `SICAR`, `Embrapa`, etc.    |
| poligono    | GeoJSON   | Área geográfica                           |
| metadados   | JSON      | Fonte, data, precisão, etc.               |

---

## 🧠 Sugestões Técnicas

- Banco de dados: **PostgreSQL com PostGIS**
- Campos JSON: usar **JSONB** para performance e flexibilidade
- Criar **views** para facilitar análises por stakeholder
- Implementar **controle de acesso** por tipo de usuário

---

## 📌 Diagrama ER

O Modelo Entidade-Relacionamento (ER) pode ser visto abaixo e o diagrama ER correspondente pode ser visualizado no arquivo `.drawio` gerado separadamente, com relacionamentos evidenciados, cardinalidade e agrupamentos visuais.

![Diagrama ER](https://github.com/Moriblo/ESG_VM_Datasets/blob/main/esg-vm-datasets-uml.drawio.png)

---

### 🔗 Mapeamento: Fontes Open Free → Tabelas do Projeto - VERSÃO INICIAL

| 🌐 Fonte Open Free                                                                 | 🗃️ Tabela Alvo   | ⚙️ Tipo de Integração / Enriquecimento                                      |
|------------------------------------------------------------------------------------|------------------|------------------------------------------------------------------------------|
| 🌍 [Naturebase.org](https://naturebase.org)                                       | `GeoDados`       | Áreas prioritárias para NbS (polígonos, biomas, países)                     |
| 🏡 [SICAR](https://www.car.gov.br/publico/cadastro)                                | `GeoDados`       | Imóveis rurais, uso do solo, sobreposição com projetos                      |
| 🌱 [Embrapa AgroAPI](https://www.embrapa.br/agroapi)                               | `ProjetoNbS`     | Clima, NDVI, solo → viabilidade e impacto estimado                          |
| 💼 [ESG Enterprise](https://www.esgenterprise.com) / [B3 ESG](https://www.b3.com.br) | `FundoESG`       | Critérios ESG, setores, regiões, valores disponíveis                        |
| 🤝 [Aliança Brasil NbS](https://www.aliancabrasilnbs.org)                          | `ProjetoNbS`     | Cadastro direto ou via integração com parceiros                             |
| 🛰️ [MapBiomas](https://mapbiomas.org) / [GFW](https://www.globalforestwatch.org) / [OSM](https://www.openstreetmap.org) | `GeoDados` | Uso da terra, alertas, infraestrutura                                       |


### 🔗 Mapeamento: Fontes Open Free → Tabelas do Projeto

| 🌐 Fonte Open Free                                                                 | 🗃️ Tabela Alvo   | ⚙️ Tipo de Integração / Enriquecimento                                      |
|------------------------------------------------------------------------------------|------------------|------------------------------------------------------------------------------|
| 🌍 [Naturebase.org](https://naturebase.org)                                       | `GeoDados`       | Áreas prioritárias para NbS (polígonos, biomas, países)                     |
| 🏡 [SICAR](https://www.car.gov.br/publico/cadastro)                                | `GeoDados`       | Imóveis rurais, uso do solo, sobreposição com projetos                      |
| 🌱 [Embrapa AgroAPI](https://www.embrapa.br/agroapi) <br> 🌿 [B3 Sustentabilidade](https://www.b3.com.br/pt_br/b3/sustentabilidade/) | `ProjetoNbS`     | Clima, NDVI, solo, critérios de sustentabilidade → viabilidade e impacto    |
| 💼 [ISE B3](https://www.b3.com.br/pt_br/market-data-e-indices/indices/indices-de-sustentabilidade/indice-de-sustentabilidade-empresarial-ise-b3.htm) | `FundoESG`       | Critérios ESG, setores, regiões, valores disponíveis                        |
| 🤝 [Aliança Brasil NbS](https://www.aliancabrasilnbs.org)                          | `ProjetoNbS`     | Cadastro direto ou via integração com parceiros                             |
| 🛰️ [MapBiomas](https://mapbiomas.org) / [GFW](https://www.globalforestwatch.org) / [OSM](https://www.openstreetmap.org) | `GeoDados` | Uso da terra, alertas, infraestrutura                                       |






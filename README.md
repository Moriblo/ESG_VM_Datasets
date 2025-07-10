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

O diagrama ER correspondente pode ser visualizado no arquivo `.drawio` gerado separadamente, com relacionamentos evidenciados, cardinalidade e agrupamentos visuais.

![Diagrama ER](https://github.com/Moriblo/ESG_VM_Datasets/blob/main/esg-vm-datasets-uml.drawio.png)


# Capítulo X – Modelagem e Implementação do Banco de Dados

## 1. Introdução

O banco de dados do sistema foi projetado para armazenar informações de usuários, perfis, filmes, gêneros e avaliações, seguindo a **modelagem conceitual previamente definida no DER (Diagrama Entidade-Relacionamento)**.
A implementação foi realizada no **MySQL**, utilizando comandos da **Data Definition Language (DDL)** para criar tabelas, definir restrições e relacionamentos.

---

## 2. Modelo Conceitual – DER

### 2.1 Diagrama Entidade-Relacionamento

![DER - diagrama de entidade-relacionamento](./net-flix-db.png)

## 3. Implementação – DDL

### 3.1 Criação do Banco

```sql
CREATE DATABASE netflix_aula
  DEFAULT CHARACTER SET utf8mb4
  COLLATE utf8mb4_0900_ai_ci;
```

* **utf8mb4** → suporta emojis e caracteres especiais
* **COLLATE utf8mb4\_0900\_ai\_ci** → ignora diferenças de maiúsculas/minúsculas na comparação

---

### 3.2 Criação de Tabelas

#### Tabela `usuario`

```sql
CREATE TABLE usuario (
  id_usuario INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
  nome VARCHAR(100) NOT NULL,
  email VARCHAR(100) NOT NULL UNIQUE,
  senha VARCHAR(255) NOT NULL
) ENGINE=InnoDB;
```

* `AUTO_INCREMENT` → gera IDs únicos automaticamente
* `PRIMARY KEY` → identifica cada registro
* `NOT NULL` e `UNIQUE` → garantem integridade
* `ENGINE=InnoDB` → suporta FK e transações

---

#### Tabela `perfil`

```sql
CREATE TABLE perfil (
  id_perfil INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
  id_usuario INT UNSIGNED NOT NULL,
  nome_perfil VARCHAR(50) NOT NULL,
  idioma VARCHAR(20) DEFAULT 'pt-BR',
  controle_parental BOOLEAN DEFAULT FALSE,
  CONSTRAINT fk_perfil_usuario
    FOREIGN KEY (id_usuario) REFERENCES usuario(id_usuario)
    ON DELETE CASCADE
    ON UPDATE CASCADE
);
```

* FK → vincula perfil a usuário
* **CASCADE** → se usuário for apagado, perfis são removidos

---

#### Tabela `genero`

```sql
CREATE TABLE genero (
  id_genero INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
  nome VARCHAR(50) NOT NULL
) ENGINE=InnoDB;
```

---

#### Tabela `filme`

```sql
CREATE TABLE filme (
  id_filme INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
  titulo VARCHAR(150) NOT NULL,
  ano INT,
  duracao INT,
  id_genero INT UNSIGNED,
  FOREIGN KEY (id_genero) REFERENCES genero(id_genero)
    ON DELETE SET NULL
    ON UPDATE CASCADE
) ENGINE=InnoDB;
```

* **SET NULL** → se o gênero for apagado, o filme fica sem gênero

---

#### Tabela `avaliacao`

```sql
CREATE TABLE avaliacao (
  id_perfil INT UNSIGNED NOT NULL,
  id_filme INT UNSIGNED NOT NULL,
  nota TINYINT UNSIGNED NOT NULL,
  comentario TEXT,
  PRIMARY KEY (id_perfil, id_filme),
  FOREIGN KEY (id_perfil) REFERENCES perfil(id_perfil)
    ON DELETE CASCADE
    ON UPDATE CASCADE,
  FOREIGN KEY (id_filme) REFERENCES filme(id_filme)
    ON DELETE CASCADE
    ON UPDATE CASCADE,
  CHECK (nota BETWEEN 1 AND 5)
) ENGINE=InnoDB;
```

* Representa relacionamento N\:N entre perfil e filme
* **CHECK** → nota entre 1 e 5

---

## 4. Considerações Técnicas

| Conceito        | Significado                                 | Exemplo                           |
| --------------- | ------------------------------------------- | --------------------------------- |
| ENGINE=InnoDB   | Motor da tabela, suporta transações e FK    | `CREATE TABLE ... ENGINE=InnoDB;` |
| CONSTRAINT      | Restrição de integridade                    | `PRIMARY KEY`, `FOREIGN KEY`      |
| COLLATE         | Como o banco compara/ordena textos          | `utf8mb4_0900_ai_ci`              |
| CASCADE         | Propaga ação para tabela filha              | `ON DELETE CASCADE`               |
| SET NULL        | Deixa FK nula se pai for apagado            | `ON DELETE SET NULL`              |
| RESTRICT        | Impede apagar/alterar se houver dependentes | `ON DELETE RESTRICT`              |
| DEFAULT         | Valor padrão para coluna                    | `idioma DEFAULT 'pt-BR'`          |
| AUTO\_INCREMENT | Gera valores automáticos para PK            | `id_usuario INT AUTO_INCREMENT`   |

---
---
## 📖 Dicionário de Dados – Banco Netflix

| Tabela     | Coluna            | Tipo             | PK | FK               | Not Null | Único | Default   | Descrição                                     |
|------------|-----------------|-----------------|----|-----------------|---------|-------|-----------|-----------------------------------------------|
| usuario    | id_usuario       | INT UNSIGNED    | ✓  |                 | ✓       |       | AUTO_INCREMENT | Identificador único do usuário               |
| usuario    | nome             | VARCHAR(100)    |    |                 | ✓       |       |           | Nome completo do usuário                      |
| usuario    | email            | VARCHAR(100)    |    |                 | ✓       | ✓     |           | Email do usuário (único no sistema)          |
| usuario    | senha            | VARCHAR(255)    |    |                 | ✓       |       |           | Senha criptografada do usuário               |
| perfil     | id_perfil        | INT UNSIGNED    | ✓  |                 | ✓       |       | AUTO_INCREMENT | Identificador único do perfil                |
| perfil     | id_usuario       | INT UNSIGNED    |    | usuario.id_usuario | ✓    |       |           | Relaciona perfil a um usuário               |
| perfil     | nome_perfil      | VARCHAR(50)     |    |                 | ✓       |       |           | Nome do perfil dentro do usuário            |
| perfil     | idioma           | VARCHAR(20)     |    |                 |         |       | 'pt-BR'   | Idioma do perfil                             |
| perfil     | controle_parental| BOOLEAN         |    |                 |         |       | FALSE     | Indica se o perfil tem controle parental    |
| genero     | id_genero        | INT UNSIGNED    | ✓  |                 | ✓       |       | AUTO_INCREMENT | Identificador único do gênero               |
| genero     | nome             | VARCHAR(50)     |    |                 | ✓       |       |           | Nome do gênero                               |
| filme      | id_filme         | INT UNSIGNED    | ✓  |                 | ✓       |       | AUTO_INCREMENT | Identificador único do filme                |
| filme      | titulo           | VARCHAR(150)    |    |                 | ✓       |       |           | Título do filme                              |
| filme      | ano              | INT             |    |                 |         |       |           | Ano de lançamento do filme                   |
| filme      | duracao          | INT             |    |                 |         |       |           | Duração em minutos                           |
| filme      | id_genero        | INT UNSIGNED    |    | genero.id_genero |         |       |           | Relaciona filme a um gênero                  |
| avaliacao  | id_perfil        | INT UNSIGNED    | ✓* | perfil.id_perfil | ✓       |       |           | Perfil que avaliou o filme                   |
| avaliacao  | id_filme         | INT UNSIGNED    | ✓* | filme.id_filme  | ✓       |       |           | Filme que foi avaliado                        |
| avaliacao  | nota             | TINYINT UNSIGNED|    |                 | ✓       |       |           | Nota do filme (1 a 5)                        |
| avaliacao  | comentario       | TEXT            |    |                 |         |       |           | Comentário opcional do perfil sobre o filme |

> \*PK composta (`id_perfil + id_filme`) na tabela `avaliacao`.


## 5. Conclusão

A implementação do banco de dados permite:

1. Armazenar informações de usuários, perfis, filmes, gêneros e avaliações.
2. Garantir integridade e consistência dos dados.
3. Transformar o DER conceitual em tabelas funcionais no MySQL.

> Com essa estrutura, o sistema está preparado para operações de inserção, consulta, atualização e exclusão de dados de forma segura e confiável.





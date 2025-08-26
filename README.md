# 🚀 Aula: Introdução à Modelagem de Banco de Dados (MySQL)

---

## 1. O que é um Banco de Dados?

Banco de Dados é um **conjunto organizado de informações** que podem ser armazenadas, manipuladas e recuperadas.

📌 **Exemplos do dia a dia:**

* O **WhatsApp** armazena contatos, mensagens e grupos.
* O **Instagram** guarda usuários, postagens, curtidas e comentários.
* O **IFood** tem clientes, restaurantes, pedidos e entregas.

---

## 2. O que é Modelagem de Dados?

Modelagem de dados é o **processo de planejar e organizar os dados** antes de criar o banco.

💡 **Analogia:** É como fazer a planta de uma casa antes de construir.

---

## 3. Níveis de Modelagem

* **Modelo Conceitual (o quê):**

  * Mostra entidades e relacionamentos do mundo real.
  * Representado no **DER (Diagrama Entidade-Relacionamento)**.

* **Modelo Lógico (como):**

  * Mostra atributos, chaves primárias e estrangeiras.
  * É uma “tradução” para tabelas, mas ainda sem SQL.

* **Modelo Físico (implementação):**

  * O código SQL no MySQL (ou outro SGBD).
  * Estrutura final: tabelas, índices, constraints.

---

## 4. Entidades

### **Entidade Forte 💪**

* Existe por si só.
* Tem chave primária própria.
* Exemplo: **Aluno**, **Produto**, **Filme**, **Funcionário**.

---

### **Entidade Fraca 🪶**

* Não existe sozinha, depende de outra.
* A chave primária é formada pela chave da entidade forte (ou recebe um id próprio + FK).
* Exemplo: **Dependente** (de Funcionário), **Perfil** (de Usuário Netflix).

---

### **Entidade Associativa 🔗**

* Representa um relacionamento **N\:N**.
* Vira uma tabela intermediária.
* Exemplo: **Matrícula** (Aluno ↔ Curso), **Avaliação** (Usuário ↔ Filme).

---

## 5. Atributos

São as **características da entidade**.

* **Simples:** nome, CPF.
* **Composto:** endereço (rua, nº, bairro).
* **Derivado:** idade (a partir de data\_nascimento).
* **Multivalorado:** telefones (um aluno pode ter vários números).

---

## 6. Relacionamentos

* **1 : 1** → Um CPF só pode ter um RG.
* **1 : N** → Um curso tem vários alunos.
* **N : N** → Um aluno pode estar em vários cursos e um curso pode ter vários alunos.

---

## 7. Linguagens SQL

### **DDL – Data Definition Language**

Define a **estrutura do banco**.

* `CREATE`, `ALTER`, `DROP`, `TRUNCATE`.

```sql
CREATE TABLE aluno (
    id_aluno INT PRIMARY KEY,
    nome VARCHAR(100)
);
```

---

### **DML – Data Manipulation Language**

Manipula **os dados**.

* `INSERT`, `UPDATE`, `DELETE`, `SELECT`.

```sql
INSERT INTO aluno (id_aluno, nome) VALUES (1, 'Maria');
```

---

### **DCL – Data Control Language**

Controle de **permissões**.

* `GRANT`, `REVOKE`.

---

### **TCL – Transaction Control Language**

Controle de **transações**.

* `COMMIT`, `ROLLBACK`, `SAVEPOINT`.

---

## 8. Propriedades ACID

Garantem segurança e integridade dos dados:

* **A – Atomicidade:** ou tudo acontece, ou nada.
* **C – Consistência:** mantém os dados válidos.
* **I – Isolamento:** transações não interferem entre si.
* **D – Durabilidade:** dados confirmados ficam salvos mesmo após falhas.

📌 Exemplo: compra online → se o pagamento falhar, o estoque não pode diminuir.

---

## 9. Exemplo do Mundo Real – 🎬 Netflix Simplificada

### 📋 Escopo:

* Usuários podem se cadastrar.
* Cada usuário pode criar vários perfis.
* Filmes pertencem a gêneros.
* Perfis podem avaliar filmes.

---

### **Entidades**

* **Usuário (forte):** id\_usuario, nome, email, senha.
* **Perfil (fraca):** id\_perfil, nome\_perfil, idioma, controle\_parental, id\_usuario.
* **Filme (forte):** id\_filme, titulo, ano, duracao, id\_genero.
* **Gênero (forte):** id\_genero, nome.
* **Avaliação (associativa):** id\_perfil, id\_filme, nota, comentario.

---

### **DER (simplificado em texto)**

!["DER -NETFLIX"]("./net-flix-db.png")

---

### **Modelo Lógico (SQL)**

```sql
CREATE TABLE usuario (
    id_usuario INT AUTO_INCREMENT PRIMARY KEY,
    nome VARCHAR(100) NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    senha VARCHAR(255) NOT NULL
);

CREATE TABLE perfil (
    id_perfil INT AUTO_INCREMENT PRIMARY KEY,
    id_usuario INT NOT NULL,
    nome_perfil VARCHAR(50) NOT NULL,
    idioma VARCHAR(20) DEFAULT 'pt-BR',
    controle_parental BOOLEAN DEFAULT FALSE,
    FOREIGN KEY (id_usuario) REFERENCES usuario(id_usuario)
);

CREATE TABLE genero (
    id_genero INT AUTO_INCREMENT PRIMARY KEY,
    nome VARCHAR(50) NOT NULL
);

CREATE TABLE filme (
    id_filme INT AUTO_INCREMENT PRIMARY KEY,
    titulo VARCHAR(150) NOT NULL,
    ano INT,
    duracao INT,
    id_genero INT,
    FOREIGN KEY (id_genero) REFERENCES genero(id_genero)
);

CREATE TABLE avaliacao (
    id_perfil INT NOT NULL,
    id_filme INT NOT NULL,
    nota INT NOT NULL,
    comentario TEXT,
    PRIMARY KEY (id_perfil, id_filme),
    FOREIGN KEY (id_perfil) REFERENCES perfil(id_perfil),
    FOREIGN KEY (id_filme) REFERENCES filme(id_filme)
);
```

---

## 10. Quadro Comparativo de Entidades

```
ENTIDADE FORTE 💪
- Tem identidade própria.
- PK independente.
Ex: Usuário, Filme, Funcionário.

ENTIDADE FRACA 🪶
- Não existe sozinha.
- Depende de outra (PK composta ou FK).
Ex: Dependente, Perfil Netflix.

ENTIDADE ASSOCIATIVA 🔗
- Representa relacionamento N:N.
- Usa PK composta (ou id próprio).
Ex: Matrícula, Avaliação.
```

---

## 11. Atividade em Grupo 📝

📌 **Objetivo:** Criar um modelo conceitual simples de um sistema do dia a dia.

Sugestões de escopo:

* **IFood:** Cliente, Restaurante, Pedido, ItemPedido.
* **Uber:** Passageiro, Motorista, Corrida, Avaliação.
* **Escola:** Aluno, Professor, Turma, Matrícula, Nota.

👉 Cada grupo define entidades, atributos e relacionamentos.
👉 Depois, escolhem uma entidade forte, uma fraca e uma associativa para justificar.

---

# 🎯 Encerrando

* **Banco de Dados** = informações organizadas.
* **Modelagem** = planejar antes de construir.
* **Entidades:** forte, fraca, associativa.
* **SQL:** DDL, DML, DCL, TCL.
* **ACID:** garante segurança e integridade.
* **Exemplo Netflix:** mostrou como entidades se conectam.

---

👨‍🏫 **Mensagem final para alunos apáticos:**

> Tudo que vocês usam no dia a dia (Netflix, WhatsApp, IFood, jogos online) só existe porque alguém modelou e criou um banco de dados para armazenar cada detalhe.

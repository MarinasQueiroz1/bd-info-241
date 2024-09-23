# Primeiro 
  Faça login na conta do docker hub usando o comando `docker login` 
# Segundo
 Faça `vim docker-compose.yml` e pressione a tecla i para entrar no modo INSERT e poder editar alguma coisa
    Cole o script (Ctrl+Shift+V)
    
``` ruby
version: '3.8'

services:
  mysql:
    image: mysql:8.0
    container_name: mysql_container
    environment:
      MYSQL_ROOT_PASSWORD: rootpassword
      MYSQL_DATABASE: mydatabase
      MYSQL_USER: myuser
      MYSQL_PASSWORD: mypassword
    volumes:
      - mysql_data:/var/lib/mysql
    ports:
      - "3306:3306"

  phpmyadmin:
    image: phpmyadmin/phpmyadmin
    container_name: phpmyadmin_container
    environment:
      PMA_HOST: mysql
      PMA_PORT: 3306
      PMA_USER: root
      PMA_PASSWORD: rootpassword
    ports:
      - "8080:80"
    depends_on:
      - mysql

volumes:
  mysql_data:

```
# Terceiro 

 Feche o vim, usando `esc`
   Depois pressione `:` e escreva `wq` para salvar o arquivo 
# Quarto
instale o connector do mysql para o python, usando ` pip install mysql-connector-python`

Crie um container com ` docker-compose up -d`

Para abrir o mysql: `docker exec -it mysql_container mysql -u root -p`

Ele vai pedir a senha, coloque `rootpassword`

`  use mydatabase;` para entrar no database, vai aparecer a mensagem " Database changed" isso quer dizer que deu certo

cole o codigo abaixo:

``` ruby
CREATE TABLE IF NOT EXISTS TB_ALUNOS (
    id INT PRIMARY KEY AUTO_INCREMENT,
    nome TEXT
);
CREATE TABLE IF NOT EXISTS TB_PROFESSOR (
    id INT PRIMARY KEY AUTO_INCREMENT,
    nome TEXT
);
CREATE TABLE IF NOT EXISTS TB_DISCIPLINA (
    id INT PRIMARY KEY AUTO_INCREMENT,
    nome TEXT
);
CREATE TABLE IF NOT EXISTS Matricula (
    id INT PRIMARY KEY AUTO_INCREMENT,
    nome_aluno INT,
    nome_professor INT,
    disciplina INT,
    nota_N1 FLOAT,
    nota_N2 FLOAT,
    faltas INT,
    Aprovado_SN BOOLEAN,
    FOREIGN KEY (nome_aluno) REFERENCES TB_ALUNOS(id),
    FOREIGN KEY (nome_professor) REFERENCES TB_PROFESSOR(id),
    FOREIGN KEY (disciplina) REFERENCES TB_DISCIPLINA(id)
);

```
Para popular as tabelas: 
``` ruby
-- popular as tabelas

INSERT INTO TB_ALUNOS (nome) VALUES ('Joao Pedro');
INSERT INTO TB_ALUNOS (nome) VALUES ('Maria Isadora');
INSERT INTO TB_ALUNOS (nome) VALUES ('Carlos Nobrega');
INSERT INTO TB_ALUNOS (nome) VALUES ('Gabriella Pastos');
INSERT INTO TB_ALUNOS (nome) VALUES ('Guilherme Padua');
INSERT INTO TB_ALUNOS (nome) VALUES ('Linda Carvalho');
INSERT INTO TB_ALUNOS (nome) VALUES ('Mateus Henrique');
INSERT INTO TB_ALUNOS (nome) VALUES ('Porcia Magalhaes');
INSERT INTO TB_ALUNOS (nome) VALUES ('Joao Paula');


INSERT INTO TB_PROFESSOR (nome) VALUES ('Prof. Elena Brito');
INSERT INTO TB_PROFESSOR (nome) VALUES ('Prof. Pascoal Monte');
INSERT INTO TB_PROFESSOR (nome) VALUES ('Prof. Carla Padro');
INSERT INTO TB_PROFESSOR (nome) VALUES ('Prof. Joao Silva');


INSERT INTO TB_DISCIPLINA (nome) VALUES ('Matematica');
INSERT INTO TB_DISCIPLINA (nome) VALUES ('Historia');
INSERT INTO TB_DISCIPLINA (nome) VALUES ('Fisica');
INSERT INTO TB_DISCIPLINA (nome) VALUES ('Quimica');
INSERT INTO TB_DISCIPLINA (nome) VALUES ('Biologia');


INSERT INTO Matricula (nome_aluno, nome_professor, disciplina, nota_N1, nota_N2, faltas, Aprovado_SN) 
VALUES (1, 1, 1, 7.5, 8.0, 2, TRUE);
INSERT INTO Matricula (nome_aluno, nome_professor, disciplina, nota_N1, nota_N2, faltas, Aprovado_SN) 
VALUES (2, 2, 2, 6.0, 5.5, 5, FALSE);
INSERT INTO Matricula (nome_aluno, nome_professor, disciplina, nota_N1, nota_N2, faltas, Aprovado_SN) 
VALUES (3, 1, 1, 9.0, 8.5, 1, TRUE);
INSERT INTO Matricula (nome_aluno, nome_professor, disciplina, nota_N1, nota_N2, faltas, Aprovado_SN) 
VALUES (4, 3, 3, 6.5, 7.0, 3, TRUE);
INSERT INTO Matricula (nome_aluno, nome_professor, disciplina, nota_N1, nota_N2, faltas, Aprovado_SN) 
VALUES (5, 4, 4, 5.0, 6.0, 6, FALSE);
INSERT INTO Matricula (nome_aluno, nome_professor, disciplina, nota_N1, nota_N2, faltas, Aprovado_SN) 
VALUES (6, 1, 1, 8.5, 9.0, 0, TRUE);
INSERT INTO Matricula (nome_aluno, nome_professor, disciplina, nota_N1, nota_N2, faltas, Aprovado_SN) 
VALUES (7, 2, 5, 7.0, 7.5, 4, TRUE);
INSERT INTO Matricula (nome_aluno, nome_professor, disciplina, nota_N1, nota_N2, faltas, Aprovado_SN) 
VALUES (8, 3, 3, 9.0, 8.5, 1, TRUE);
INSERT INTO Matricula (nome_aluno, nome_professor, disciplina, nota_N1, nota_N2, faltas, Aprovado_SN) 
VALUES (9, 4, 4, 5.5, 6.0, 8, FALSE);
```
Pressione `enter` para confirmar

Saia do mysql, use o comando: ` exit`

# Quinto 

Crie o arquivo.py, com ` vim main.py` 

Clique na tecla i para entrar no modo INSERT e poder editar alguma coisa

Cole o script

``` ruby
import mysql.connector

# Conexão com o banco de dados

conn = mysql.connector.connect(
    host='localhost',
    user='myuser',
    password='mypassword',
    database='mydatabase'
)

cursor = conn.cursor()

# Função para executar uma consulta e exibir os resultados
# Função para executar uma consulta e exibir os resultados

def executar_consulta(query):
    cursor.execute(query)
    result = cursor.fetchall()
    for row in result:
        
    # Usar 'join' para formatar a saída sem parênteses ou vírgulas

        print(", ".join(str(item) for item in row))


# 1. Listar todos os alunos reprovados
query_reprovados = """
SELECT 
    TB_ALUNOS.nome AS Nome_Aluno, 
    TB_DISCIPLINA.nome AS Nome_Disciplina, 
    TB_PROFESSOR.nome AS Nome_Professor, 
    Matricula.nota_N1, 
    Matricula.nota_N2, 
    (Matricula.nota_N1 + Matricula.nota_N2) / 2 AS Media, 
    Matricula.faltas, 
    CASE 
        WHEN (Matricula.nota_N1 + Matricula.nota_N2) / 2 < 6 THEN 'Reprovado por Média' 
        WHEN Matricula.faltas > 5 THEN 'Reprovado por Falta' 
    END AS Status_Reprovacao
FROM Matricula
JOIN TB_ALUNOS ON Matricula.nome_aluno = TB_ALUNOS.id
JOIN TB_DISCIPLINA ON Matricula.disciplina = TB_DISCIPLINA.id
JOIN TB_PROFESSOR ON Matricula.nome_professor = TB_PROFESSOR.id
WHERE Matricula.Aprovado_SN = FALSE;
"""
print("Alunos Reprovados:")
executar_consulta(query_reprovados)

# 2. Listar todos os alunos aprovados
query_aprovados = """
SELECT 
    TB_ALUNOS.nome AS Nome_Aluno, 
    TB_DISCIPLINA.nome AS Nome_Disciplina, 
    TB_PROFESSOR.nome AS Nome_Professor, 
    Matricula.nota_N1, 
    Matricula.nota_N2, 
    (Matricula.nota_N1 + Matricula.nota_N2) / 2 AS Media, 
    Matricula.faltas, 
    'Aprovado por Média' AS Status_Aprovacao
FROM Matricula
JOIN TB_ALUNOS ON Matricula.nome_aluno = TB_ALUNOS.id
JOIN TB_DISCIPLINA ON Matricula.disciplina = TB_DISCIPLINA.id
JOIN TB_PROFESSOR ON Matricula.nome_professor = TB_PROFESSOR.id
WHERE Matricula.Aprovado_SN = TRUE;
"""
print("\nAlunos Aprovados:")
executar_consulta(query_aprovados)

# 3. Listar a quantidade de alunos aprovados
query_quantidade_aprovados = """
SELECT COUNT(*) AS Quantidade_Aprovados
FROM Matricula
WHERE Matricula.Aprovado_SN = TRUE;
"""
print("\nQuantidade de Alunos Aprovados:")
executar_consulta(query_quantidade_aprovados)

# 4. Listar a quantidade de alunos reprovados
query_quantidade_reprovados = """
SELECT COUNT(*) AS Quantidade_Reprovados
FROM Matricula
WHERE Matricula.Aprovado_SN = FALSE;
"""
print("\nQuantidade de Alunos Reprovados:")
executar_consulta(query_quantidade_reprovados)


cursor.close()
conn.close()
``` 
Feche o vim

#### Pressione `esc`

# Sexto (Executar)

#### Faça `  python3 main.py` 

#### digite ` docker logout ` 




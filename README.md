# SQL server

# PRIMEIRAMENTE BAIXAMOS O PACOTE NUGET PARA O DOTNET

![Untitled](SQL%20server%202422fdac54f244bb940f6d8911566525/Untitled.png)

```csharp
dotnet add package microsoft.data.sqlclient
```

---

OBSERVAÇÃO: antes de tentar a conexão seguir o passo a passo desse artigo [https://balta.io/blog/sql-server-docker](https://balta.io/blog/sql-server-docker)

[https://github.com/balta-io/2805/blob/master/balta.zip](https://github.com/balta-io/2805/blob/master/balta.zip)  = baixe esta pasta para criar um banco basico e com admin pack vc consegue repassar todo o codigo para azure data studio

![Untitled](SQL%20server%202422fdac54f244bb940f6d8911566525/Untitled%201.png)

![Untitled](SQL%20server%202422fdac54f244bb940f6d8911566525/Untitled%202.png)

1q2w3e4r@#$

![Untitled](SQL%20server%202422fdac54f244bb940f6d8911566525/Untitled%203.png)

---

# STRING DE CONEXÃO

![Untitled](SQL%20server%202422fdac54f244bb940f6d8911566525/Untitled%204.png)

esse codigo para conexão no banco de dados gerava um erro na certificação nao confiavel tive que incluir mais um pedaço de codigo igual abaixo. Adicionei a mais “TrustServerCertificate=True”, depois ele conectou no banco

![Untitled](SQL%20server%202422fdac54f244bb940f6d8911566525/Untitled%205.png)

---

# Criando database

```jsx
CREATE DATABASE [Curso]

```

---

# excluindo database : primeiramente ele elimina todos os processos ativos do banco.

```sql
USE [master];

DECLARE @kill varchar(8000) = '';
SELECT @kill = @kill + 'kill ' + CONVERT(varchar(5), session_id) + ';'
FROM sys.dm_exec_sessions
WHERE database_id  = db_id('Curso')

EXEC(@kill);

DROP DATABASE [Curso]   // no final ele exclui o banco de dados

```

---

# Usando o banco de dados e criando tabelas:

```sql
use [Curso]  // [] serve para que o sistema entenda que nao é uma palavra reservada

CREATE TABLE [Aluno](
        [Id] INT,
        [Nome] NVARCHAR(80),
        [Nascimento] DATETIME,
        [Active] BIT
)
GO   // essa função ja inseri no banco de dados

-----------------------------------------------------------------------

CREATE TABLE [Aluno](
        [Id] INT not null,
        [Nome] NVARCHAR(80) not null,      // criando dessa forma tiramos ele nao deixa passar os parametros nullos
        [Nascimento] DATETIME not null,
        [Active] BIT
)
GO
---------------------------------------------------------------------------
CREATE TABLE [Aluno](
        [Id] INT NOT NULL,
        [Nome] NVARCHAR(80) NOT NULL,
        [Nascimento] DATETIME NOT NULL,
        [Active] BIT NOT NULL DEFAULT(0), // aqui ele deixa o valor padrão de 0

)
GO

```

---

# Adicionando informação a tabela

```sql
use [Curso]

ALTER TABLE [Aluno] // alterar tabela

    ADD [Document] NVARCHAR(11) // adiciona esta coluna

```

---

# excluindo uma tabela

```sql
DROP TABLE [Aluno]

```

---

# excluindo uma coluna

```sql
use [Curso]

ALTER TABLE [Aluno] // alterar tabela

    DROP COLUMN [Document]  // exclui a coluna

```

---

# alterando uma tabela

```sql
use [Curso]

ALTER TABLE [Aluno]
    ALTER COLUMN [Document] CHAR(11) // altera o tipo primitivo

```

---

# alterando uma tabela e uma coluna

```sql
ALTER TABLE [Aluno]
    ALTER COLUMN [Active] BIT NOT NULL // aqui mudamos para not null

```

---

# NOT NULL

```sql
CREATE TABLE [Aluno](
        [Id] INT NOT NULL ,
        [Nome] NVARCHAR(80) NOT NULL, // NAO DEIXA ENTRAR NULLO
        [Email] NVARCHAR(180) NOT NULL ,
        [Nascimento] DATETIME NOT NULL,
        [Active] BIT NOT NULL DEFAULT(0),

)
GO

```

---

# UNIQUE

```sql
CREATE TABLE [Aluno](
        [Id] INT NOT NULL ,
        [Nome] NVARCHAR(80) NOT NULL,
        [Email] NVARCHAR(180) NOT NULL UNIQUE, // FAZ COM QUE O VALOR SEJA UNICO, E NAO DEIXA DUPLICAR OS DADOS
        [Nascimento] DATETIME NOT NULL,
        [Active] BIT NOT NULL DEFAULT(0),

)
GO

```

---

# PRYMARY KEY

```sql
CREATE TABLE [Aluno](
        [Id] INT NOT NULL,
        [Nome] NVARCHAR(80) NOT NULL,
        [Email] NVARCHAR(180) NOT NULL UNIQUE,
        [Nascimento] DATETIME NOT NULL,
        [Active] BIT NOT NULL DEFAULT(0),

        PRIMARY KEY ([Id])
)
GO

```

---

# PRYMARY KEY COM CONSTRAINT

```sql
CREATE TABLE [Aluno](
        [Id] INT NOT NULL,
        [Nome] NVARCHAR(80) NOT NULL,
        [Email] NVARCHAR(180) NOT NULL ,
        [Nascimento] DATETIME NOT NULL,
        [Active] BIT NOT NULL DEFAULT(0),

       CONSTRAINT [PK_Aluno] PRIMARY KEY ([Id]), // A CONSTRAINT TE DA OPÇÃO DE ALTERAR O NOME E DECIDIR O TIPO UNICO DE CADA UM
       CONSTRAINT [UQ_Aluno_Email] UNIQUE([Email]),
)
GO

```

---

# COMPOSITE KEY

```sql
CREATE TABLE [ProgressoCurso](
        [AlunoId] INT NOT NULL,
        [CursoId] INT NOT NULL,
        [Progresso] INT NOT NULL,
        [UltimaAtualizacao] DATETIME NOT NULL DEFAULT (GETDATE()),

        CONSTRAINT PK_ProgressoCurso PRIMARY KEY ([AlunoId], [CursoId]), /* ISSO É UMA CHAVE PRIMARIA COMPOSTA, QUANDO ELE DECLARA MAIS DE UM
PARAMETRO COMO PRIMARY KEY*/
)
GO

```

```sql
CREATE TABLE [Curso](
        [Id] INT NOT NULL,
        [Nome] NVARCHAR(80) NOT NULL,
        [CategoriaId] INT NOT NULL,

        CONSTRAINT [PK_Curso] PRIMARY KEY ([Id]),
        CONSTRAINT [FK_Curso_Categoria] FOREIGN KEY ([CategoriaId]) // CHAVE ESTRANGEIRA
            REFERENCES [Categoria]([Id])  // TEVE QUE FAZER UMA REFERENCIA PARA O TIPO DE TABELA E O PARAMETRO
)
GO

```

---

# INDICES

```sql
CREATE INDEX [IX_Aluno_Email] ON [Aluno] ([Email]) // CRIA UM INDICE, NA TABELA ALUNO E NA COLUNA EMAIL

DROP INDEX [IX_Aluno_Email] ON [Aluno] // DELETA UM INDICE

```

indices são criados para os campos mais pesquisados e requeridos do banco

---

# INDENTITY

```sql
CREATE TABLE [Curso](
        [Id] INT NOT NULL IDENTITY(1,1), // ELE CRIA UMA INDENTIDADE DE 1 EM 1, COMO SE FOSSE UM GUID, MAS VAI ACRESCENTANDO O NUMERO 1
        [Nome] NVARCHAR(80) NOT NULL,
        [CategoriaId] INT NOT NULL,

        CONSTRAINT [PK_Curso] PRIMARY KEY ([Id]),
        CONSTRAINT [FK_Curso_Categoria] FOREIGN KEY ([CategoriaId])
            REFERENCES [Categoria]([Id])
)
GO

```

---

# CRIAÇÃO DE UM BANCO RELACIONAL BASICO

```sql
USE [Cursos]
CREATE TABLE [Categoria] (
        [Id] INT NOT NULL IDENTITY(1,1),
        [Nome] NVARCHAR(80) NOT NULL,

        CONSTRAINT [PK_Categoria] PRIMARY KEY ([Id])
)
GO

CREATE TABLE [Curso](
        [Id] INT NOT NULL IDENTITY(1,1),
        [Nome] NVARCHAR(80) NOT NULL,
        [CategoriaId] INT NOT NULL,

        CONSTRAINT [PK_Curso] PRIMARY KEY ([Id]),
        CONSTRAINT [FK_Curso_Categoria] FOREIGN KEY ([CategoriaId])
            REFERENCES [Categoria]([Id])
)
GO

```

---

# INSERINDO OS SEGUINTES VALORES

```sql
INSERT into [Categoria] (Nome) VALUES('Backend')
INSERT into [Categoria] (Nome) VALUES('Frontend')
INSERT into [Categoria] (Nome) VALUES('Mobile')

INSERT into [Curso] ([Nome], [CategoriaId]) VALUES('Fundamentos de C#', 1)
INSERT into [Curso] ([Nome], [CategoriaId]) VALUES('Fundamentos de OOP', 1)
INSERT into [Curso] ([Nome], [CategoriaId]) VALUES('Angular', 2)
INSERT into [Curso] ([Nome], [CategoriaId]) VALUES('Flutter', 3)

INSERT into [Curso] VALUES('Flutter e SQLIte' , 3)

```

---

# SELECTS

```sql
SELECT * FROM [Curso]   // seleciona todos os parametros do banco curso

SELECT top 2 * FROM [Curso] // seleciona os dois primeiros dados do banco

SELECT top 2
    [Id], [Nome]  // seleciona os dois primeiros, desses campos id e nome
FROM  [Curso]

SELECT distinct top 100
	     [Nome]     // nao deixa nenhum nome se repetir
FROM  [Categoria]

```

---

# QUERIES

```sql
SELECT distinct top 100
     [Id], [Nome] , [CategoriaId]

FROM  [Curso]

WHERE [CategoriaId] = 1
    AND [id] = 1

```

```sql
SELECT distinct top 100
     [Id], [Nome] , [CategoriaId]

FROM  [Curso]

WHERE [CategoriaId] = 1
    AND [id] IS NOT NULL

```

---

# ORDER BY

```sql
SELECT distinct top 100
     [Id], [Nome] , [CategoriaId]

FROM  [Curso]

-- WHERE [CategoriaId] = 1   -- ESSE SINAL É TRANSFORMA EM COMENTARIO NO SQL
 --   AND [id] IS NOT NULL

 ORDER BY Nome ASC OU DESC

```

---

# UPDATE

```sql
BEGIN TRANSACTION
        UPDATE  [Categoria]
        SET [Nome] = 'Azure'

        WHERE [id] = 4
COMMIT

ROLLBACK // VOLTA A VERSAO ANTERIOR, AI COLOCAMOS NO LUGAR DO COMMIT

```

---

# DELETE

```sql
DELETE FROM [Categoria]

        WHERE [id] = 4

```

DEU ERRO PARA EXCLUIR ESSA CATEGORIA PORQUE ELA É UMA CHAVE ESTRANGEIRA E VINCULADA A OUTRO BANCO, COMO ESTAMOS SELECIONANDO O ID ELE É UMA CHAVE ESTRANGEIRA, SE TENTAMOS PELO CURSO AI CONSEGUIMOS APAGAR OS DADOS

![SQL%20server%202422fdac54f244bb940f6d8911566525/Untitled%206.png](SQL%20server%202422fdac54f244bb940f6d8911566525/Untitled%206.png)

AI NESSE CASO TIVEMOS QUE EXCLUIR PRIMEIRO AS INFORMAÇÕES DO BANCO PRINCIPAL E DEPOIS DO SEGUNDO BANCO

```sql
DELETE FROM [Curso]

        WHERE [CategoriaId] = 3

        DELETE FROM [Categoria]

        WHERE [Id] = 3

```

---

# LIKE

```sql
SELECT TOP 100 *

FROM [Curso]

where [Nome] LIKE '%Fundamentos%' -- QUANDO COLOCAMOS '%%' SIGNIFICA SE ELE CONTÊM O ITEM fUNDAMENTOS

```

---

# IN E BETWEEN

```sql
SELECT TOP 100 *

FROM [Curso]

where [Id] IN (1,3)  -- CONTEM ID 1 E 3

SELECT TOP 100 *

FROM [Curso]

where [Id] BETWEEN 1 AND 3  -- VAI PEGAR TODOS OS VALORES DE 1 A 3

```

---

# ALIAS

```sql
SELECT TOP 100

        [Id] AS [Codigo],   -- serve para alterar o nome do parametro e repassamos os valores
        [Nome] AS [Nomes]

FROM [Curso]

SELECT TOP 100

        COUNT([Id]) as [total]
FROM [Curso]

```

---

# INNER JOIN

```sql
SELECT TOP 100
        [Curso].[Id],
        [Curso].[Nome],
        [Categoria].[Id] AS [Categoria],
        [Categoria].[Nome]
FROM [Curso]

INNER JOIN [Categoria] ON [Curso].[CategoriaId] = [Categoria].[Id]

```

---

# LEFT JOIN

PEGA TODOS OS DADOS DA PRIMEIRA TABELA

```sql
SELECT TOP 100
        [Curso].[Id],
        [Curso].[Nome],
        [Categoria].[Id] AS [Categoria],
        [Categoria].[Nome]
FROM [Curso]

LEFT JOIN [Categoria] ON [Curso].[CategoriaId] = [Categoria].[Id]

```

---

# UNION

```sql
SELECT TOP 100
        [Id],[Nome]

FROM [Curso]

UNION ALL OU UNION -- O ALL SERVE PARA DISTINGUIR OS DADOS

SELECT TOP 100
        [Id],[Nome]

FROM [Categoria]

```

---

# GROUP BY

QUANDO TEM UM COUNT É NECESSARIO AGRUPAR OS MESMOS PARAMETROS DE CALCULO

```sql
SELECT TOP 100
       [Categoria].[Nome],
        COUNT([Curso].[CategoriaId]) AS [Cursos]

FROM [Categoria]

INNER JOIN [Curso] ON [Curso].[CategoriaId] = [Categoria].[Id]

GROUP BY [Curso].[CategoriaId],
        [Categoria].[Nome]

```

---

# HAVING

USAMOS O HAVING NESSE CASO, PORQUE ELE NAO ACEITA O WHERE JUNTO NESSA QUERY

```sql
SELECT TOP 100
       [Categoria].[Nome],
        COUNT([Curso].[CategoriaId]) AS [Cursos]

FROM [Categoria]

INNER JOIN [Curso] ON [Curso].[CategoriaId] = [Categoria].[Id]

GROUP BY [Curso].[CategoriaId],
        [Categoria].[Nome]

HAVING    -- CONTENDO, ELE É COMO SE FOSSE UM WHERE
    COUNT([Curso].[CategoriaId]) = 2

```

---

# VIEW

```sql
CREATE OR ALTER VIEW vwContagemCursosPorCategoria AS  -- ELE ARMAZENA ESSA QUERY E PODEMOS CONSULTAR E VISUALIZAR FUTURAMENTE
																														-- O ALTER FAZ COM QUE PODEMOS ALTERAR A CONSULTA DA QUERY
SELECT TOP 100
       [Categoria].[Nome],
        COUNT([Curso].[CategoriaId]) AS [Cursos]

FROM [Categoria]

INNER JOIN [Curso] ON [Curso].[CategoriaId] = [Categoria].[Id]

GROUP BY [Curso].[CategoriaId],
        [Categoria].[Nome]

HAVING
    COUNT([Curso].[CategoriaId]) = 2

```

---

# STORED PROCEDURES

ELA SERVE PARA DEIXA ALGUNS PEDAÇOS DE CODIGOS TEMPORARIOS NA BASE

```sql
CREATE OR ALTER PROCEDURE [spListCourse] AS   -- CRIA OU ALTERA UMA PROCEDURES
    SELECT * FROM [Curso]

    DROP PROCEDURE [spListCourse] -- DROPA UMA PROCEDURES

EXEC [spListCourse]  -- EXECUÇÃO DELA

```

NESSE CASO TIVEMOS QUE COLOCAR O NOME DA CATEGORIA, PORQUE OBRIGAMOS A PESSOA A PESQUISAR

```sql
CREATE OR ALTER PROCEDURE [spListCourse]

    @Category NVARCHAR(60)
 AS

        DECLARE @CategoryId INT
        SET @CategoryId = (SELECT TOP 1 [Id] FROM [Categoria] WHERE [Nome] = @Category)

        SELECT * FROM [Curso] where [CategoriaId] = @CategoryId

---------------------------------------------------------------------OUTRA TELA

EXEC spListCourse 'Backend'

```

OPÇÃO 2

NESSE CASO JA COLOCAMOS QUE SO QUEREMOS OS DADOS DO BACKEND

```sql
CREATE OR ALTER PROCEDURE [spListCourse] AS

        DECLARE @CategoryId INT
        SET @CategoryId = (SELECT TOP 1 [Id] FROM [Categoria] WHERE [Nome] = 'Backend')

        SELECT * FROM [Curso] where [CategoriaId] = @CategoryId

---------------------------------------------------------------------OUTRA TELA

EXEC spListCourse

```

---

# CRIAÇÃO DO BANCO DE DADOS

```sql
CREATE DATABASE [balta]
GO

USE [balta]
GO

CREATE TABLE [Student]
(
    [Id] uniqueidentifier NOT NULL,
    [Name] NVARCHAR(120) NOT NULL,
    [Email] NVARCHAR(180) NOT NULL,
    [Document] NVARCHAR(20) NULL,
    [Phone] NVARCHAR(20) NULL,
    [Birthdate] DATETIME NULL,
    [CreateDate] DATETIME NOT NULL DEFAULT(GETDATE()),
    CONSTRAINT [PK_Student] PRIMARY KEY ([Id])
);
GO

CREATE TABLE [Author]
(
    [Id] uniqueidentifier NOT NULL,
    [Name] NVARCHAR(80) NOT NULL,
    [Title] NVARCHAR(80) NOT NULL,
    [Image] NVARCHAR(1024) NOT NULL,
    [Bio] NVARCHAR(2000) NOT NULL,
    [Url] nvarchar(450) NULL,
    [Email] NVARCHAR(160) NOT NULL,
    [Type] TINYINT NOT NULL, -- 0 a 255
    CONSTRAINT [PK_Author] PRIMARY KEY ([Id])
);
GO

CREATE TABLE [Career]
(
    [Id] uniqueidentifier NOT NULL,
    [Title] NVARCHAR(160) NOT NULL,
    [Summary] NVARCHAR(2000) NOT NULL,
    [Url] NVARCHAR(1024) NOT NULL,
    [DurationInMinutes] INT NOT NULL,
    [Active] BIT NOT NULL,
    [Featured] BIT NOT NULL,
    [Tags] NVARCHAR(160) NOT NULL,
    CONSTRAINT [PK_Career] PRIMARY KEY ([Id])
);
GO

CREATE TABLE [Category]
(
    [Id] uniqueidentifier NOT NULL,
    [Title] NVARCHAR(160) NOT NULL,
    [Url] NVARCHAR(1024) NOT NULL,
    [Summary] NVARCHAR(2000) NOT NULL,
    [Order] int NOT NULL,
    [Description] TEXT NOT NULL,
    [Featured] BIT NOT NULL,
    CONSTRAINT [PK_Category] PRIMARY KEY ([Id])
);
GO

CREATE TABLE [Course]
(
    [Id] uniqueidentifier NOT NULL,
    [Tag] NVARCHAR(20) NOT NULL,
    [Title] NVARCHAR(160) NOT NULL,
    [Summary] NVARCHAR(2000) NOT NULL,
    [Url] NVARCHAR(1024) NOT NULL,
    [Level] TINYINT NOT NULL,
    [DurationInMinutes] INT NOT NULL,
    [CreateDate] DATETIME NOT NULL,
    [LastUpdateDate] DATETIME NOT NULL,
    [Active] BIT NOT NULL,
    [Free] BIT NOT NULL,
    [Featured] BIT NOT NULL,
    [AuthorId] uniqueidentifier NOT NULL,
    [CategoryId] uniqueidentifier NOT NULL,
    [Tags] NVARCHAR(160) NOT NULL,
    CONSTRAINT [PK_Course] PRIMARY KEY ([Id]),
    CONSTRAINT [FK_Course_Author_AuthorId] FOREIGN KEY ([AuthorId]) REFERENCES [Author] ([Id]),
    CONSTRAINT [FK_Course_Category_CategoryId] FOREIGN KEY ([CategoryId]) REFERENCES [Category] ([Id])
);
GO

CREATE TABLE [CareerItem]
(
    [CareerId] uniqueidentifier NOT NULL,
    [CourseId] uniqueidentifier NOT NULL,
    [Title] NVARCHAR(160) NOT NULL,
    [Description] TEXT NOT NULL,
    [Order] TINYINT NOT NULL,

    CONSTRAINT [PK_CareerItem] PRIMARY KEY ([CourseId], [CareerId]),
    CONSTRAINT [FK_CareerItem_Career_CareerId] FOREIGN KEY ([CareerId]) REFERENCES [Career] ([Id]),
    CONSTRAINT [FK_CareerItem_Course_CourseId] FOREIGN KEY ([CourseId]) REFERENCES [Course] ([Id])
);
GO

CREATE TABLE [StudentCourse]
(
    [CourseId] uniqueidentifier NOT NULL,
    [StudentId] uniqueidentifier NOT NULL,
    [Progress] TINYINT NOT NULL,
    [Favorite] BIT NOT NULL,
    [StartDate] DATETIME NOT NULL,
    [LastUpdateDate] DATETIME NULL DEFAULT(GETDATE()),
    CONSTRAINT [PK_StudentCourse] PRIMARY KEY ([CourseId], [StudentId]),
    CONSTRAINT [FK_StudentCourse_Course_CourseId] FOREIGN KEY ([CourseId]) REFERENCES [Course] ([Id]),
    CONSTRAINT [FK_StudentCourse_Student_StudentId] FOREIGN KEY ([StudentId]) REFERENCES [Student] ([Id])
);
GO

```

---

# CRIANDO UMA VIEW DO CODIGO ANTERIOR CRIADO

```sql
CREATE OR ALTER VIEW vwCourses AS

    SELECT
        [Course].[Id],
        [Course].[Tag],
        [Course].[Title],
        [Course].[Url],
        [Course].[summary],
				 [Course].[CreateDate]
        [Category].[Title] as [Category],
        [Author].[Name] as [Author]

    FROM [Course]
            INNER JOIN [Category] ON [Course].[CategoryId] = [Category].[Id]
            INNER JOIN [Author] ON [Course].[AuthorId] = [Author].[Id]
    WHERE  [Active] = 1
ORDER BY [CreateDate] DESC  // COM ORDER BY A VIEW DÁ ERRO

```

DEPOIS O SELECT DO VIEW

AS VIEWS SÃO USADAS NORMALMENTE PARA AJUDA A CONSULTAR MAIS RAPIDO E COM TODOS OS FILTROS DO SELECT ANTERIOR, COMO SE FOSSE UM CACHE DO DA QUERY ANTERIOR

```sql
SELECT *
FROM [vwCourses]
ORDER BY [CreateDate] DESC

```

---

# REALIZANDO SUBSELECT

 NÃO É UM MEIO TÃO PERFORMATICO

```sql
SELECT 
        [Id],
        [Title],
        [Url],
        (SELECT COUNT([CareerId]) FROM [CareerItem] WHERE [CareerId] = [Id]) AS [Courses]
FROM [Career]
```

RESULTADO

![Untitled](SQL%20server%202422fdac54f244bb940f6d8911566525/Untitled%207.png)

REFATORANDO O SELECT 

ESSE CODIGO É BEM MAIS PERFORMÁTICO

```sql
SELECT 
        [Career].[Id],
        [Career].[Title],
        [Career].[Url],
        COUNT([Id]) AS [Courses]
        
FROM [Career]
        INNER JOIN [CareerItem] ON [CareerItem].[CareerId] = [Career].[Id]
GROUP BY [Career].[Id],
         [Career].[Title],    -- QUANDO DAMOS GROUP BY TEMOS QUE INCLUIR TODAS AS COLUNAS
         [Career].[Url]
```

RESULTADO

![Untitled](SQL%20server%202422fdac54f244bb940f6d8911566525/Untitled%208.png)

CRIANDO VIEW DESSE CODIGO

```sql
CREATE OR ALTER VIEW vwCareers AS

SELECT 
        [Career].[Id],
        [Career].[Title],
        [Career].[Url],
        COUNT([Id]) AS [Courses]
        
FROM [Career]
        INNER JOIN [CareerItem] ON [CareerItem].[CareerId] = [Career].[Id]
GROUP BY [Career].[Id],
         [Career].[Title],
         [Career].[Url]
```

SELECT DO VIEW CRIADO

OBTEM O MESMO RESULTADO, PORQUE ELE SALVA O SELECT ANTERIOR

```sql
SELECT *
FROM [vwCareers]
```

RESULTADO

![Untitled](SQL%20server%202422fdac54f244bb940f6d8911566525/Untitled%209.png)

INSERINDO DADOS  NO BANCO

```sql
INSERT INTO 
    [Student]
    VALUES(
        '09ce0b7b-cfca-497b-92c0-3290ad9d5142', 
        'Caio Silva',
        'caiosilvaferreira1@outlook.com.br',
        '123456456',
        '3416549',
        NULL,
        GETDATE()
    )

    INSERT INTO 
        [StudentCourse]
        VALUES(
            '5d1b6506-c980-8031-5957-26df00000000',
            '09ce0b7b-cfca-497b-92c0-3290ad9d5142',  -- O ID TEM QUE SER O MESMO DO ALUNO CRIADO ANTERIORMENTE
            50,
            0,
            '2020-01-13 12:35:54',
            GETDATE()

        )
```

RESULTADO 

![Untitled](SQL%20server%202422fdac54f244bb940f6d8911566525/Untitled%2010.png)

---

# SELECT NO BANCO ESTUDANTE

```sql
DECLARE @StudentId UNIQUEIDENTIFIER = '09ce0b7b-cfca-497b-92c0-3290ad9d5142'  -- ID DO ALUNO CAIO

SELECT
        [Course].[Title] AS [Student],
        [Student].[Name] AS [Student],
        [StudentCourse].[Progress],
        [StudentCourse].[LastUpdateDate]

FROM [StudentCourse]

        INNER JOIN [Student] ON [StudentCourse].[StudentId] = [Student].[Id]
        INNER JOIN [Course] ON [StudentCourse].[CourseId] = [Course].[Id]

WHERE
        [StudentCourse].[StudentId] = @StudentId
        AND [StudentCourse].[progress] < 100    -- PROGRESSO DO ALUNO FILTRAGEM
        AND [StudentCourse].[progress] > 0

ORDER BY [StudentCourse].[LastUpdateDate] DESC

```

---

# OUTRO SELECT

```sql
DECLARE @StudentId UNIQUEIDENTIFIER = '09ce0b7b-cfca-497b-92c0-3290ad9d5142'

SELECT
        [Course].[Title] AS [Course],
        [Student].[Name] AS [Student],
        [StudentCourse].[Progress],
        [StudentCourse].[LastUpdateDate]

FROM [Course]

        LEFT JOIN [StudentCourse] ON [StudentCourse].[CourseId] = [Course].[Id]
        LEFT JOIN [Student] ON [StudentCourse].[StudentId] = [Student].[Id]

```

---

# CRIANDO UMA PROCEDURE

TIVEMOS QUE CRIAR UMA PROCEDURE, POIS A VIEW NÃO FUNCIONOU DEVIDO AO WHERE, GROUP BY E QUANDO DECLARAMOS UMA VARIAVEL

```sql
CREATE OR ALTER PROCEDURE spStudentProgress (

    @StudentId UNIQUEIDENTIFIER
)
AS

SELECT
        [Course].[Title] AS [Student],
        [Student].[Name] AS [Student],
        [StudentCourse].[Progress],
        [StudentCourse].[LastUpdateDate]

FROM [StudentCourse]

        INNER JOIN [Student] ON [StudentCourse].[StudentId] = [Student].[Id]
        INNER JOIN [Course] ON [StudentCourse].[CourseId] = [Course].[Id]

WHERE
        [StudentCourse].[StudentId] = @StudentId
        AND [StudentCourse].[progress] < 100    -- PROGRESSO DO ALUNO FILTRAGEM
        AND [StudentCourse].[progress] > 0

ORDER BY [StudentCourse].[LastUpdateDate] DESC

```

SELECT NO PROCEDURE

```sql
EXEC spStudentProgress '09ce0b7b-cfca-497b-92c0-3290ad9d5142'  // AQUI EU EXECUTEI O PROCEDURE E PESQUISEI O ID DO ALUNO CAIO

```

---

# DELETANDO A CONTA DO ALUNO

```sql
DELETE FROM [Student] WHERE [Id]='09ce0b7b-cfca-497b-92c0-3290ad9d5142'

```

GEROU ESTE ERRO PORQUE TEM VARIAS INFORMAÇÕES VINCULADAS A ESTE BANCO , ENTÃO DEVEMOS EXCLUIR OS REGISTROS VINCULADOS A ESSA INFORMAÇÃO PARA DEPOIS EXCLUIMOS A CONTA DO ALUNO

![SQL%20server%202422fdac54f244bb940f6d8911566525/Untitled%2011.png](SQL%20server%202422fdac54f244bb940f6d8911566525/Untitled%2011.png)

PRIMEIRO FIZEMOS O SELECT PARA EXCLUSÃO DE DADOS, CRIAMOS UMA PROCEDURE, DEPOIS DE DECLARAMOS O STUDENTID.

```sql
CREATE OR ALTER PROCEDURE spDeleteStudent (
        @StudentId UNIQUEIDENTIFIER

)
AS

BEGIN TRANSACTION

DELETE FROM [StudentCourse]

WHERE [StudentId]=@StudentId

DELETE FROM [Student]
WHERE [Id]=@StudentId

COMMIT

```

DEPOIS DA PROCEDURE TODA CRIADA COM SCRIPT DE EXCLUSÃO, EXECUTAMOS ELA PARA EXCLUIR A CONTA DO ALUNO, DEPOIS COLOCAMOS O ID DO ALUNO

```sql
EXEC spDeleteStudent '09ce0b7b-cfca-497b-92c0-3290ad9d5142'

```

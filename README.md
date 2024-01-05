<h1 align="center">Projeto de banco de dados - Restaurante Outback🍽</h1> 

> Projeto realizado na disciplina de Banco de Dados I, do curso ADS - IFPE.<br>
> Para mais detalhes: [Orientações sobre o projeto em pdf.](https://github.com/BiankaIbrahim/projeto-BD/files/13836834/BD1.-.Orientacoes.sobre.o.projeto.da.2.Unidade.pdf)<br>
> Equipe: [Bianka Ibrahim](https://www.linkedin.com/in/biankaibrahim/), [George Monteiro](https://www.linkedin.com/in/george-monteiro-7051ba28b/), [Pedro Lima](https://www.linkedin.com/in/pedro-lima-135913254/)
## MiniMundo - Gerenciamento de um Restaurante

Flávio, dono do restaurante Outback, deseja modernizar mais o seu restaurante, então contratou os serviços de uma empresa de TI para modelar o banco de dados do restaurante, após o levantamento inicial dos requisitos, as informações levantadas foram as seguintes:  

- Restaurante tem atributo nome e cnpj como identificador;
- Restaurante possui 1 ou muitos fornecedores, e o fornecedor possui 1 restaurante. É necessário informar o cnpj e o nome do fornecedor.
- Restaurante possui 1 ou muitos funcionários, e o funcionário possui 1 restaurante. Cada funcionário terá um número identificador, que será chamado de prontuário, o nome e sua função.
- Restaurante possui 1 ou muitos clientes, e o cliente possui 1 restaurante. Deve-se registrar o nome e número identificador do cliente. Cada cliente pode realizar 1 ou mais pedidos, mas cada pedido é realizado por apenas 1 cliente.  
- Pedido possui 1 ou muitos itens, mas cada item é realizado apenas por 1 pedido. Pedido deve ter um número identificador e o valor total.
- Item deve conter o nome do item e seu valor unitário.
- Pedido possui 1 pagamento e pagamento possui um único pedido. Necessário informar se o pagamento irá ser dinheiro ou cartão.
- Item deve conter o nome do item e seu valor unitário.

### MER
![](https://github.com/BiankaIbrahim/projeto-BD/assets/84330067/44bf9538-a203-423b-861c-23ea511549f0)

### MODELO RELACIONAL
![](https://github.com/BiankaIbrahim/projeto-BD/assets/84330067/339069b1-6bf2-4b4e-b3b6-91a8231de2e2)

### SQL
```sql
CREATE DATABASE gerenciamento_restaurante;

USE gerenciamento_restaurante;

CREATE TABLE restaurante(
	cnpj_restaurante VARCHAR(14) PRIMARY KEY,
	nome_restaurante VARCHAR(50)
);

CREATE TABLE funcionarios (
    prontuario INT PRIMARY KEY,
    nome_funcionario VARCHAR(50),
    funcao VARCHAR(50),
    cnpj_restaurante_fk VARCHAR(14),
    FOREIGN KEY (cnpj_restaurante_fk) REFERENCES restaurante(cnpj_restaurante)
);

CREATE TABLE fornecedores (
    cnpj_fornecedor VARCHAR(14) PRIMARY KEY,
    nome_fornecedor VARCHAR(50),
    cnpj_restaurante_fk VARCHAR(14),
    FOREIGN KEY (cnpj_restaurante_fk) REFERENCES restaurante(cnpj_restaurante) 
);

CREATE TABLE clientes (
    id_cliente INT PRIMARY KEY,
    nome_cliente VARCHAR(50),
    cnpj_restaurante_fk VARCHAR(14),
    FOREIGN KEY (cnpj_restaurante_fk) REFERENCES restaurante(cnpj_restaurante)
);

CREATE TABLE pagamento (
    id_pagamento INT PRIMARY KEY,
    cartao INT(1),  
    dinheiro INT(2)
);

CREATE TABLE pedido (
    id_pedido INT PRIMARY KEY,
    valor_total DECIMAL(10, 2),  
    id_cliente_fk INT,
    id_pagamento_fk INT,
    FOREIGN KEY (id_cliente_fk) REFERENCES clientes(id_cliente),
    FOREIGN KEY (id_pagamento_fk) REFERENCES pagamento(id_pagamento)
);

CREATE TABLE item_pedido (
    id_item INT PRIMARY KEY,
    nome_item VARCHAR(50),
    valor_item DECIMAL(10, 2),  
    id_pedido_fk INT,
    FOREIGN KEY (id_pedido_fk) REFERENCES pedido(id_pedido)
);
```

### COMANDOS INSERT
```sql
INSERT INTO restaurante(nome_restaurante, cnpj_restaurante) VALUES ("OUTBACK", 12345678912345);

INSERT INTO clientes (id_cliente, nome_cliente, cnpj_restaurante_fk) VALUES (1, "Pedro Lima", 12345678912345);

INSERT INTO clientes (id_cliente, nome_cliente, cnpj_restaurante_fk) VALUES (2, "Bianka Ibrahim", 12345678912345);

INSERT INTO clientes (id_cliente, nome_cliente, cnpj_restaurante_fk) VALUES (3, "George Monteiro", 12345678912345);

INSERT INTO fornecedores (cnpj_fornecedor, nome_fornecedor, cnpj_restaurante_fk) VALUES  (1234567891, "ambev", 12345678912345);

INSERT INTO fornecedores (cnpj_fornecedor, nome_fornecedor, cnpj_restaurante_fk) VALUES  (987654321, "vitarela", 12345678912345);

INSERT INTO funcionarios (prontuario, nome_funcionario, funcao, cnpj_restaurante_fk) VALUES (123, "Pedro Lima", "garçom", 12345678912345);

INSERT INTO funcionarios (prontuario, nome_funcionario, funcao, cnpj_restaurante_fk) VALUES  (321, "George Monteiro", "cozinheiro", 12345678912345);

INSERT INTO pagamento(id_pagamento, cartao, dinheiro) VALUES (123, 1, 2);

INSERT INTO pedido(id_pedido, valor_total, id_cliente_fk, id_pagamento_fk) VALUES  (1, 0.0, 1, 123);

INSERT INTO pedido(id_pedido, valor_total, id_cliente_fk, id_pagamento_fk) VALUES  (2, 0.0, 2, 123);

INSERT INTO item_pedido(id_item, nome_item, valor_item, id_pedido_fk) VALUES  (1, "lasanha", 10.00, 1);

INSERT INTO item_pedido(id_item, nome_item, valor_item, id_pedido_fk) VALUES  (2, "feijoada", 15.00, 2);

INSERT INTO item_pedido(id_item, nome_item, valor_item, id_pedido_fk) VALUES  (3, "coca", 8.00, 2);
```

### COMANDOS SELECT
```sql
SELECT nome_funcionario, funcao 
FROM funcionarios;


SELECT nome_cliente, nome_item, valor_item
FROM clientes
INNER JOIN item_pedido
ON id_pedido_fk = id_cliente;


SELECT cliente.nome_cliente,
    total.valor_total + SUM(valores.valor_item) AS valor_total
FROM
    clientes cliente
INNER JOIN
    pedido total ON cliente.id_cliente = total.id_cliente_fk
INNER JOIN
    item_pedido valores ON total.id_pedido = valores.id_pedido_fk
GROUP BY
    cliente.nome_cliente, total.valor_total;


SELECT nome_restaurante, prontuario, nome_funcionario, funcao
FROM restaurante
INNER JOIN funcionarios
ON cnpj_restaurante = cnpj_restaurante_fk;
```
### COMANDOS UPDATE
```sql
UPDATE funcionarios SET nome_funcionario = "Otavio Santos"
WHERE prontuario = 123

UPDATE item_pedido SET valor_item = 13.00
WHERE id_item = 1
```

### COMANDOS DELETE
```sql
DELETE FROM funcionarios WHERE prontuario = 123;

DELETE FROM item_pedido WHERE id_item = 1;
```

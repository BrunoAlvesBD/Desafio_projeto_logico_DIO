# Desafio_projeto_logico_DIO
-- Modelo relacional: 
Clientes:

idCliente (PK)
Primeiro nome
Sobrenome
CPF (único)
Endereço
Tipo
Veículos:

idVehicle (PK)
Marca
Modelo
Ano
Placa de carro
idCliente (FK)
Serviços:

idService (PK)
Nome do Serviço
Descrição
Preço
idVehicle (FK)
Peças:

idPart (PK)
Nome da peça
Quantidade de estoque
Peças de serviço:

idService (FK)
idPart (FK)
Quantidade
Pagamentos:

idPagamento (PK)
Tipo de pagamento
Quantia
idCliente (FK)
Funcionários:

idEmployee (PK)
Primeiro nome
Sobrenome
Cargo
Salário
Funcionários do serviço:

idService (FK)
idEmployee (FK)
-- ------------------------------------------------------------------------------------------



MODELO LÓGICO: 
-- Criação do banco de dados para o cenário de Oficina Mecânica
CREATE DATABASE oficina_mecanica;
USE oficina_mecanica;

-- Tabela cliente
CREATE TABLE Clients (
    idClient INT AUTO_INCREMENT PRIMARY KEY,
    FirstName VARCHAR(30),
    LastName VARCHAR(30),
    CPF CHAR(11) NOT NULL,
    Address VARCHAR(100),
    Type ENUM('Pessoa Física', 'Pessoa Jurídica') NOT NULL,
    UNIQUE (CPF)
);

ALTER TABLE Clients AUTO_INCREMENT = 1;

-- Tabela veículo
CREATE TABLE Vehicles (
    idVehicle INT AUTO_INCREMENT PRIMARY KEY,
    Brand VARCHAR(30),
    Model VARCHAR(30),
    Year INT,
    LicensePlate VARCHAR(10) NOT NULL,
    idClient INT,
    FOREIGN KEY (idClient) REFERENCES Clients(idClient) ON UPDATE CASCADE
);

-- Tabela serviço
CREATE TABLE Services (
    idService INT AUTO_INCREMENT PRIMARY KEY,
    ServiceName VARCHAR(100),
    Description VARCHAR(255),
    Price DECIMAL(10, 2) NOT NULL,
    idVehicle INT,
    FOREIGN KEY (idVehicle) REFERENCES Vehicles(idVehicle) ON UPDATE CASCADE
);

-- Tabela peças
CREATE TABLE Parts (
    idPart INT AUTO_INCREMENT PRIMARY KEY,
    PartName VARCHAR(50),
    StockQuantity INT DEFAULT 0
);

-- Tabela serviço_peças
CREATE TABLE ServiceParts (
    idService INT,
    idPart INT,
    Quantity INT DEFAULT 1,
    PRIMARY KEY (idService, idPart),
    FOREIGN KEY (idService) REFERENCES Services(idService) ON UPDATE CASCADE,
    FOREIGN KEY (idPart) REFERENCES Parts(idPart) ON UPDATE CASCADE
);

-- Tabela pagamento
CREATE TABLE Payments (
    idPayment INT AUTO_INCREMENT PRIMARY KEY,
    PaymentType ENUM('Dinheiro', 'Cartão de Crédito', 'Cartão de Débito', 'Transferência'),
    Amount DECIMAL(10, 2) NOT NULL,
    idClient INT,
    FOREIGN KEY (idClient) REFERENCES Clients(idClient) ON UPDATE CASCADE
);

-- Tabela funcionário
CREATE TABLE Employees (
    idEmployee INT AUTO_INCREMENT PRIMARY KEY,
    FirstName VARCHAR(30),
    LastName VARCHAR(30),
    JobTitle VARCHAR(50),
    Salary DECIMAL(10, 2) NOT NULL
);

-- Tabela serviço_funcionário
CREATE TABLE ServiceEmployees (
    idService INT,
    idEmployee INT,
    PRIMARY KEY (idService, idEmployee),
    FOREIGN KEY (idService) REFERENCES Services(idService) ON UPDATE CASCADE,
    FOREIGN KEY (idEmployee) REFERENCES Employees(idEmployee) ON UPDATE CASCADE
);

-- Inserir dados de clientes
INSERT INTO Clients (FirstName, LastName, CPF, Address, Type)
VALUES
    ('João', 'Silva', '12345678900', 'Rua A, 123', 'Pessoa Física'),
    ('Oficina do ABC', 'CNPJ 987654321', '9876543210001', 'Av. B, 456', 'Pessoa Jurídica');

-- Inserir dados de veículos
INSERT INTO Vehicles (Brand, Model, Year, LicensePlate, idClient)
VALUES
    ('Ford', 'Fiesta', 2018, 'ABC-1234', 1),
    ('Chevrolet', 'Cruze', 2020, 'XYZ-5678', 1);

-- Inserir dados de serviços
INSERT INTO Services (ServiceName, Description, Price, idVehicle)
VALUES
    ('Troca de óleo', 'Troca de óleo e filtro', 100.00, 1),
    ('Reparo do motor', 'Reparo no motor e sistema de escapamento', 350.00, 2);

-- Inserir dados de peças
INSERT INTO Parts (PartName, StockQuantity)
VALUES
    ('Filtro de óleo', 100),
    ('Vela de ignição', 50);

-- Inserir dados de serviço-peças
INSERT INTO ServiceParts (idService, idPart, Quantity)
VALUES
    (1, 1, 1),
    (2, 1, 2),
    (2, 2, 4);

-- Inserir dados de pagamento
INSERT INTO Payments (PaymentType, Amount, idClient)
VALUES
    ('Dinheiro', 100.00, 1),
    ('Cartão de Crédito', 350.00, 1);

-- Inserir dados de funcionários
INSERT INTO Employees (FirstName, LastName, JobTitle, Salary)
VALUES
    ('Pedro', 'Santos', 'Mecânico', 2000.00),
    ('Maria', 'Silva', 'Atendente', 1500.00);

-- Inserir dados de serviço-funcionários
INSERT INTO ServiceEmployees (idService, idEmployee)
VALUES
    (1, 1),
    (2, 1),
    (2, 2);

-- consultas
Quantos serviços foram realizados para cada veículo?
SELECT v.LicensePlate, COUNT(s.idService) AS TotalServices
FROM Vehicles v
LEFT JOIN Services s ON v.idVehicle = s.idVehicle
GROUP BY v.LicensePlate;

Qual é o valor total dos pagamentos feitos por cada cliente?
SELECT c.idClient, CONCAT(c.FirstName, ' ', c.LastName) AS ClientName, SUM(p.Amount) AS TotalPayments
FROM Clients c
LEFT JOIN Payments p ON c.idClient = p.idClient
GROUP BY c.idClient;

Quais funcionários realizaram quais serviços?
SELECT e.idEmployee, CONCAT(e.FirstName, ' ', e.LastName) AS EmployeeName, s.ServiceName
FROM Employees e
LEFT JOIN ServiceEmployees se ON e.idEmployee = se.idEmployee
LEFT JOIN Services s ON se.idService = s.idService;






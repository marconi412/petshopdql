select emp.nome "Nome empregado", 
	emp.cpf "CPF", 
	date_format(emp.dataAdm, "%d/%m/%Y")  "Admissão",
	concat("R$ ", format(emp.salario, 2, 'de_DE')) "Salario",  
	dep.nome "Departmaneto", tele.numero "Telefone"
from empregado emp
		inner join departamento dep on dep.idDepartamento = emp.Departamento_idDepartamento
        left join telefone tele on tele.Empregado_cpf = emp.cpf
			where dataAdm between '2019-01-01' and  '2022-03-31'
				order by dataAdm desc;

select emp.nome "Nome", 
	emp.cpf "CPF", 
	date_format(emp.dataAdm, "%d/%m/%Y")  "Admissão",
	concat("R$ ", format(emp.salario, 2, 'de_DE')) "Salario",  
	dep.nome "Departmaneto", tele.numero "Telefone"
from empregado emp
		inner join departamento dep on dep.idDepartamento = emp.Departamento_idDepartamento
        left join telefone tele on tele.Empregado_cpf = emp.cpf
			where salario < (select avg(salario) from empregado)
				order by emp.nome;
                               
select dep.nome "Departamento",
	concat('R$ ', format(avg(salario),2,'de_DE')) "Média Salarial",
    concat('R$ ', format(avg(comissao), 2, 'de_DE')) "Média de Comissão",
    count(emp.cpf) "Empregado por Departamento"
from departamento dep
		inner join empregado emp on emp.Departamento_idDepartamento = dep.idDepartamento			
				group by dep.idDepartamento
					order by dep.nome;
			
select emp.nome "Nome", 
	emp.cpf "CPF", 
	emp.sexo "Sexo",
	concat("R$ ", format(emp.salario, 2, 'de_DE')) "Salário",  
    concat("R$ ", format(sum(venda.idVenda -  coalesce(venda.desconto, 0)), 2, "de_DE")) "Valor Total Vendido",
	count(venda.idVenda) "Quantidade de Vendas",
    concat("R$ ", format(sum(venda.comissao), 2, "de_DE")) "Total de Comissão"
from empregado emp	
    left join venda on venda.Empregado_cpf = emp.cpf
		group by emp.cpf
			order by count(venda.idVenda) desc;

select emp.nome "Nome", 
	emp.cpf "CPF", 
	emp.sexo "Sexo",
	concat("R$ ", format(emp.salario, 2, 'de_DE')) "Salário",  
	concat("R$ ", format(sum(serv.valorVenda -  coalesce(isv.desconto, 0)), 2, "de_DE")) "Valor Total vendas Serviço",
	count(serv.idServico) "Qntd de Vendas Serviços",
    concat("R$ ", format(sum(vnd.comissao), 2, "de_DE")) "Total de Comissão"
from empregado emp
	left join itensservico isv on isv.Empregado_cpf = emp.cpf
    inner join servico serv on serv.idServico = isv.Servico_idServico
    inner join venda vnd on vnd.idVenda = isv.Venda_idVenda
		group by emp.cpf
			order by count(serv.idServico) desc;
    
select pet.nome "Pet",
	date_format(vnd.data , "%d/%m/%Y") "Data Serviço",
    serv.nome "Nome Serviço",
    isv.quantidade "Quantidade",
    concat('R$ ', format(isv.valor, 2, 'de_DE')) "Valor Serviço",
    emp.nome "Funcionário Responsável"
	from pet
		inner join itensservico isv on isv.pet_idPet = pet.idPet
		inner join venda vnd on isv.venda_idVenda = vnd.idVenda
		inner join empregado emp on emp.cpf = isv.empregado_cpf
        inner join servico serv on isv.servico_idServico = serv.idServico
			group by serv.idServico
				order by vnd.data desc;
 
select date_format(vend.data , "%d/%m/%Y") "Data da Venda", 
concat("R$ ", format(vend.valor, 2, 'de_DE')) "Valor da venda", 
concat("R$ ", format(vend.desconto, 2, 'de_DE')) "Desconto da venda", 
concat("R$ ", format(sum(vend.valor -  coalesce(vend.desconto, 0)), 2, "de_DE")) "Valor final da venda",
emp.nome "Funcionário que fez a venda"
	from venda vend
		inner join empregado emp on emp.cpf = vend.empregado_cpf
			group by vend.idVenda
			order by date_format(vend.data , "%d/%m/%Y") desc;
            
select serv.nome "NomeServiço", 
count(isv.quantidade) "Qntd de vendas", 
concat("R$ ", format(sum(serv.valorVenda), 2, "de_DE")) "Valor total Vendido"
	from servico serv
		inner join itensservico isv on isv.servico_idServico = serv.idServico        
			group by serv.idServico
				order by sum(serv.valorVenda)
					limit 10;

select
    fpv.tipo "formas de Pagamento",
    count(fpv.Venda_idVenda) "Quantidade_Vendas",
    concat("R$ ", replace(format(sum(vend.valor), 2), '.', ',')) "Total valor vendido"
	from formapgvenda fpv
		inner join venda vend on fpv.venda_idVenda = vend.idVenda
			group by fpv.tipo
				order by count(fpv.Venda_idVenda)  desc;

select date_format(vend.data , "%d/%m/%Y") "Data da Venda",
count(vend.idVenda) "Quantidade de vendas",
concat("R$ ", format(sum(vend.valor), 2, "de_DE")) "Valor Total Vendido"
	from venda vend
		group by date_format(vend.data , "%d/%m/%Y")
			order by date_format(vend.data , "%d/%m/%Y")
            
select 
    p.nome_produto AS "Nome Produto", 
    p.valor_produto AS "Valor Produto", 
    p.categoria_produto AS "Categoria Produto", 
    f.nome_fornecedor AS "Nome Fornecedor", 
    f.email_fornecedor AS "Email Fornecedor", 
    f.telefone_fornecedor AS "Telefone Fornecedor"
FROM 
    produtos p
JOIN 
    fornecedores f ON p.fornecedor_id = f.id
ORDER BY 
    p.nome_produto ASC;  
            
select 
    p.nome_produto AS "Nome Produto", 
    COUNT(v.id) AS "Qntd de Vendas", 
    SUM(v.valor_venda) AS "Valor Total Recebido pela Venda"
FROM 
    produtos p
JOIN 
    vendas v ON p.id = v.produto_id
GROUP BY 
    p.nome_produto
ORDER BY 
    COUNT(v.id) DESC;
